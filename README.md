# genomics-VM

Setup your development environment for genomics applications easily by setting up a Virtualbox VM through Vagrant.

## Prerequisite

- Vagrant
- Virtual Box

## Installation

- Install [vagrant-vbguest](https://github.com/dotless-de/vagrant-vbguest) vagrant plugin:

  `vagrant plugin install vagrant-vbguest`

- Build the vagrant box

  ``` shell
    git clone https://github.com/NAL-i5K/genomics-VM
    cd genomics-VM
    vagrant up
  ```

- You are done ! Wait for VM restart and you can login CentOS through **vagrant** user with password **vagrant**.

## What kind of environment is settled up in VM ?

- CentOS 6.9
- git 2.24
- samtools 1.9
- wigToBigWig

## Future development plan

Following tools will be installed into VM in the future:

- wiggle-tools
- bam_to_bigwig
- coordinate_conversion
