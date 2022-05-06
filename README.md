# ansible-wso2-apim

This is a repository for deploying WSO2 API Manager with Ansible. It is based on https://github.com/wso2/ansible-apim.

## Local testing

Vagrant and VirtualBox are required for local testing on Windows, macOS, and Linux. With these tools, you can create one or more virtual machines and install WSO2 API Manager on it automatically. You can use Vagrant to provision the virtual machine(s), or you can run Ansible yourself from macOS, Linux, or from the virtual machine.

This repository contains a Vagrantfile and JSON files in the inventory directory for testing environments. Two environments have been defined:

- "dev" This is the default, a single virtual machine will be created, and the WSO2 API Manager is configured to use the dev configuration.
   you can choose the operating system as an argument to the `vagrant up` command. i.e. `vagrant up centos8`, `vagrant up ubuntu18`, etc. `vagrant status` will show the status of the possible virtual machines.
- "test" If you set the environment variable `STAGE` to "test", four virtual machines will be created, one for the postgresql database, one for the load balancer/reverse proxy, and the WSO2 API Manager is configured on two virtual machines. All machines run centos8 stream.

### VirtualBox
https://www.virtualbox.org/wiki/Downloads

### Vagrant
https://www.vagrantup.com/downloads.html

## Author Information
@bbaassssiiee
