# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  
  config.vm.box = "ubuntu_jammy64"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
  end

  SWARM_MANAGER_IP = "192.168.33.10"

  TOKEN_FILE = "swarm-token.txt"

  INIT_SWARM_SCRIPT = <<-SCRIPT
    cat ""
    docker swarm init --advertise-addr=$1
    docker swarm join-token -q worker > /vagrant/$2
  SCRIPT

  config.vm.provision "docker"

  config.vm.define "manager", primary: true do |worker|
    worker.vm.network "private_network", ip: SWARM_MANAGER_IP
    worker.vm.network "forwarded_port", guest: 2375, host: 2385

    worker.vm.provision "initswarm", type:"shell" do |s|
      s.inline = INIT_SWARM_SCRIPT
      s.args   = [SWARM_MANAGER_IP, TOKEN_FILE]
    end

  end  
  
  JOIN_SWARM_SCRIPT = <<-SCRIPT
    token=`cat /vagrant/$1`
    docker swarm join --token $token $2:2377
  SCRIPT

  config.vm.define "worker1" do |worker|
    worker.vm.network "private_network", ip: "192.168.33.11"
    worker.vm.provision "shell" do |s|
      s.inline = JOIN_SWARM_SCRIPT
      s.args   = [TOKEN_FILE, SWARM_MANAGER_IP]
    end 
  end

  config.vm.define "worker2" do |worker|
    worker.vm.network "private_network", ip: "192.168.33.12"
    worker.vm.provision "shell" do |s|
      s.inline = JOIN_SWARM_SCRIPT
      s.args   = [TOKEN_FILE, SWARM_MANAGER_IP]
    end 
  end

end
