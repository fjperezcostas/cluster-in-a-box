Vagrant.configure("2") do |config|

    # basic config
    NETWORK_NAME         = "local-cluster"
    LINUX_DISTRO         = "bento/ubuntu-22.04"
    CLUSTER_NODES_COUNT  = 1
    CONSUL_SERVERS_COUNT = 1
    NOMAD_SERVERS_COUNT  = 1
    CONSUL_PORT          = 10000
    NOMAD_PORT           = 10001
    FABIO_UI_PORT        = 10002
    FABIO_LB_PORT        = 10003

    ClusterNode = Struct.new(:id, :ip)

    nodes = []

    config.vm.provider "virtualbox" do |v|
        v.memory = 1024
        v.cpus = 1
    end

    (1..CLUSTER_NODES_COUNT).each do |i|
        newnode = ClusterNode.new("node-#{i.to_s.rjust(2, '0')}", "10.0.0.#{i + 2}")
        nodes << newnode
        config.vm.define newnode.id do |node|
            node.vm.box = LINUX_DISTRO            
            node.vm.hostname = newnode.id
            node.vm.network "private_network", ip: newnode.ip, virtualbox__intnet: NETWORK_NAME
            if i == 1
                node.vm.network "forwarded_port", guest: CONSUL_PORT, host: CONSUL_PORT
                node.vm.network "forwarded_port", guest: NOMAD_PORT, host: NOMAD_PORT
                node.vm.network "forwarded_port", guest: FABIO_UI_PORT, host: FABIO_UI_PORT
                node.vm.network "forwarded_port", guest: FABIO_LB_PORT, host: FABIO_LB_PORT
            end
            if i == CLUSTER_NODES_COUNT
                node.vm.provision "ansible" do |ansible|
                    ansible.playbook = "ansible/main.yml"
                    ansible.limit = "all"                    
                    ansible.raw_arguments = "--vault-password-file=.vault_pass"
                    ansible.groups = {
                      "consul_servers" => nodes.first(CONSUL_SERVERS_COUNT).map { |node| node.id },
                      "consul_clients" => nodes.drop(CONSUL_SERVERS_COUNT).map { |node| node.id },
                      "nomad_servers" => nodes.first(NOMAD_SERVERS_COUNT).map { |node| node.id },
                    }
                    ansible.extra_vars = {
                        consul_servers_count: CONSUL_SERVERS_COUNT,
                        nomad_servers_count: NOMAD_SERVERS_COUNT,
                        cluster_node_ips: nodes.map { |node| node.ip },
                        consul_port: CONSUL_PORT,
                        nomad_port: NOMAD_PORT,
                        fabio_ui_port: FABIO_UI_PORT,
                        fabio_lb_port: FABIO_LB_PORT
                    }
                end
            end
        end
    end
  
end