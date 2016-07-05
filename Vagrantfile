# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.require_version ">= 1.7.0"

Vagrant.configure("2") do |config|
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.

  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = "bento/ubuntu-14.04"

  if Vagrant.has_plugin? 'vagrant-omnibus'
    # Set Chef version for Omnibus
    config.omnibus.chef_version = :latest
  else
    raise Vagrant::Errors::VagrantError.new,
      "vagrant-omnibus missing, please install the plugin:\n" +
      "vagrant plugin install vagrant-omnibus"
  end

  # Disable vagrant-berkshelf because it overrides chef cookbooks path
  # See https://github.com/berkshelf/vagrant-berkshelf/issues/274
  if Vagrant.has_plugin? 'vagrant-berkshelf'
    config.berkshelf.enabled = false
  end

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine.
  # Forward MySql port on 33066, used for connecting admin-clients to localhost:33066
  config.vm.network :forwarded_port, guest: 3306, host: 33066
  # Forward http port on 8080, used for connecting web browsers to localhost:8080
  config.vm.network :forwarded_port, guest: 80, host: 8080
  # Forward http port on 8025, used for connecting web browsers to MailHog
  config.vm.network :forwarded_port, guest: 8025, host: 8025

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  #config.vm.network :private_network, ip: "192.168.33.10"
  #config.vm.network :public_network
  config.vm.network :public_network, bridge: "en1: Wi-Fi (AirPort)"

  # update /etc/host in host machine
  config.vm.hostname = 'local.dev'
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true
  #config.hostmanager.aliases = %w(example-box.localdomain example-box-alias)

  config.hostmanager.ip_resolver = proc do |vm, resolving_vm|
    if hostname = (vm.ssh_info && vm.ssh_info[:host])
      `vagrant ssh -c "/sbin/ifconfig eth1" | grep 'inet addr:' | cut -d: -f2 | awk '{ print $1}'`.split("\n").first[/(\d+\.\d+\.\d+\.\d+)/, 1]
    end
  end

  # Set share folder permissions to 777 so that apache can write files
  config.vm.synced_folder ".", "/vagrant", mount_options: ['dmode=777','fmode=666']

  # Provider-specific configuration so you can fine-tune VirtualBox for Vagrant.
  # These expose provider-specific options.
  config.vm.provider :virtualbox do |vb|
    # Use VBoxManage to customize the VM.
    # For example to change memory or number of CPUs:
    vb.customize ["modifyvm", :id, "--memory", "1024"]
    vb.customize ["modifyvm", :id, "--cpus", "1"]
  end

  # Enable provisioning with chef zero, specifying a cookbooks path, roles
  # path, and data_bags path (all relative to this Vagrantfile), and adding
  # some recipes and/or roles.
  config.vm.provision :chef_zero do |chef|
    chef.cookbooks_path = ["berks-cookbooks", "cookbooks"]
    chef.data_bags_path = "data_bags"

    # List of recipes to run
    chef.add_recipe "vagrant_main"
    chef.add_recipe "vagrant_main::nodejs"
    chef.add_recipe "vagrant_main::wordpress"
    chef.add_recipe "vagrant_main::magento"
  end
end
