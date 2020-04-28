# jenkins-examples
This repo was created to roughly document a personal Jenkins setup. It was also created to provide Jenkins configuration examples for anyone interested.

The repo outlines how to run the following:
* Jenkins master running in a Docker container.
* Jenkins slave running in a Docker container.
  * Supports Docker agents.
* NGINX proxy to allow SSL and public access for Jenkins.
  * Supports SSL using [Lets Encrypt](https://letsencrypt.org/)
  * Supports Jenkins agents/slaves.
* Dynamic DNS via Route53.

See each folder for a readme and example configurations and code.
