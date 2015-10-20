OpenStack Puppet samples
# openstack-puppet-samples

*Note from Iben:* We will edit this to show a settings example for all-in-one OpenStack Kilo install on single NIC machine with only eth0.

This can work on a VM or Physical server and will take care of the work to swtich IP from eth0 DHCP to bridge static address.
Thanks to Cloudbase for this work: cloudbase/openstack-puppet-samples

As an example we will show how this can be installed on Servers .76 and .77 so we need to allocate a range of IP addresses to each one.
be sure to read all the steps below... here is a guide once you're ready to deploy.

* .76 - 10.241.1.220-224
* .77 - 10.241.1.225-229

NOTE: give each server their own private /24 subnet. These IP addresses will be used by the local DHCP server to automatically configure the network for the OpenStack VMs we instantiate on each node. Additionaly, running VM instances can be asssigned a floating IP from the public allocation pool allowing us to communicate with the VM from outside the OpenStack environment. 

### This example is for .76

    $interface = 'eth0'
    $ext_bridge_interface = 'br-ex'
    $dns_nameservers = ['8.8.8.8', '8.8.4.4'] # update for your lab name servers
    $private_subnet_cidr = '192.168.76.0/24' 
    $public_subnet_cidr = '10.241.0.0/16'
    $public_subnet_gateway = '10.241.254.253'
    $public_subnet_allocation_pools = ['start=10.241.1.220,end=10.241.1.224']

### This example is for .77

    $interface = 'eth0'
    $ext_bridge_interface = 'br-ex'
    $dns_nameservers = ['8.8.8.8', '8.8.4.4'] # update for your lab name servers
    $private_subnet_cidr = '192.168.77.0/24' 
    $public_subnet_cidr = '10.241.0.0/16'
    $public_subnet_gateway = '10.241.254.253'
    $public_subnet_allocation_pools = ['start=10.241.1.225,end=10.241.1.229']


OpenStack Kilo Puppet manifests
===============================

This is a collection of Puppet resources meant to be useful for proof of
concepts and similar scenarios where the goal is to deploy OpenStack in a
quick and painless way, often on limited hardware resources.

All in One
----------

Use this manifest to deploy OpenStack with:

* One single host
* One single NIC
* KVM hypervisor

The host can be either a physical or even a virtual machine, as long as it supports nested
virtualization (e.g. VMWare Workstation / Player / Fusion / ESXi, KVM, etc).

### OpenStack release

Kilo

### OpenStack components

* Keystone
* Glance
* Nova / KVM
* Neutron / Open vSwitch
* Cinder / LVM
* Horizon

To be added soon:

* Swift
* Heat
* Ceilometer

### Supported operating system

Ubuntu 14.04 LTS

### Prerequisites

system should have a static IP address on eht0 - don't use DHCP 

check that dns is properly setup in /etc/resolv.conf use public DNS like google 8.8.8.8 and 8.8.4.4

logged in as a user - run these command. (sudo is not needed if you are logged in as root)

    sudo apt-get update -y
    sudo apt-get install puppet -y
    sudo puppet module install openstack/keystone --version ">=6.0.0 <7.0.0"
    sudo puppet module install openstack/glance --version ">=6.0.0 <7.0.0"
    sudo puppet module install openstack/cinder --version ">=6.0.0 <7.0.0"
    sudo puppet module install openstack/nova --version ">=6.0.0 <7.0.0"
    sudo puppet module install openstack/neutron --version ">=6.0.0 <7.0.0"
    sudo puppet module install example42/network
    sudo puppet module install saz/memcached

### Edit the Manifest pp file

Modify the variables at the beginning of _openstack-aio-ubuntu-single-nic.pp_
to match your environment where needed (see examples above).

Copy the manifest file to the system where we want to install openstack. The prerequisites must be installed already.

### Run puppet to apply the manifest

    sudo puppet apply --verbose openstack-aio-ubuntu-single-nic.pp

### OpenStack environment

After applying the manifest, the environment is fully deployed and ready to be
used. A Cirros image is included in Glance.

OpenStack Dahsboard (Horizon):

    http://<openstack_host>/horizon

Login with either the "admin" or "demo" credentials. Default password is
"Passw0rd" for both.

When booting instances, attach a nic to the "private" network.

Command line environment variables:

    source /root/keystonerc_admin

or

    source /root/keystonerc_demo

