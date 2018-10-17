# -*- mode: ruby -*-
# vi: set ft=ruby :

# CustomInstaller to fix the installation of VirtualBox Guest Additions
# https://github.com/dotless-de/vagrant-vbguest/issues/301#issuecomment-408161379
class CustomInstaller < VagrantVbguest::Installers::RedHat
  def install(opts = nil, &block)
    communicate.sudo('yum -y update kernel', opts, &block)
    super
  end
end

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure('2') do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.

  config.vm.box = 'centos/6'
  config.vm.box_version = '1804.02'
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
  # config.vm.network 'forwarded_port',
  #                  guest: 80, host: 8080,
  #                  host_ip: '127.0.0.1'

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
  config.vm.provider 'virtualbox' do |vb|
    # Display the VirtualBox GUI when booting the machine
    vb.gui = true
    vb.memory = 4096
    vb.cpus = 2
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision 'shell', inline: <<-SHELL

    # General update of system
    sudo yum update
    sudo yum upgrade

    su vagrant

    # Use EPEL: https://fedoraproject.org/wiki/EPEL
    wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
    sudo rpm -ivh epel-release-6-8.noarch.rpm
    rm epel-release-6-8.noarch.rpm

    # Install GUI desktop
    sudo yum groupinstall -y "X Window System" "Desktop"
    sudo yum install -y gnome-core xfce4 xorg-x11-fonts
    sudo echo "id:5:initdefault:" > /etc/inittab

    # Fix fonts problem in terminal
    # https://forums.anandtech.com/threads/fonts-screwed-up-in-centos-6-terminal.2186468/
    sudo yum -y install terminus-fonts terminus-fonts-console

    # Install newer git version
    sudo yum install -y curl-devel expat-devel libcurl-devel perl-devel asciidoc \
      xmlto xz zlib-devel zlib-static gettext
    curl -kOL https://www.kernel.org/pub/software/scm/git/git-2.18.0.tar.xz
    tar xvf git-2.18.0.tar.xz
    cd git-2.18.0
    make configure
    ./configure --prefix=/usr
    make all
    sudo make install install-doc install-html
    cd /home/vagrant
    rm -rf git-2.18.0 git-2.18.0.tar.xz

    # Install python2.7
    cd /home/vagrant
    sudo yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel
    sudo yum -y install readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel python-devel
    wget http://www.python.org/ftp/python/2.7.15/Python-2.7.15.tar.xz
    tar -xf Python-2.7.15.tar.xz
    cd Python-2.7.15
    ./configure --prefix=/usr/local --enable-unicode=ucs4 --enable-shared LDFLAGS="-Wl,-rpath /usr/local/lib"
    make
    sudo make altinstall
    rm /home/vagrant/Python-2.7.15.tar.xz
    rm -rf /home/vagrant/Python-2.7.15

    # Install pip
    cd /home/vagrant
    export PATH="/usr/local/bin:$PATH"
    wget https://bootstrap.pypa.io/ez_setup.py
    sudo /usr/local/bin/python2.7 ez_setup.py
    rm ez_setup.py
    wget https://bootstrap.pypa.io/get-pip.py
    sudo /usr/local/bin/python2.7 get-pip.py
    rm get-pip.py

    # Install virtualenv
    sudo /usr/local/bin/pip2.7 install virtualenv

    # Install RabbitMQ server (used by genomics-workspace)
    sudo yum -y install erlang  # prerequisite of rabbitMQ
    sudo yum -y install rabbitmq-server
    sudo chkconfig rabbitmq-server on  # to start the daemon by default when system boots run:
    sudo /sbin/service rabbitmq-server start

    # Install and activate memcached (used by genomics-workspace)
    sudo yum -y install memcached
    sudo chkconfig memcached on  # set to start at boot time

    # Install the PostgreSQL (9.5)
    echo 'exclude=postgresql*' | sudo tee -a /etc/yum.repos.d/CentOS-Base.repo  # add line to yum repository
    sudo yum -y install http://yum.postgresql.org/9.5/redhat/rhel-6-x86_64/pgdg-centos95-9.5-2.noarch.rpm  # install the PostgreSQL Global Development Group (PGDG) RPM file:
    sudo yum -y install postgresql95-server postgresql95-contrib postgresql95-devel
    sudo service postgresql-9.5 initdb  # initialize (uses default data directory: /var/lib/pgsql)
    sudo chkconfig postgresql-9.5 on  # make PostgreSQL startup at boot:
    sudo sed '82,84s/ident/md5/g' /var/lib/pgsql/9.5/data/pg_hba.conf
    export PATH=/usr/pgsql-9.5/bin:$PATH
    sudo service postgresql-9.5 restart

    # Install samtools
    cd /home/vagrant
    sudo yum install --enablerepo=epel -y ncurses-devel zlib-devel bzip2-devel xz-devel
    wget https://github.com/samtools/samtools/releases/download/1.9/samtools-1.9.tar.bz2
    tar xvjf samtools-1.9.tar.bz2
    cd samtools-1.9
    sudo ./configure --prefix=/usr/local
    sudo make
    sudo make install
    cd /home/vagrant
    rm -rf samtools-1.9.tar.bz2 samtools-1.9

    # Install glibc 2.17 (dependency of wig2BigWig)
    # https://gist.github.com/harv/f86690fcad94f655906ee9e37c85b174#gistcomment-2083385
    SERVER=http://copr-be.cloud.fedoraproject.org/results/mosquito/myrepo-el6/
    REPO64=epel-6-x86_64
    VERSION=glibc-2.17-55.fc20
    SERVER64=$SERVER/$REPO64/$VERSION
    sudo rpm -Uvh --force --nodeps $SERVER64/glibc-2.17-55.el6.x86_64.rpm $SERVER64/glibc-common-2.17-55.el6.x86_64.rpm $SERVER64/glibc-devel-2.17-55.el6.x86_64.rpm $SERVER64/glibc-headers-2.17-55.el6.x86_64.rpm $SERVER64/glibc-static-2.17-55.el6.x86_64.rpm

    # Install zlib 1.2.7 (dependency of wig2BigWig)
    sudo rpm -Uvh --force --nodeps https://rpmfind.net/linux/centos/7.5.1804/os/x86_64/Packages/zlib-1.2.7-17.el7.x86_64.rpm

    # Download wig2BigWig
    sudo wget http://hgdownload.cse.ucsc.edu/admin/exe/linux.x86_64/wigToBigWig -O /usr/local/bin/wigToBigWig
    sudo chmod +x /usr/local/bin/wigToBigWig
    wget https://github.com/GMOD/jbrowse/tree/master/bin

    # Install gff3tool
    sudo /usr/local/bin/pip2.7 install gff3tool

    # Install coordinates conversion
    sudo /usr/local/bin/pip2.7 install git+https://github.com/NAL-i5K/coordinates_conversion.git

    # Install genomics-workspace
    cd /home/vagrant
    git clone https://github.com/NAL-i5K/genomics-workspace
    cd /home/vagrant/genomics-workspace
    virtualenv -p python2.7 py2.7
    source py2.7/bin/activate
    pip install -r requirements.txt
    deactivate

    # Clone other tools used
    cd /home/vagrant
    git clone https://github.com/NAL-i5K/wiggle-tools
    git clone https://github.com/NAL-i5K/bam_to_bigwig
    git clone https://gitlab.com/i5k_Workspace/apollo2_data_build_scripts.git
   	git clone https://github.com/GMOD/jbrowse

    # Create directories used
    mkdir -p /app/data/blat
    mkdir -p /app/data/other-species
    mkdir -p /app/data/scratch
    mkdir -p /app/data/working-files

    # Fix the permission of the folders
    sudo chown -R vagrant /app
    sudo chown -R vagrant wiggle-tools
    sudo chown -R vagrant genomics-workspace
    sudo chown -R vagrant bam_to_bigwig
    sudo chown -R vagrant apollo2_data_build_scripts
    sudo chown -R vagrant jbrowse

    reboot # reboot to load GUI
  SHELL
end
