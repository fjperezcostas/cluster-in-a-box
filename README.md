# cluster-in-a-box

cluster-in-a-box virtualizes a server cluster in your own computer. It boots N server and provision them with the next tools:

- [Envoy](https://www.envoyproxy.io/): as proxy which filters who can use the network services
- [authz-server](https://github.com/fjperezcostas/authz-server): external authorization server written in Golang
- [OpenLDAP](https://www.openldap.org/): LDAP database used to store allowed network users
- [Consul](https://www.consul.io/): as service discovery and service mesh
- [Nomad](https://www.nomadproject.io/): as workload orchestator
- [Nomad-Autoscaling](https://developer.hashicorp.com/nomad/tools/autoscaling): as the autoscaler tool
- [Fabio](https://fabiolb.net/): as load balancer
- [Prometheus](https://prometheus.io/): as monitoring service

### How to deploy cluster-in-a-box (only proven in Linux systems)

1. You have to install [VirtualBox](https://www.virtualbox.org/), [Ansible](https://www.ansible.com/) and [Vagrant](https://www.vagrantup.com/) previously.
2. Clone the project `git clone https://github.com/fjperezcostas/cluster-in-a-box.git`
3. `cd cluster-in-a-box/ && vagrant up` and wait until everything is deployed.

### URLs to access the deployed services

| Service          | URL                                 |
|------------------|-------------------------------------|
| Consul           | https://localhost:10000             |
| Nomad            | https://localhost:10001             |
| Fabio UI         | https://localhost:10002             |
| Prometheus UI    | https://localhost:10003/prometheus  |
| http-echo-server | https://localhost:10003/http-server |

### Allowed users credentials

| email                 |         password         |
|-----------------------|--------------------------|
| foo20379905@gmail.com | @ZL[(;Z-!;B:PUJD8dE{&W(! |
| bar20379905@gmail.com | @ZL[(;Z-!;B:PUJD8dE{&W(! |
