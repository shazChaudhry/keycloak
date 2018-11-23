# -*- mode: ruby -*-
# vi: set ft=ruby :

$docker_swarm_init = <<SCRIPT
docker swarm init --advertise-addr 192.168.99.101 --listen-addr 192.168.99.101:2377
SCRIPT

Vagrant.configure("2") do |config|
	config.vm.box = "ubuntu/xenial64"
	config.hostmanager.enabled = true
	config.hostmanager.manage_host = true
	config.hostmanager.manage_guest = true
	config.vm.provision "docker"

	config.vm.define "keycloak", primary: true do |keycloak|
		keycloak.vm.hostname = 'keycloak'
		keycloak.vm.network :private_network, ip: "192.168.99.101"
		keycloak.vm.provider :virtualbox do |v|
			v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
			v.customize ["modifyvm", :id, "--memory", 8000]
			v.customize ["modifyvm", :id, "--name", "keycloak"]
		end
		keycloak.vm.provision :shell, inline: $docker_swarm_init
	end

end
