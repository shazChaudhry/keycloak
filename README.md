### User story

### Prerequisite
Docker swarm mode environment is required:
- Use provided `Vagrantfile` if you are unable to run Docker CE natively on a local machine. You might then have to adjust memory allocation
  - It is assumed that you have already installed Oracle VM VirtualBox
- *OR* see [Docker for AWS](https://docs.docker.com/docker-for-aws/) documentation on how to create a Docker swarm cluster on AWS
- *OR* see [Play with Docker](https://labs.play-with-docker.com/) which is a simple, interactive and fun playground to learn Docker

### Creating infrastructure locally
Create VMs locally and change directory to docker compose file is:
- `vagrant up` - This will create 1x node docker swarm mode cluster
- `vagrant ssh` - SSH in to the VM with is a master node
- `cd /vagrant`

### Running docker stack in the cloud
```
alias git='docker run -it --rm --name git -v $PWD:/git -w /git alpine/git'
git version
git clone https://github.com/shazChaudhry/keycloak.git
sudo chown -R $USER:$USER keycloak
cd keycloak
```

### Deploying Keycloak
```
docker stack deploy --compose-file docker-compose.yml keycloak
docker stack services keycloak
```

### Testing
In your favorite browser, navigate to:
- http://HOST_NAME _(e.g. http://keycloak for locally provisioned infra)_
- username / password = admin / password