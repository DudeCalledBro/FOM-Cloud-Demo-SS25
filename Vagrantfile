# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "bento/debian-12"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  config.vm.box_check_update = true

  N = 3
  (1..N).each do |machine_id|
    config.vm.define "k3s-0#{machine_id}" do |k3s|

      # Configure Virtual Machine Hostname
      k3s.vm.hostname = "k3s-0#{machine_id}"

      # Configure Virtual Machine Network
      k3s.vm.network "private_network", ip: "10.40.0.#{10+machine_id}"

      # Provider-specific configuration so you can fine-tune various
      # backing providers for Vagrant. These expose provider-specific options.
      k3s.vm.provider "parallels" do |vb|

        # Define the Virtual Machine Name in Parallels
        vb.name = "k3s-0#{machine_id}"

        # Configure the Virtual Machine Memory
        vb.memory = "1024"

        # Configure the Virtual Machine CPUs
        vb.cpus = 1

        # Update Virtual Machine Guest Tools
        vb.update_guest_tools = true

        # Disable annoying Parallels Window
        vb.customize ["set", :id, "--startup-view", "headless"]
      end
    end
  end

  # Enable provisioning with ansible.
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "prepare.yml"
  end
end
