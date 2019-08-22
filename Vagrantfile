# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "centos/7"
  config.vm.hostname = 'node1.example.com'

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false  

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  #config.vm.network "forwarded_port", guest: 8443, host: 8880, host_ip: "127.0.0.1"
  config.vm.network "forwarded_port", guest: 8443, host: 8443, host_ip: "127.0.0.1"
  # auto_correct = true


  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"
  # config.vm.network "private_network", type: "dhcp"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"
  config.vm.network "public_network", use_dhcp_assigned_default_route: true, bridge: "bridge100"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    # vb.gui = true

   # Customize the amount of memory on the VM:
    vb.memory = 8192
    vb.cpus = 4  
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
    sudo groupadd docker 
    sudo usermod -aG docker vagrant

    sudo yum -y update

    sudo yum install git epel-release ansible pyOpenSSL python-cryptography python-lxml docker -y
    sudo yum install wget -y

    # update python
    sudo yum install gcc openssl-devel bzip2-devel -y
    wget https://www.python.org/ftp/python/2.7.16/Python-2.7.16.tgz
    tar xzf Python-2.7.16.tgz
    cd Python-2.7.16
    ./configure --enable-optimizations
    sudo make altinstall
    /usr/local/bin/python2.7 -V

    # install pip
    curl "https://bootstrap.pypa.io/get-pip.py" -o "get-pip.py"
    sudo python get-pip.py

    # upgrade ansible
    sudo pip install --upgrade ansible

    # ensure docker started on reboot
    sudo systemctl enable docker

    # start docker
    sudo systemctl start docker

    # fix error due to docker not supported for overlay2
    sudo sed -i 's/overlay2/devicemapper/g' /etc/sysconfig/docker-storage && cat /etc/sysconfig/docker-storage
    sudo sed -i 's/overlay2/devicemapper/g' /etc/sysconfig/docker-storage-setup && cat /etc/sysconfig/docker-storage-setup

    # setup insecure docker login
    echo '{ "insecure-registries": ["172.30.0.0/16" ] }' | sudo tee  /etc/docker/daemon.json 

    # restart docker after using devicemapper
    sudo systemctl restart docker

    # update and cleanup
    sudo yum update -y

    # Download the Linux oc binary
    wget https://github.com/openshift/origin/releases/download/v3.11.0/openshift-origin-client-tools-v3.11.0-0cbc58b-linux-64bit.tar.gz
    tar xvf openshift-origin-client-tools*.tar.gz
    cd openshift-origin-client*/ && sudo mv oc kubectl  /usr/local/bin/

    /usr/local/bin/oc cluster up

  SHELL
  # config.vm.provision :shell, path: "bootstrap.sh"  
end
