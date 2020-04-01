# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "bento/ubuntu-16.04"
  # config.vm.box = "coreos-stable"
  # config.vm.box_url = "http://stable.release.core-os.net/amd64-usr/current/coreos_production_vagrant.json"

  # The offical box is broken.
  #config.vm.box = "bento/xenial64"
  config.ssh.insert_key = false
  #config.ssh.private_key_path = "/Users/philipfrilling/.vagrant.d/insecure_private_key_new"
  #config.vm.synced_folder '.', '/vagrant', disabled: true

  # First, install python
  config.vm.provision "shell" do |s|
    s.inline = "apt-get install -y python"
  end

  # General VirtualBox VM configuration.
  config.vm.provider :virtualbox do |v|
    v.memory = 512
    v.cpus = 1
    v.linked_clone = true
    v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    v.customize ["modifyvm", :id, "--ioapic", "on"]
  end

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  N = 1
  #
  # VAGRANT_VM_PROVIDER = "virtualbox"
  # ANSIBLE_RAW_SSH_ARGS = []
  #
  # (1..N-1).each do |machine_id|
  #   ANSIBLE_RAW_SSH_ARGS << "-o IdentityFile=#{ENV["VAGRANT_DOTFILE_PATH"]}/machines/kontena-#{machine_id}/#{VAGRANT_VM_PROVIDER}/private_key"
  # end

  # Start our master VM.
  (1..N).each do |i|
    config.vm.define "ubuntu-#{i}" do |config|
      config.vm.hostname = "ubuntu-#{i}"
      config.vm.network :private_network, ip: "192.168.70.1#{i}"

      if i == 1
        config.vm.provider :virtualbox do |v|
          v.customize ["modifyvm", :id, "--memory", 1536]
          v.customize ["modifyvm", :id, "--cpus", 2]
        end
      end

      if i == 2
        config.vm.provider :virtualbox do |v|
          v.customize ["modifyvm", :id, "--memory", 3072]
          v.customize ["modifyvm", :id, "--cpus", 2]
        end
      end

      # if i == N
      #   config.vm.provision :ansible do |ansible|
      #     ansible.inventory_path = "inventory/inventory-local.yml"
      #     ansible.limit = "all"
      #     ansible.playbook = "playbooks/setup-domain.yml"
      #     ansible.become = true
      #     #ansible.ask_vault_pass = true
      #     #ansible.compatibility_mode = "2.0"
      #     #ansible.verbose = "-v"
      #     #ansible.raw_ssh_args = ANSIBLE_RAW_SSH_ARGS
      #   end
      # end
    end
  end
end
