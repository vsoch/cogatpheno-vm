# this was adapted from myconnectome-vm Vagrantfile

VAGRANTFILE_API_VERSION = "2"

$script = <<SCRIPT

if [ ! -d $HOME/miniconda ]
then
 # install anaconda
 wget http://repo.continuum.io/miniconda/Miniconda-latest-Linux-x86_64.sh -O miniconda.sh
 chmod +x miniconda.sh
 ./miniconda.sh -b
 echo "export PATH=$HOME/miniconda/bin:\\$PATH" >> .bashrc
 echo "export PATH=$HOME/miniconda/bin:\\$PATH" >> .env
fi

# install python packages
$HOME/miniconda/bin/conda update --yes conda
$HOME/miniconda/bin/pip install setuptools
$HOME/miniconda/bin/conda install --yes pip numpy scipy nose traits networkx
$HOME/miniconda/bin/conda install --yes dateutil ipython-notebook matplotlib
$HOME/miniconda/bin/conda install --yes statsmodels boto  pandas scikit-learn

sudo apt-get update > /dev/null
sudo apt-get install -y --force-yes git 
sudo apt-get install -y --force-yes build-essential
sudo apt-get install -y --force-yes nginx

# Install postgresql
sudo apt-get install -y --force-yes postgresql postgresql-contrib
sudo apt-get install -y --force-yes pgadmin3
sudo apt-get install -y --force-yes libpq-dev

if [ ! -d $HOME/CogatPheno ]
then
  git clone https://github.com/vsoch/CogatPheno.git $HOME/CogatPheno
  cd $HOME/CogatPheno
  $HOME/miniconda/bin/pip install -r requirements.txt  
  $HOME/miniconda/bin/pip install uwsgi 
fi

sudo apt-get update
# Run script to update database, install nltk
$HOME/miniconda/bin/pip install psycopg2
$HOME/miniconda/bin/python $HOME/CogatPheno/scripts/setup_nltk.py

# Set up nginx and web server in emperor mode
uwsgi --socket $HOME/CogatPheno/cogpheno/cogpheno.sock --module $HOME/CogatPheno/cogpheno/cogpheno.wsgi --chmod-socket=666
sudo ln -s $HOME/CogatPheno/config/cogpheno_nginx.conf /etc/nginx/sites-enabled/
sudo mkdir /etc/uwsgi
sudo mkdir /etc/uwsgi/vassals
sudo mkdir /var/log/uwsgi
# symlink from the default config directory to your config file
sudo ln -s $HOME/CogatPheno/config/uwsgi.ini /etc/uwsgi/vassals/
# ensure it restarts with the server
sudo mv $HOME/CogatPheno/config/cogpheno.rc.local /etc/rc.local
sudo /etc/init.d/nginx start
# run the emperor
sudo $HOME/miniconda/bin/uwsgi --emperor /etc/uwsgi/vassals --uid www-data --gid www-data &

SCRIPT

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.ssh.forward_x11 = true

  config.vm.define :engine do |engine_config|
      engine_config.vm.box = "aws"
      #engine_config.vm.box_url = "https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box"
      engine_config.vm.hostname = 'cogatpheno-vm'

    engine_config.vm.provider :aws do |aws, override|
        aws.access_key_id = "AWS_ACCES_KEY_ID"
        aws.secret_access_key = "AWS_SECRET_ACCESS_KEY"
        aws.keypair_name = "AWS_KEYPAIR_NAME"
        aws.region = "us-west-2" 
        aws.ami = "ami-5189a661"
        aws.instance_type = "t2.small"
        aws.security_groups = "AWS_SECURITY_GROUP"
        aws.iam_instance_profile_name = "IAM_INSTANCE_PROFILE_NAME"
        aws.elastic_ip = "54.149.190.168"
        override.ssh.username = "ubuntu"
        override.ssh.private_key_path = "AWS_PRIVATE_KEY_PATH"
        override.nfs.functional = false
    end
        engine_config.vm.provision "shell", :privileged => false, inline: $script
  end
end
