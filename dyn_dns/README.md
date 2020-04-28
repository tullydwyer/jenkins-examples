# dyn_dns
https://github.com/crazy-max/ddns-route53

This image automatically updates a AWS Route53 record with the containers pubic ip. This is usefull if we want a domain name but do not have a static public IP.

## Installation Instructions (Ubuntu 18.04)
1. Create a folder `nginx` and the configuration with the files in this repo.
  *  `docker-compose.yml`
  *  `ddns-route53.yml`

1. Update fields
1. Run container
```sh
docker-compose up -d
```
