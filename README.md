# Work in progress

### User story
As a member of DevOps team, I would like to stand up Keycloak instance along with postgres database

### Prerequisite
Docker swarm mode environment is required:
1. You may use the provided `Vagrantfile` which creates a single-node swarm cluster. You might have to adjust memory allocation in the Vagrantfile. It is assumed that you have already installed [Vagrant](https://www.vagrantup.com), [Oracle VM VirtualBox](https://www.virtualbox.org) and [GitBash](https://gitforwindows.org) _(this Varantfile was tested on Windows 10 pro machine)_
2. *OR* see [Docker for AWS](https://docs.docker.com/docker-for-aws) documentation on how to create a Docker swarm cluster on AWS
3. *OR* see [Play with Docker](https://labs.play-with-docker.com) which is a simple, interactive and fun playground to learn Docker

### If infra created locally using provided Vagrantfile
Create VMs locally and change directory to docker compose file is:
- `vagrant up` - This will create 1x node docker swarm mode cluster
- `vagrant ssh` - SSH in to the VM with is a master node
- `cd /vagrant`

### if infra created in the cloud _(Option #2 or #3 above)_
SSH in to the swarm master node and execute the following commands to clone this repo
```
alias git='docker run -it --rm --name git -v $PWD:/git -w /git alpine/git'
git version
git clone https://github.com/shazChaudhry/keycloak.git
sudo chown -R $USER:$USER keycloak
cd keycloak
```

### Deploying Keycloak
Once repo has been cloned, execute the following commands to start keycloak service
- `docker stack deploy --compose-file docker-compose.yml keycloak`. This uses an embadded database
- `docker stack services keycloak`

### Configuring Keycloak
- Follow the documentation at https://www.keycloak.org/documentation.html

### Testing
In your favorite browser, navigate to:
- http://HOST_NAME _(e.g. http://keycloak for locally provisioned infra)_
- username / password = admin / password
