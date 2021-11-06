# cluster-in-a-box

cluster-in-a-box virtualizes a server cluster in your own computer. It boots N server and provision them with the next tools:

- [Consul](https://www.consul.io/): as service discovery and service mesh
- [Nomad](https://www.nomadproject.io/): as workload orchestator
- [Fabio](https://fabiolb.net/): as load balancer
- [Jenkins](https://www.jenkins.io/): automation of CI/CD tasks

### How to deploy cluster-in-a-box (only proven in Linux systems)

1. You have to install [VirtualBox](https://www.virtualbox.org/), [Ansible](https://www.ansible.com/) and [Vagrant](https://www.vagrantup.com/) previously.
2. Clone the project `git clone https://github.com/fjperezcostas/cluster-in-a-box.git`
3. `cd cluster-in-a-box/ && vagrant up` and wait until everything is deployed.
4. Once Ansible ends it will show you the necessary token to start with Jenkins config.
```
    TASK [config-server-leader : print init password jenkins] **********************
    ok: [server-1] => {
        "password.stdout": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
    }
```

### URLs to access the deployed services

| Service   |           URL            |
|-----------|--------------------------|
| Consul    |   http://localhost:9000  | 
| Nomad     |   http://localhost:9001  |   
| Jenkins   |   http://localhost:9002  | 
| Fabio UI  |   http://localhost:9003  | 
| Fabio lb  |   http://localhost:9999  |    

### Bonus Track

This repo contains 2 Jenkinsfiles on `jenkins/` which allows you deploy a full featured app and see how this mini-cloud works. You only have to create a `New Item` in Jenkins, choose `Pipeline` option, paste the content of Jenkinsfile and run the job.

Links to deployed app:

https://github.com/fjperezcostas/wetalk-web

https://github.com/fjperezcostas/wetalk-api