# jenkins-slave-docker
Runs a jenkins slave that can also run docker build nodes.
## Installation Instructions (Ubuntu 18.04)
**Docker version should match the version installed on the jenkins-slave-docker Docker image (Dockerfile).**
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
```

```
sudo useradd jenkins
sudo usermod -a -G docker jenkins
```
1. Create a folder `jenkins-slave-docker` and the configuration with the files in this repo.
  *  `Dockerfile`
  *  `docker-compose.yml`
  *  `jenkins-agent`
1.Create new Jenkins node on Jenkins
  1. Navigate `Manage Jenkins` > `Manage Nodes`
  1. Click `New Node`
  1. Choose your options
  1. `Remote root directory` = `/home/jenkins/agent`
1. Update `docker-compose.yml` on server.
  1. `http://{JENKINS_MASTER_IP/HOST}:8080` = the Jenkins master IP or hostname e.g. `http://192.168.1.123:8080` or `http://myhostname.com:8080`. Make sure port matches the Jenkins master configuration.
  1. `{NODE_KEY}` = Click on the node in jenkins and there should be a long secret string if it is offline, e.g. `abc1234905e3b4ce3c033267c4381192d52c6f917a394ee705217f550a512743`
  1. `{NODE_NAME}` = Must match the node name in Jenkins.
1. Build and bring up container at the same time.
```sh
docker-compose up --build -d
```
1. Check logs.
```sh
docker ps
docker logs {NAME/ID}
```
