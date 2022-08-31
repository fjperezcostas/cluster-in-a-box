Vagrant.configure("2") do |config|

    # basic config
    NETWORK_NAME        = "local-cluster"
    LINUX_DISTRO        = "bento/ubuntu-22.04"
    SERVER_NODES_COUNT  = 1
    CLIENT_NODES_COUNT  = 1
    GATEWAY_ID          = "cluster-gw"
    CONSUL_PORT         = 10000
    NOMAD_PORT          = 10001
    FABIO_UI_PORT       = 10002
    FABIO_LB_PORT       = 10003
    AUTHZ_SERVER_PORT   = 8080
    
    ClusterNode = Struct.new(:id, :ip)

    clusterNodes = []

    config.vm.define GATEWAY_ID do |gw|
        gw.vm.box = LINUX_DISTRO
        gw.vm.hostname = GATEWAY_ID
        gw.vm.network "private_network", ip: "10.0.0.3", virtualbox__intnet: NETWORK_NAME
        gw.vm.network "forwarded_port", guest: CONSUL_PORT, host: CONSUL_PORT
        gw.vm.network "forwarded_port", guest: NOMAD_PORT, host: NOMAD_PORT
        gw.vm.network "forwarded_port", guest: FABIO_UI_PORT, host: FABIO_UI_PORT
        gw.vm.network "forwarded_port", guest: FABIO_LB_PORT, host: FABIO_LB_PORT
        gw.vm.network "forwarded_port", guest: AUTHZ_SERVER_PORT,  host: AUTHZ_SERVER_PORT
    end

    (1..SERVER_NODES_COUNT).each do |i|
        newServer = ClusterNode.new("server-#{i}","10.0.0.#{i+4}")
        clusterNodes << newServer
        config.vm.define newServer.id do |server|
            server.vm.box = LINUX_DISTRO
            server.vm.hostname = newServer.id
            server.vm.network "private_network", ip: newServer.ip, virtualbox__intnet: NETWORK_NAME
        end
    end

    (1..CLIENT_NODES_COUNT).each do |i|
        newClient = ClusterNode.new("client-#{i}","10.0.0.#{i+SERVER_NODES_COUNT+4}")
        clusterNodes << newClient
        config.vm.define newClient.id do |client|
            client.vm.box = LINUX_DISTRO
            client.vm.hostname = newClient.id
            client.vm.network "private_network", ip: newClient.ip, virtualbox__intnet: NETWORK_NAME
            if i == CLIENT_NODES_COUNT
                serverNodes = clusterNodes.select{ |node| node.id.start_with?("server-")}
                clientNodes = clusterNodes.select{ |node| node.id.start_with?("client-")}
                client.vm.provision "ansible" do |ansible|
                    ansible.playbook = "ansible/main.yml"
                    ansible.limit = "all"                    
                    ansible.raw_arguments = "--vault-password-file=.vault_pass"
                    ansible.groups = {
                      "cluster_gw" => GATEWAY_ID,
                      "cluster_servers" => serverNodes.map { |node| node.id },
                      "cluster_clients" => clientNodes.map { |node| node.id }
                    }
                    ansible.extra_vars = {
                        cluster_servers_count: SERVER_NODES_COUNT,
                        cluster_clients_count: CLIENT_NODES_COUNT,
                        cluster_server_ips: serverNodes.map { |node| node.ip },
                        cluster_client_ips: clientNodes.map { |node| node.ip },
                        cluster_consul_port: CONSUL_PORT,
                        cluster_nomad_port: NOMAD_PORT,
                        cluster_fabio_ui_port: FABIO_UI_PORT,
                        cluster_fabio_lb_port: FABIO_LB_PORT,
                        cluster_authz_server_port: AUTHZ_SERVER_PORT
                    }
                end
            end
        end
    end
  
end