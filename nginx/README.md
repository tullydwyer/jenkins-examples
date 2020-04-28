# nginx
Runs an NGINX proxy for Jenkins.

## Installation Instructions (Ubuntu 18.04)
1. Create a folder `nginx` and the configuration with the files in this repo.
  *  `docker-compose.yml`
  *  `nginx.conf`

1. Install Certbot
```sh
sudo apt-get update
sudo apt-get install software-properties-common
sudo add-apt-repository universe
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install certbot
```

1. Create LetsEncrypt SSL certificates
```sh
sudo certbot certonly --standalone -d jenkins.example.com
```

1. Auto renew certificates
```sh
sudo crontab -e
```
```txt
@weekly certbot renew --pre-hook "su {USERNAME} -c docker-compose -f /.../nginx/docker-compose.yml down" --post-hook "su {USERNAME} -c docker-compose -f /.../nginx/docker-compose.yml up -d"
```

1. Install latest docker-compose (version 3.5+ required)
```sh
# https://stackoverflow.com/questions/49839028/how-to-upgrade-docker-compose-to-latest-version/52991655
sudo apt remove docker-compose
sudo apt autoremove
sudo apt install jq -y
VERSION=$(curl --silent https://api.github.com/repos/docker/compose/releases/latest | jq .name -r)
DESTINATION=/usr/local/bin/docker-compose
sudo curl -L https://github.com/docker/compose/releases/download/${VERSION}/docker-compose-$(uname -s)-$(uname -m) -o $DESTINATION
sudo chmod 755 $DESTINATION
```

1. Run container
```sh
docker-compose up -d
```
If there is an error about jenkins master not being available, we now need to run jenkins master to create the Jenkins master container (and Docker hostname record), then re-run the NGINX container.
