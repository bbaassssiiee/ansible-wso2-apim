# -*- mode: ruby -*-
# vi: set ft=ruby :
# To use these virtual machines install Vagrant and VirtualBox.
# vagrant up

Vagrant.require_version ">= 2.0.0"

# Select the config file from the STAGE environment variable (dev or test)
# VM Configs are loaded from json files.
$Stage = ENV['STAGE'] || "dev"
# Require JSON module
require 'json'
# Read JSON file with config details
guests = JSON.parse(File.read(File.join(File.dirname(__FILE__), "inventory", $Stage + ".json")))
# Local PATH_SRC for mounting
$PathSrc = ENV['PATH_SRC'] || "."
Vagrant.configure(2) do |config|

  # check for updates of the base image
  config.vm.box_check_update = true
  # wait a while longer
  config.vm.boot_timeout = 1200

  # disable update guest additions
  if Vagrant.has_plugin?("vagrant-vbguest")
    config.vbguest.auto_update = false
  end

  # enable ssh agent forwarding
  config.ssh.forward_agent = true

  # use the standard vagrant ssh key
  config.ssh.insert_key = false

  # Iterate through entries in JSON file
  guests.each do |guest|
    config.vm.define guest['name'], autostart: guest['autostart'] do |srv|
      srv.vm.box = guest['box']
      srv.vm.hostname = guest['name']
      srv.vm.network 'private_network', ip: guest['ip_addr']

      srv.vm.network :forwarded_port, host: guest['forwarded_port'], guest: guest['app_port']

      # set no_share to false to enable file sharing
      srv.vm.synced_folder ".", "/vagrant", id: "vagrant-root", disabled: guest['no_share']
      srv.vm.provider :virtualbox do |virtualbox|
        virtualbox.customize ["modifyvm", :id,
           "--audio", "none",
           "--cpus", guest['cpus'],
           "--memory", guest['memory'],
           "--graphicscontroller", "VMSVGA",
           "--vram", "64"
        ]
        virtualbox.gui = guest['gui']
        virtualbox.name = guest['name']
      end
    end
    config.vm.provision "ansible" do |ansible|
      ansible.compatibility_mode = "2.0"
      ansible.playbook = "provision.yml"
      ansible.inventory_path = "inventory/" + $Stage + "/hosts"
      ansible.galaxy_role_file = "roles/requirements.yml"
      ansible.verbose = "v"
      ansible.limit = guest['name']
    end
  end

  config.vm.define 'windows2022' , autostart: false do |windows|
    windows.vm.box = "jborean93/WindowsServer2022"
    windows.vm.network "private_network", ip: "192.168.56.222"
    windows.vm.network :forwarded_port, host: 45986, guest: 5986
    windows.vm.synced_folder ".", "/vagrant", id: "vagrant-root", disabled: true
  end

  # Ansible Controller VM
  config.vm.define 'ansible' , autostart: false do |controller|
    controller.vm.box = "ubuntu/focal64"
    controller.vm.network "private_network", ip: "192.168.56.3"
    controller.vm.provision :ansible_local do |ansible|
      ansible.install        = true
      ansible.install_mode = "pip_args_only"
      ansible.pip_args = "-r /vagrant/requirements.txt"
      ansible.pip_install_cmd = "sudo apt install -y python3-pip python3-winrm"
      ansible.playbook = "provision.yml"
      ansible.inventory_path = "inventory/" + $Stage + "/hosts"
      ansible.galaxy_role_file = "roles/requirements.yml"
      ansible.verbose = "v"
    end
  end
end
