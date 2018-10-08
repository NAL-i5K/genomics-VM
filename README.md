# genomics-VM

[![Build Status](https://travis-ci.org/NAL-i5K/genomics-VM.svg?branch=master)](https://travis-ci.org/NAL-i5K/genomics-VM)

Setup your development environment for genomics applications easily by setting up a Virtualbox VM through Vagrant.

## Prerequisite

- Vagrant > 2.0
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

### General purpose

- CentOS 6.9
- git 2.24
- Python 2.7.15
- [samtools](http://www.htslib.org/) 1.9
- [wigToBigWig](https://genome.ucsc.edu/goldenpath/help/bigWig.html)
- [wiggle-tools](https://github.com/NAL-i5K/wiggle-tools)
- [bam_to_bigwig](https://github.com/NAL-i5K/bam_to_bigwig)
- [coordinate_conversion](https://github.com/NAL-i5K/coordinates_conversion)
- [genomics-workspace](https://github.com/NAL-i5K/genomics-workspace) (including python virtual environment with name **py2.7**)
- [jbrowse](https://github.com/GMOD/jbrowse)

### i5K-only tools

- [apollo2_data_build_script](https://gitlab.com/i5k_Workspace/apollo2_data_build_scripts)