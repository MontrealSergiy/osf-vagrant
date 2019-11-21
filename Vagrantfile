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
  config.vm.box = "certiprosolutions/ubuntu-docker"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  config.vm.network "forwarded_port", guest: 5000, host: 5000
  config.vm.network "forwarded_port", guest: 8001, host: 8001
  config.vm.network "forwarded_port", guest: 7007, host: 7007
  config.vm.network "forwarded_port", guest: 7778, host: 7778
  config.vm.network "forwarded_port", guest: 8000, host: 8000
  config.vm.network "forwarded_port", guest: 9200, host: 9200
  config.vm.network "forwarded_port", guest: 2001, host: 2002
  config.vm.network "forwarded_port", guest: 5432, host: 54320
  config.vm.network "forwarded_port", guest: 7777, host: 7777
  config.vm.network "forwarded_port", guest: 4201, host: 4201

  config.vm.network "forwarded_port", guest: 41953, host: 41953

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

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
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
     vb.memory = "8000"
     vb.cpus = 2
   end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
 config.vm.provision "shell", inline: <<-SHELL
    # apt-get update
    # apt-get install -y apache2
    # apk add git
    apt-get update && apt-get install -y git #python-pip
    # apt-get install python-pip -y 
    git clone https://github.com/CenterForOpenScience/osf.io.git 
    cd osf.io
    # rm *.lock -f
    # git checkout master
    # pip install pre-commit
    ifconfig lo:0 192.168.168.167 netmask 255.255.255.255 up
    # do manually
    echo fs.inotify.max_user_watches=131072 |  tee -a /etc/sysctl.conf
    sysctl -p   
    cp ./website/settings/local-dist.py ./website/settings/local.py
    cp ./api/base/settings/local-dist.py ./api/base/settings/local.py
    cp ./docker-compose-dist.override.yml ./docker-compose.override.yml
    cp ./tasks/local-dist.py ./tasks/local.py
    docker-compose up requirements mfr_requirements wb_requirements
    sleep 20m
    docker-compose up -d elasticsearch postgres mongo rabbitmq
    rm -Rf ../node_modules
    sleep 10m
    docker-compose up -d assets
    sleep 25m
    docker-compose up -d admin_assets
    sleep 20m
    docker-compose up -d mfr wb fakecas sharejs
    sleep 10m
    # might loop forewer ...
    until [[ `docker-compose exec postgres psql -c 'select 1' -U postgres` == *1* ]] do sleep 2m; echo 'waiting'; done
    docker-compose run --rm web python -m scripts.populate_institutions -e test -a
    docker-compose run --rm web python manage.py populate_fake_providers
    docker-compose run --rm web python -m scripts.parse_citation_styles
    docker-compose up -d worker web api admin preprints registries ember_osf_web 
  SHELL
end
