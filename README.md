Vagrant Virtualbox setup for Docker EE Engine on CentOS 7.3
========================

An exercise on installing Docker EE Engine and properly configuring Device Mapper on CentOS, which may be helpful for walking through the install and configuration of Docker EE Engine before actually doing so in production environments. This vagrant file is provided strictly for educational purposes.

If you're looking for a full cluster deployment of EE Standard and Advanced on CentOS please take a look at this repo: https://github.com/yongshin/vagrant-docker-ee-centos

## Download vagrant from Vagrant website

```
https://www.vagrantup.com/downloads.html
```

## Install Virtual Box

```
https://www.virtualbox.org/wiki/Downloads
```

## Download CentOS 7 box
```
vagrant init centos/7
```

## Install Vagrant Plugins
```
vagrant plugin install vagrant-persistent-storage
```

## Create files in project to store environment variables with custom values for use by Vagrant
```
ee_url
```

## Bring up nodes

```
vagrant up centos-node
```

## Configure Device Mapper

After provisioning the node and installing Docker EE Engine it is highly recommended to configure DeviceMapper to use direct-lvm mode in production. You can read more about selecting Graph Drivers here: https://success.docker.com/KBase/An_Introduction_to_Storage_Solutions_for_Docker_CaaS#Selecting_Graph_Drivers

### Create a Disk for VM in VirtualBox

The best practice for configuring DeviceMapper with Docker is to provide a spare block device to create a logical volume as a thinpool for the graph driver storage. In Virtual Box you may manually create a new disk and attach it to the VM as shown in the images below:

#### Create a Disk
![Create Disk](images/centos-disk-create.png?raw=true)

#### Choose VMDK Disk type
![Disk Type](images/centos-disk-type.png?raw=true)

#### Select Disk Size
![Disk Size](images/centos-disk-size.png?raw=true)

#### Final Disk Config
![Final Disk Config](images/centos-disk-final.png?raw=true)

After creating the disk, when you run 'fdisk -l' you should be able to see the disks that are available to you.

```
[vagrant@centos-node ~]$ sudo su -
[root@centos-node ~]# fdisk -l

Disk /dev/sdb: 8589 MB, 8589934592 bytes, 16777216 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/sda: 42.9 GB, 42949672960 bytes, 83886080 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x000dc137

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1            2048        4095        1024   83  Linux
/dev/sda2   *        4096     2101247     1048576   83  Linux
/dev/sda3         2101248    83886079    40892416   8e  Linux LVM

Disk /dev/mapper/VolGroup00-LogVol00: 40.2 GB, 40231763968 bytes, 78577664 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/VolGroup00-LogVol01: 1610 MB, 1610612736 bytes, 3145728 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

The disk will be located on `/dev/sdb` if you created and attached just a second disk, or `/dev/sdc` if you created and attached a third disk. Use this as your device (instead of `/dev/xvdf`) when running through the Device Mapper config below.

### Configure Device Mapper

Configure Docker to use DeviceMapper as the graph storage driver: [Configure Docker With DeviceMapper](https://docs.docker.com/engine/userguide/storagedriver/device-mapper-driver/#/configure-docker-with-devicemapper)

### Validate Docker Device Mapper Config

Before properly configuring Docker with Device Mapper:

```
[vagrant@centos-node ~]$ docker info
Containers: 0
 Running: 0
 Paused: 0
 Stopped: 0
Images: 0
Server Version: 1.13.1-cs2
Storage Driver: overlay
 Backing Filesystem: xfs
 Supports d_type: true
Logging Driver: json-file
...
```

After properly configuring Docker with Device Mapper:

```
[vagrant@centos-node ~]$ docker info
Containers: 0
 Running: 0
 Paused: 0
 Stopped: 0
Images: 0
Server Version: 1.13.1-cs2
Storage Driver: devicemapper
 Pool Name: docker-thinpool
 Pool Blocksize: 524.3 kB
 Base Device Size: 10.74 GB
 Backing Filesystem: xfs
 Data file:
 Metadata file:
 Data Space Used: 19.92 MB
 Data Space Total: 3.997 GB
 Data Space Available: 3.977 GB
 Metadata Space Used: 40.96 kB
 Metadata Space Total: 41.94 MB
 Metadata Space Available: 41.9 MB
 Thin Pool Minimum Free Space: 399.5 MB
 Udev Sync Supported: true
 Deferred Removal Enabled: true
 Deferred Deletion Enabled: true
 Deferred Deleted Device Count: 0
 Library Version: 1.02.135-RHEL7 (2016-09-28)
Logging Driver: json-file
Cgroup Driver: cgroupfs
...
```

## Stop nodes

```
vagrant halt centos-node
```

## Destroy nodes

```
vagrant destroy centos-node
```
