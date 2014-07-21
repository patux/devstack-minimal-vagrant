# -*- mode: ruby -*-
# vi: set ft=ruby :

$script = <<SCRIPT
apt-get update
apt-get install -y git python-dev build-essential git-review curl vim wget
cat << EOF > install_devstack.sh
#!/bin/bash
cd /home/vagrant
git clone https://github.com/openstack-dev/devstack.git
[ ! -d devstack/files ] && mkdir -p devstack/files
[ -f /vagrant/cirros-0.3.2-x86_64-uec.tar.gz ] && cp /vagrant/cirros-0.3.2-x86_64-uec.tar.gz devstack/files
[ -f /vagrant/Fedora-x86_64-20-20140618-sda.qcow2 ] && cp /vagrant/Fedora-x86_64-20-20140618-sda.qcow2 devstack/files
cat << EOC > devstack/local.conf
[[local|localrc]]
STACK_USER=vagrant
ADMIN_PASSWORD=secrete
DATABASE_PASSWORD=secrete
RABBIT_PASSWORD=secrete
SERVICE_PASSWORD=secrete
SERVICE_TOKEN=a682f596-76f3-11e3-b3b2-e716f9080d50

GUEST_INTERFACE_DEFAULT=eth1
HOST_IP_IFACE=eth1

[[post-config|$NOVA_CONF]]
[DEFAULT]
flat_interface = eth1
vlan_interface = eth1
EOC
cd devstack
git pull origin master
./stack.sh
EOF
chmod +x /home/vagrant/install_devstack.sh
su - vagrant -c '/home/vagrant/install_devstack.sh'
SCRIPT

macaddress = '0800274a508d'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.box = "trusty64"
  config.vm.box_url= "https://vagrantcloud.com/ubuntu/trusty64/version/1/provider/virtualbox.box"
  config.vm.provision "shell", inline: $script, keep_color: false
  config.vm.network :private_network, ip: '10.10.10.40'
  config.vm.network :forwarded_port, guest: 80, host: 8080
  config.vm.network :forwarded_port, guest: 22, host: 8022
  #config.vm.network "public_network", bridge: 'en0: Wi-Fi (AirPort)', :use_dhcp_assigned_default_route => true
  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--memory", "4096" ]
    vb.customize ["modifyvm", :id, "--cpus", "4"]
    # you need this for openstack guests to talk to each other
    vb.customize ["modifyvm", :id, "--nicpromisc2", "allow-all"]
    # allows for stable mac addresses
    vb.customize ["modifyvm", :id, "--macaddress2", macaddress]
  end

end
