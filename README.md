# CogatPheno-VM: A virtual machine to deploy Cognitive Phenotype Tagging

[CogatPheno](http://www.github.com/vsoch/CogatPheno) is a Django application and database that allows for sharing of assessments, and tagging with behavioral concepts. This virtual machine deploys the Django Application to an Amazon instance.

## Setting up the virtual machine

1. Install [VirtualBox] (https://www.virtualbox.org/wiki/Downloads)

2. Install [Vagrant] (http://www.vagrantup.com/downloads) - Vagrant is a provisioning system that sets up the virtual machine.

3. If you don't already have it, install [git] (https://git-scm.com/downloads)

4.  cd to the directory where you want to house the project, and then clone the myconnectome vagrant setup:
`git clone https://github.com/vsoch/cogatpheno-vm.git`

5. cd into the vm directory: `cd cogatpheno-vm`

6. set up the vagrant VM (which may take a little while):
`vagrant up`

7.  Step 6 will automatically deploy the Django application to the configuration on Amazon that you have specified.

## Log in with SH

If you wish to log into the virtual machine and programatically access the application, you can do so with:

      vagrant ssh


## Setting up the virtual machine on aws

Make sure you have the [most up-to-date version of vagrant](https://www.vagrantup.com/downloads), and install vagrant-aws. If you do not, you will see this error (version 1.6.5)

       vagrant-share can't be installed without vagrant login (RuntimeError)
 
After updating:

      vagrant --version
      Vagrant 1.7.2

Then you should install vagrant-aws, which will allow you to provision the Amazon machine.

      sudo vagrant plugin install vagrant-aws
      Installing the 'vagrant-aws' plugin. This can take a few minutes...
      Installed the plugin 'vagrant-aws (0.6.0)'!

You then need to add an aws compatible box. I found this box on the vagrant-aws plugin github repository:

      vagrant box add aws https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box

The vagrantfile included in this repo has instructions to provision the machine on Amazon using this box. Then to launch the instance:

      vagrant up --provider=aws
