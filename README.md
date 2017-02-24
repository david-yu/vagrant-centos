Vagrant Virtualbox setup for CS Engine 1.13.1-cs1 on CentOS 7.3 
========================

## Download vagrant from Vagrant website

```
https://www.vagrantup.com/downloads.html
```

## Install Virtual Box

```
https://www.virtualbox.org/wiki/Downloads
```

## Download Xenial box
```
vagrant init centos/7
```

## Install Vagrant Plugins
```
vagrant plugin install vagrant-hostsupdater
vagrant plugin install vagrant-multiprovider-snap
```

## Bring up nodes

```
vagrant up centos-node
```

## Stop nodes

```
vagrant halt centos-node
```

## Destroy nodes

```
vagrant destroy centos-node
```
