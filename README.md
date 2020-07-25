# k8s-vagrant

## Description

k8s test cluster on Centos 8

## Requirements
- VirtualBox 6.1
- Ansible 2.9.7
- Vagrant 2
- vagrant-hosts plugin

Tested on Centos 8.2. You can use this script to prepare environment

```bash
# install virtualbox
dnf install -y binutils kernel-devel kernel-headers libgomp make patch gcc glibc-headers glibc-devel dkms
dnf install -y VirtualBox-6.1.x86_64
#reboot if needed  (after kernel upgrade)

# rebuild drivers, 
/sbin/vboxconfig
/usr/lib/virtualbox/vboxdrv.sh setup
curl -O https://download.virtualbox.org/virtualbox/6.1.10/Oracle_VM_VirtualBox_Extension_Pack-6.1.10.vbox-extpack
vboxmanage extpack install --accept-license=56be48f923303c8cababb0bb4c478284b688ed23f16d775d729b89a2e8e5f9eb \
Oracle_VM_VirtualBox_Extension_Pack-6.1.10.vbox-extpack

# install ansible
dnf install -y ansible

# install vagrant
curl -O https://releases.hashicorp.com/vagrant/2.2.9/vagrant_2.2.9_x86_64.rpm
dnf install -y vagrant_2.2.9_x86_64.rpm
vagrant plugin install vagrant-hosts
vagrant box add centos/8 --provider virtualbox

# install git
dnf install -y git
```

## Usage
Clone repo, edit Vagrantfile if needed and run 'vagrant up' command.
Grab a cup of coffee (it can take about 40 minutes on a 4-core machine) and wait until vagrant is done.

Use 'vagrant ssh k8s-master-1' command to login on first master node and then 'kubectl get nodes' to check cluster status.

## Config variables
Variables that can be changed in Vagrantfile:

```bash
M = 3 # number of master nodes
W = 1 # number of worker nodes
NODE_NETWORK="192.168.20.0" # internal virtualbox network for nodes, netmask is /24
POD_NETWORK="10.244.0.0/16" # pod network inside k8s cluster
```
