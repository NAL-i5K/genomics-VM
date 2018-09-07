# -*- mode: ruby -*-
# vi: set ft=ruby :

# CustomInstaller to fix the installation of VirtualBox Guest Additions
# https://github.com/dotless-de/vagrant-vbguest/issues/301#issuecomment-408161379
class CustomInstaller < VagrantVbguest::Installers::RedHat
  def install(opts=nil, &block)
    communicate.sudo('yum -y update kernel', opts, &block)
    super
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
  # boxes at https://vagrantcloud.com/search.

  config.vm.box = "centos/6"
  config.vm.box_version = "1804.02"
  config.vbguest.installer = CustomInstaller

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
  # config.vm.synced_folder ".", "/vagrant", type: "virtualbox"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    vb.gui = true
    vb.memory = 2048
    vb.cpus = 2
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
    # use EPEL: https://fedoraproject.org/wiki/EPEL
    wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
    sudo rpm -ivh epel-release-7-8.noarch.rpm
    rm epel-release-7-8.noarch.rpm
    # general update of system
    sudo yum update
    # install GUI desktop
    sudo yum groupinstall -y "X Window System" "Desktop"
    sudo yum install -y gnome-core xfce4 xorg-x11-fonts
    sudo echo "id:5:initdefault:" > /etc/inittab
    # fix fonts problem in terminal
    # https://forums.anandtech.com/threads/fonts-screwed-up-in-centos-6-terminal.2186468/
    sudo yum -y install terminus-fonts terminus-fonts-console
    # install newer git version
    sudo yum install -y curl-devel expat-devel libcurl-devel perl-devel asciidoc xmlto xz zlib-devel gettext
    curl -kOL https://www.kernel.org/pub/software/scm/git/git-2.4.4.tar.xz
    tar xvf git-2.4.4.tar.xz
    cd git-2.4.4
    make configure
    ./configure --prefix=/usr
    make all
    sudo make install install-doc install-html
    cd ..
    rm -rf git-2.4.4 git-2.4.4.tar.xz
    reboot # reboot to load GUI
  SHELL
end

