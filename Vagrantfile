# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "centos/7"
  config.vm.define "server" do |server|
    server.vm.hostname = "server.loc"
    server.vm.network "private_network", ip: "192.168.56.10"
  end
  config.vm.define "client" do |client|
    client.vm.hostname = "client.loc"
    client.vm.network "private_network", ip: "192.168.56.20"
  end
  # config.vm.provision "ansible" do |ansible|
  #   ansible.playbook = "ansible/provision.yml"
  #   ansible.inventory_path = "ansible/inventory"
  #   ansible.host_key_checking = "false"
  #   ansible.limit = "all"
  # end
end