#Vagrant::DEFAULT_SERVER_URL.replace('https://vagrantcloud.com')
# Vagrantfile
# -*- mode: ruby -*-
# vi: set ft=ruby :

$provision_script = <<PROVISION
  export DEBIAN_FRONTEND=noninteractive
  dpkg --configure -a
  apt-get update -y
  apt-get install ifupdown -y
PROVISION

Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-18.04"
  config.vm.hostname = "my-local-dev"
  # vagrant-cachier
  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
  end
  
  # bind port
  config.vm.network "forwarded_port", guest: 8080, host: 8080
  # specify ipaddress
  config.vm.network "private_network", ip: "192.168.60.10"
  config.vm.provision "shell", inline: $provision_script
  config.vm.provider "virtualbox" do |vb|
     vb.gui = true
     vb.name = "Ubuntu18.04-box"
     vb.memory = "8192"
     vb.customize [
      "modifyvm", :id,
      "--vram", "256", # video memory for full screen
      "--clipboard", "bidirectional", # sharing clipboard
      "--draganddrop", "bidirectional", # enable drag & drop
      "--cpus", "2", # 2 cpu
      "--ioapic", "on" # enable I/O APIC
    ]
  end
  
  # ansible
  config.vm.synced_folder "./ansible", "/ansible"
  config.vm.provision "ansible_local" do |ansible|
    ansible.inventory_path = "/ansible/development"
    ansible.playbook = "/ansible/site.yml"
    ansible.limit = 'all'
  end
end
