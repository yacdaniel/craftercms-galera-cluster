# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'yaml'
settings = YAML.load_file 'vagrant.yml'


# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.
  node_name1 = settings['vm_authoring_name'] + "_1"
  config.vm.define node_name1 do |node|
    config.vm.box = settings['vm_box']
    node_ip = settings['vm_authoring_cluster_ip']
    node.vm.network "private_network", ip: node_ip
    node.vm.provider "virtualbox" do |vb|
      # Display the VirtualBox GUI when booting the machine
      vb.gui = settings['vm_gui']
      # Customize the amount of memory on the VM:
      vb.memory = settings['vm_authoring_memory']
      # Customize the amount of CPU on the VM:
      vb.cpus = settings['vm_authoring_cpus']
      # Customize the name of the VM:
      vb.name = node_name1
    end

    # Disable the new default behavior introduced in Vagrant 1.7, to
    # ensure that all Vagrant machines will use the same SSH key pair.
    # See https://github.com/mitchellh/vagrant/issues/5005
    node.ssh.insert_key = false
    node.vm.provision "ansible" do |ansible|
      ansible.verbose = "v"
      ansible.playbook = "playbook_new_cluster.yml"
      ansible.extra_vars = {
        node_ip: node_ip,
        hostname: node_name1,
        bundle_filename: settings['authoring_bundle_name']
      }
    end
  end


  node_name2 = settings['vm_authoring_name'] + "_2"
  config.vm.define node_name2 do |node|
    config.vm.box = settings['vm_box']
    node_ip = settings['vm_authoring_node_ip']
    node.vm.network "private_network", ip: node_ip
    node.vm.provider "virtualbox" do |vb|
      vb.gui = settings['vm_gui']
      vb.memory = settings['vm_authoring_memory']
      vb.cpus = settings['vm_authoring_cpus']
      vb.name = node_name2
    end

    node.ssh.insert_key = false
    node.vm.provision "ansible" do |ansible|
      ansible.verbose = "v"
      ansible.playbook = "playbook_add_cluster_node.yml"
      ansible.extra_vars = {
        node_ip: node_ip,
        hostname: node_name2,
        galera_cluster_ip: settings['vm_authoring_cluster_ip'],
        bundle_filename: settings['authoring_bundle_name']
      }
    end
  end

  node_name3 = settings['vm_arbiter_name']
  config.vm.define node_name3 do |node|
    config.vm.box = settings['vm_box']
    node_ip = settings['vm_authoring_arbiter_ip']
    node.vm.network "private_network", ip: node_ip
    node.vm.provider "virtualbox" do |vb|
      vb.gui = settings['vm_gui']
      vb.memory = settings['vm_arbiter_memory']
      vb.cpus = settings['vm_arbiter_cpus']
      vb.name = node_name3
    end

    node.ssh.insert_key = false
    node.vm.provision "ansible" do |ansible|
      ansible.verbose = "v"
      ansible.playbook = "playbook_add_arbiter_node.yml"
      ansible.extra_vars = {
        node_ip: node_ip,
        hostname: node_name3,
        galera_cluster_ip: settings['vm_authoring_cluster_ip'],
        bundle_filename: settings['authoring_bundle_name']
      }
    end
  end
end
