# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  #------------------------------------------------------------
  #------ Creating VM: a loadbalancer with haproxy installed
  #------------------------------------------------------------
  config.vm.define "nagios" do |nagios|
    nagios.vm.box = "bento/ubuntu-18.04"
    nagios.vm.hostname = 'nagios'
    # Assigning IP:
    nagios.vm.network "private_network", ip: "10.10.10.40"
    #------ Using Virtualbox provider for the exercise
    nagios.vm.provider "virtualbox" do |vb|
      vb.gui = false
      vb.customize ["modifyvm", :id, "--name", "nagios"]  
      # Customize the amount of memory on the VM:
      vb.memory = "512"      
    end
    #------ Provisioning through Ansible
    nagios.vm.provision "shell", inline: <<-SHELL
      apt-get update
      echo "****** Installing Pre-reqs *******"
      apt-get install -y autoconf gcc libc6 make wget unzip apache2 php libapache2-mod-php7.2 libgd-dev
      echo "****** Downloading source files *******"
      cd /tmp/
      wget -O nagioscore.tar.gz https://github.com/NagiosEnterprises/nagioscore/archive/nagios-4.4.3.tar.gz
      tar xzf nagioscore.tar.gz
      echo "****** Preparing Installation *******"
      cd nagioscore-nagios-4.4.3/
      sudo ./configure --with-httpd-conf=/etc/apache2/sites-enabled/
      sudo make all
      echo "****** Configuring user and group ******"
      sudo make install-groups-users
      sudo usermod -a -G nagios www-data
      echo "****** Installation continues *********"
      sudo make install && sudo make install-daemoninit && sudo make install-commandmode
      sudo make install-config && sudo make install-webconf
      echo "****** Post Install config ********"
      sudo a2enmod rewrite && sudo a2enmod cgi
      sudo ufw allow Apache && sudo ufw reload
      sudo htpasswd -b -c /usr/local/nagios/etc/htpasswd.users nagiosadmin nagiosadmin
      sudo systemctl restart apache2.service
      sudo systemctl start nagios.service
    SHELL
  end
  #-----------------------------------------------------------
  #------ Creating VM: a webserver Web1
  #-----------------------------------------------------------
  config.vm.define "web1" do |web1|
    web1.vm.box = "bento/ubuntu-18.04"
    web1.vm.hostname = 'web1'
    # Assigning IP:
    web1.vm.network "private_network", ip: "10.10.10.41"
    #------ Using Virtualbox provider for the exercise
    web1.vm.provider "virtualbox" do |vb|
      vb.gui = false
      vb.customize ["modifyvm", :id, "--name", "web1"]
      # Customize the amount of memory on the VM:
      vb.memory = "512"
    end
    #------ Provisioning via shell. apache2 setup
    web1.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y apache2
      sudo mv /var/www/html /var/www/_html
      sudo ln -s /vagrant/web1 /var/www/html
    SHELL
  end
  #-----------------------------------------------------------
  #------ Creating VM: a webserver Web2
  #-----------------------------------------------------------
  config.vm.define "web2" do |web2|
    web2.vm.box = "bento/ubuntu-18.04"
    web2.vm.hostname = 'web2'
    # Assigning IP:
    web2.vm.network "private_network", ip: "10.10.10.42"
    #------ Using Virtualbox provider for the exercise
    web2.vm.provider "virtualbox" do |vb|
      vb.gui = false
      vb.customize ["modifyvm", :id, "--name", "web2"]
      # Customize the amount of memory on the VM:
      vb.memory = "512"
    end
    #------ Provisioning through Ansible as per guidelines in exercise
    web2.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y apache2
      sudo mv /var/www/html /var/www/_html
      sudo ln -s /vagrant/web2 /var/www/html
    SHELL
  end

end

