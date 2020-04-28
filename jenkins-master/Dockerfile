ARG JENKINS_VERSION=2.233
ARG DOCKER_VERSION=5:19.03.4~3-0~debian-stretch

FROM jenkins/jenkins:${JENKINS_VERSION}-jdk11

USER root

# Allow jenkins user to run docker
# https://blog.mkowalski.net/2018/01/21/build-docker-images-with-jenkins-running-in-docker/
RUN groupadd docker -g 999 && \
    usermod -a -G docker jenkins

# Add Docker CLI
# http://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/
# https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-18-04
RUN apt-get update && apt-get install -y \
  apt-transport-https ca-certificates curl gnupg-agent software-properties-common \
  && curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add - \
  && add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian stretch stable" \
  && apt-get update && apt-get install -y \
  docker-ce=${DOCKER_VERSION} \
  && rm -rf /var/lib/apt/lists/*

USER jenkins
