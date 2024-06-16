# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
    config.vm.box = "generic/debian12"

    config.vm.synced_folder ".", "/vagrant", disabled: true

    config.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
    end

    config.vm.define "server" do |server|
        server.vm.hostname = "server.local"
        server.vm.network :private_network, ip: "192.168.60.4", netmask: "25"

        server.vm.provider "virtualbox" do |vb|
            vb.memory=2048
            vb.cpus = 2
        end
    end

    NODE_COUNT = 3
    (1..NODE_COUNT).each do |node_id|
        config.vm.define "client#{node_id}" do |node|
           node.vm.hostname = "client#{node_id}.local"
           node.vm.network :private_network, ip: "192.168.60.#{30+node_id}", netmask: "25"

            if node_id == NODE_COUNT
                node.vm.provision :ansible do |ansible|
                     ansible.limit = "all"
                     ansible.playbook = "playbook.yml"
                     ansible.groups = {
                         "cluster_node" => ["client[1:#{NODE_COUNT}]", "server"],
                         "master" => ["server"]
                     }
                     ansible.raw_arguments = Shellwords.shellsplit(ENV['ANSIBLE_ARGS']) if ENV['ANSIBLE_ARGS']
                end
            end
        end
    end
end