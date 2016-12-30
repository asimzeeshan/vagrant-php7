# -*- mode: ruby -*-
# vi: set ft=ruby :

# check and install required Vagrant plugins
required_plugins = ["vagrant-hostmanager", "vagrant-vbguest", "vagrant-cachier", "vagrant-address", "vagrant-remove-old-box-versions"]
required_plugins.each do |plugin|
  if Vagrant.has_plugin?(plugin) then
      #system "echo OK: #{plugin} already installed"
  else
      system "echo Not installed required plugin: #{plugin} ..."
    system "vagrant plugin install #{plugin}"
  end
end

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "ubuntu/xenial64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  config.vm.box_check_update = true

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network "private_network", ip: "192.168.22.11"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

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
    vb.gui = false

    # Customize the VM parameters
    vb.customize ["modifyvm", :id, "--ostype", "Debian_64"]
    vb.customize ["modifyvm", :id, "--cpuexecutioncap", "50"]
    vb.customize ["modifyvm", :id, "--cpus", "2" ]
  
    # Customize the VM name:
    vb.name = "php7dev (xenial64)"

    # Customize the amount of memory on the VM:
    vb.memory = "1024"

    # Customize the number of vCPUs on the VM:
    vb.cpus = 2
  end

  # Set entries in hosts file
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true
  config.hostmanager.aliases =  ["devbox.local","admin.php7.local","phpmyadmin.php7.local","mailcatcher.php7.local"]
  
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL

  # Begin Configuring
  config.vm.define "php7dev" do|php7dev|
    php7dev.vm.hostname = "php7.local" # Setting up hostname

    php7dev.vm.provision "shell", inline: "export DEBIAN_FRONTEND=noninteractive"
    php7dev.vm.provision "file", source: "~/.ssh/id_rsa.pub", destination: "~/.ssh/me.pub"
    #php7dev.vm.provision "shell", inline: "cat ~vagrant/.ssh/me.pub >> ~vagrant/.ssh/authorized_keys"
    #php7dev.vm.provision "shell", inline: "rm -fr ~vagrant/.ssh/me.pub"

    php7dev.vm.provision "shell", inline: <<-SHELL
      export DEBIAN_FRONTEND=noninteractive
      sudo apt-get clean all
      sudo apt-get update
      sudo apt-get install -y htop iotop iftop apache2 php php-cli php-mcrypt php-curl curl libapache2-mod-php
      sudo apt-get install -y build-essential software-properties-common vim curl wget tmux ruby2.3 ruby2.3-dev libsqlite3-dev curl git mailutils
    SHELL

    php7dev.vm.provision "file", source: "sample.mysql.list", destination: "~/mysql.list"
    php7dev.vm.provision "shell", inline: "sudo mv /home/ubuntu/mysql.list /etc/apt/sources.list.d/mysql.list"

    php7dev.vm.provision "shell", inline: <<-SHELL
      export DEBIAN_FRONTEND=noninteractive
      ROOT_PASSWORD="z29t45g3"

      echo "mysql-community-server mysql-community-server/root-pass password $ROOT_PASSWORD" | sudo debconf-set-selections
      echo "mysql-community-server mysql-community-server/re-root-pass password $ROOT_PASSWORD" | sudo debconf-set-selections
      echo "mysql-community-server mysql-community-server/remove-data-dir boolean false" | sudo debconf-set-selections
      echo "mysql-community-server mysql-community-server/data-dir note" | sudo debconf-set-selections
       
      export DEBIAN_FRONTEND=noninteractive
      wget http://dev.mysql.com/get/mysql-apt-config_0.8.1-1_all.deb
      sudo apt-key adv --recv-keys --keyserver keyserver.ubuntu.com 8C718D3B5072E1F5
      sudo apt-get update
      sudo apt-get --yes --force-yes install mysql-server
      sudo apt-get -f install

      echo "phpmyadmin phpmyadmin/internal/skip-preseed boolean true" | debconf-set-selections
      echo "phpmyadmin phpmyadmin/reconfigure-webserver multiselect" | debconf-set-selections
      echo "phpmyadmin phpmyadmin/dbconfig-install boolean false" | debconf-set-selections
      sudo apt-get --yes --force-yes install phpmyadmin
    SHELL

    # copy and enable phpmyadmin.php7.local vhost
    php7dev.vm.provision "file", source: "sample.phpmyadmin.conf", destination: "~/phpmyadmin.php7.local.conf"
    php7dev.vm.provision "shell", inline: "sudo mv /home/ubuntu/phpmyadmin.php7.local.conf /etc/apache2/sites-available/phpmyadmin.php7.local.conf"
    php7dev.vm.provision "shell", inline: "sudo a2ensite phpmyadmin.php7.local"

    # copy and enable mailcatcher.php7.local vhost
    php7dev.vm.provision "file", source: "sample.mailcatcher.conf", destination: "~/mailcatcher.php7.local.conf"
    php7dev.vm.provision "shell", inline: "sudo mv /home/ubuntu/mailcatcher.php7.local.conf /etc/apache2/sites-available/mailcatcher.php7.local.conf"
    php7dev.vm.provision "shell", inline: "sudo a2ensite mailcatcher.php7.local"
    
    php7dev.vm.provision "shell", inline: "curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer"

    php7dev.vm.provision "shell", inline: "sudo gem install mailcatcher"
    php7dev.vm.provision "shell", inline: '/usr/bin/env mailcatcher --ip=0.0.0.0'

  end
end
