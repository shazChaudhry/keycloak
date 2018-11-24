# Work in progress

# User story
As a member of DevOps team, I would like to stand up Keycloak instance along with postgres database

# Prerequisite
Docker swarm mode environment is required:
1. You may use the provided `Vagrantfile` which creates a single-node swarm cluster. You might have to adjust memory allocation in the Vagrantfile. It is assumed that you have already installed [Vagrant](https://www.vagrantup.com), [Oracle VM VirtualBox](https://www.virtualbox.org) and [GitBash](https://gitforwindows.org) _(this Varantfile was tested on Windows 10 pro machine)_
2. *OR* see [Docker for AWS](https://docs.docker.com/docker-for-aws) documentation on how to create a Docker swarm cluster on AWS

# If infra created locally using provided Vagrantfile
Create VMs locally and change directory to docker compose file is:
- `vagrant up` - This will create 1x node docker swarm mode cluster
- `vagrant ssh` - SSH in to the VM with is a master node
- `cd /vagrant` - change to the mounted directory
- `docker stack deploy --compose-file docker-compose.yml keycloak` This will deploy the entire stack
- `docker service ls` - Check status of services

## Clean-up
On the swarm master node, run the following commands:
* `docker stack rm keycloak` to remove the stack
* `exit` to exit the Virtual Box
* `vagrant destroy --force` to destroy the VMs

# If infra created in the cloud _(Option #2 above)_
It is assumed you have followed [Docker for AWS](https://docs.docker.com/docker-for-aws/) documentation to create a new VPC. Follow these commands in an ssh client to log in to your master node _(I'm using gitbash on Windows 10 Pro)_.

**Please** note you can not ssh directly into worker nodes. You have to use a manager node as a jump box
- `eval $(ssh-agent) OR exec ssh-agent bash`
- `ssh-add -k ~/.ssh/personal.pem` _(You wiill have to use your own key)_
- `ssh-add -L`
- `ssh -A docker@<Manager Public IP>`
- `cat /etc/*-release`
- `docker node ls`

Clone this repo and change directory by following these commands:
- `alias git='docker run -it --rm --name git -v $PWD:/git -w /git alpine/git'`  _(This alias is only required if git is not already installed on your machine. This alias will allow you to clone the repo using a git container)_
- `git version`
- `git clone https://github.com/shazChaudhry/keycloak.git`
- `sudo chown -R $USER:$USER keycloak`
- `cd keycloak`

Notes:
> In a Docker swarm mode, only a single Compose file is accepted. If your configuration is split between multiple Compose files, e.g. a base configuration and environment-specific overrides, you can combine these by passing them to docker-compose config with the -f option and redirecting the merged output into a new file.

> These instructions are only needed if files to be merged have been edited / updated for any reason (for your info, a default "docker-stack.yml" has already been generated and is ready to be used).
- `alias docker-compose='docker run --interactive --tty --rm --name docker-compose --volume $PWD:/compose --workdir /compose docker/compose:1.23.1'`
- `docker-compose version`
- `docker-compose -f docker-compose.yml -f docker-compose.AWS.cloudstor.yml config > docker-stack.yml`

Note:
> If you did have to generate a new `docker-stack.yml` file above then ensure you follow the instructions below before launching the stack
- Open the generated "docker-stack.yml" file, address WARNINGS _(top two lines)_ and then delete WARNING lines
  - Ensure that the source path for secret files is correct i.e. $PWD is not missing

Run the combined stack. Please note that self-signed certificates are in ./certs directory:
  - `docker stack deploy --compose-file docker-stack.yml keycloak` You may be interested in knowing that this stack defines a volume plugin called [Cloudstor](https://docs.docker.com/docker-for-aws/persistent-data-volumes/). Docker containers can use a volume created with Cloudstor _(available across entire cluster)_ to mount a persistent data volume
  - `docker service ls`

# Testing
  In your favorite browser, navigate to:
  - https://HOST_NAME _(e.g. https://keycloak for locally provisioned infra)_
  - username / password = admin / Password01

# Configuring Keycloak
- Follow the documentation at https://www.keycloak.org/documentation.html

# References
- Jenkins-keycloak-plugin youtube: https://wiki.jenkins.io/display/JENKINS/keycloak-plugin
