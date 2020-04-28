FROM ubuntu:18.04

ARG DOCKER_VERSION=5:19.03.4~3-0~ubuntu-bionic
ARG REMOTING_VERSION=4.2.1
ARG user=jenkins
ARG group=jenkins
# Make sure UID's and GID's match on server
ARG USER_UID=1000
ARG USER_GID=1000
ARG DOCKER_GID=999
ARG AGENT_WORKDIR=/home/${user}/agent

RUN apt-get update && apt-get dist-upgrade -y && rm -rf /var/lib/apt/lists/*
RUN apt-get update && apt-get install -y \
    git \
    apt-transport-https \
    curl \
    init \
    openssh-server openssh-client \
 && rm -rf /var/lib/apt/lists/*

# Install Java
RUN apt-get update && apt-get install -y openjdk-11-jdk && rm -rf /var/lib/apt/lists/*

# Add Docker CLI
# http://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/
RUN apt-get update \
    && apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common -y \
    && curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add - \
    && add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable" \
    && apt-get update \
    && apt-get install docker-ce=${DOCKER_VERSION} -y

# Setup users and groups
# https://stackoverflow.com/questions/47854463/docker-got-permission-denied-while-trying-to-connect-to-the-docker-daemon-socke/55578829#55578829
# https://blog.mkowalski.net/2018/01/21/build-docker-images-with-jenkins-running-in-docker/
RUN groupadd -g ${USER_GID} jenkins
# Make Docker GID match server Docker GID
RUN groupmod -g ${DOCKER_GID} docker
RUN useradd --create-home --home /home/${user} --gid ${USER_GID} --groups ${DOCKER_GID} --no-user-group --uid ${USER_UID} ${user}

# Install jenkins jnlp
RUN curl --create-dirs -fsSLo /usr/share/jenkins/agent.jar https://repo.jenkins-ci.org/public/org/jenkins-ci/main/remoting/${REMOTING_VERSION}/remoting-${REMOTING_VERSION}.jar \
  && chmod 755 /usr/share/jenkins \
  && chmod 644 /usr/share/jenkins/agent.jar \
  && ln -sf /usr/share/jenkins/agent.jar /usr/share/jenkins/slave.jar

USER ${user}
ENV AGENT_WORKDIR=${AGENT_WORKDIR}
RUN mkdir /home/${user}/.jenkins && mkdir -p ${AGENT_WORKDIR}

VOLUME /home/${user}/.jenkins
VOLUME ${AGENT_WORKDIR}
WORKDIR /home/${user}

USER root
COPY jenkins-agent /usr/local/bin/jenkins-agent
RUN chmod +x /usr/local/bin/jenkins-agent &&\
    ln -s /usr/local/bin/jenkins-agent /usr/local/bin/jenkins-slave

USER ${user}

ENTRYPOINT ["jenkins-slave"]
