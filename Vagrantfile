# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.

  # UCP 2.1 node for DDC on CentOS 7.3
    config.vm.define "centos-node" do |centos_node|
      centos_node.vm.box = "centos/7"
      centos_node.vm.network "private_network", type: "dhcp"
      centos_node.vm.hostname = "centos-node"
      config.vm.provider :virtualbox do |vb|
         vb.customize ["modifyvm", :id, "--memory", "3072"]
         vb.customize ["modifyvm", :id, "--cpus", "2"]
         vb.name = "centos-node"
      end
      centos_node.vm.provision "shell", inline: <<-SHELL
       sudo yum -y remove docker
       sudo yum -y remove docker-selinux
       sudo rpm --import "https://sks-keyservers.net/pks/lookup?op=get&search=0xee6d536cf7dc86e2d7d56f59a178ac6c6238f52e"
       sudo yum install -y yum-utils
       sudo yum-config-manager --add-repo https://packages.docker.com/1.13/yum/repo/main/centos/7
       sudo yum makecache fast
       sudo yum -y install docker-engine
       sudo systemctl start docker
       sudo usermod -aG docker vagrant
     SHELL
    end

end
