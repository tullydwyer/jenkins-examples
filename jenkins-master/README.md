# jenkins-master
Runs a jenkins master in a Docker container.
## Installation Instructions (Ubuntu 18.04)
**Docker version number should match the version number installed on the jenkins-master Docker image (Dockerfile).**
1. Install Docker and docker-compose.
```sh
sudo apt update
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt update
# List Docker versions
apt list -a docker-ce
sudo apt install -y docker-compose docker-ce=5:19.03.4~3-0~ubuntu-bionic
# Make sure docker does not auto update version and get out of sync with Docker image.
sudo apt-mark hold docker-ce
# Allow user to run docker
sudo usermod -aG docker $USER
```
1. Create a folder `jenkins-master` and the configuration with the files in this repo.
  *  `Dockerfile`
  *  `docker-compose.yml`

1. Match the host folder ownership to the 'jenkins' user the Dockerfile (<https://github.com/jenkinsci/docker/issues/177>)
```sh
cd jenkins-master
mkdir jenkins-log
mkdir jenkins-data
sudo chown 1000 jenkins-data/
sudo chown 1000 jenkins-log/
```
1. Build and bring up container at the same time.
```sh
docker-compose up --build -d
```
1. Check logs.
```sh
docker ps
docker logs {NAME/ID}
```
## Jenkins Master Configuration
### Set the Jenkins URL
1. Navigate `Manage Jenkins` > `Configure System`
2. `Jenkins Location` > `Jenkins URL`: This is where Jenkins cloud agents/slaves will default try to connect to, can be public or private endpoints depending on configuration.

### Set TCP port for inbound agents
This is the port that jenkins agents/slaves connect to when they are brought up.
1. Navigate `Manage Jenkins` > `Configure Global Security`
1. `Agents` > `TCP port for inbound agents` > `Fixed: 50000`
