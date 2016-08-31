# Nginx Ghost Stack for Docker Cloud

A preconfigured Docker Cloud Stack for running Ghost blog Services.

See [this blog post](https://blog.switchbit.io/developing-a-ghost-theme-with-gulp-part-5/) for more detail.

## Usage

[![Deploy to Docker Cloud](https://files.cloud.docker.com/images/deploy-to-dockercloud.svg)](https://cloud.docker.com/stack/deploy/?repo=https://github.com/donovanmuller/nginx-ghost-stack)

See [`docker-cloud.yml`](docker-cloud.yml) for reference.

## Features

This stack provides the following:
 
* An Nginx reverse proxy optimised for proxying to Ghost instances
* automated (creation and renewal) SSL/TLS encryption using [Let's Encrypt](https://letsencrypt.org)
* which enables serving [HTTP/2](https://en.wikipedia.org/wiki/HTTP/2) for supported clients

## Nginx reverse proxy

A variant of the [docker-gen](https://github.com/jwilder/docker-gen) project, supporting Docker Cloud, is used in conjunction with a 
[Nginx Alpine](https://hub.docker.com/_/nginx/) based image in the ["Seperate Container Install"](https://github.com/jwilder/docker-gen#separate-container-install)
configuration to provide the reverse proxying. The Alpine version of the official Nginx image allows the use of HTTP/2. 
Unfortunately the `jessie` based image does not yet support HTTP/2 because of this [issue](https://github.com/nginxinc/docker-nginx/issues/76#issuecomment-203024132).  

Nginx is optimised for serving Ghost instances using the [`ghost-nginx-proxy-template`](ghost-nginx-proxy-template/README.md) image.
In addition, Ghost themes based on the ["Developing a Ghost theme with gulp"](https://blog.switchbit.io/tag/ghost-tag/) series will be able to take advantage of
 the asset caching strategies.
 
The [docker-gen-docker-cloud](docker-gen-docker-cloud/README.md) image is used to watch for new Ghost Services
and automatically reload the Nginx proxy (by redeploying the Nginx Service) with the relevant Nginx server blocks configuration.

##  Let's Encrypt

The [docker-letsencrypt-nginx-proxy-companion](https://hub.docker.com/r/donovanmuller/docker-letsencrypt-nginx-proxy-companion/) image is used to allow automatic creation and renewal
of Let'sd Encrypt issued certificates for proxied Ghost instances. The `docker-letsencrypt-nginx-proxy-companion-docker-cloud`
image is a fork of [docker-letsencrypt-nginx-proxy-companion](https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion)
but with support for Docker Cloud.

### Staging API

By default the Let's Encrypt *staging* URI (`ACME_CA_URI=https://acme-staging.api.letsencrypt.org/directory`) is used.
This is to facilitate testing and to prevent unwanted [rate limiting](https://letsencrypt.org/docs/rate-limits/) issues.

**Please make sure to switch to the production URI, `https://acme-v01.api.letsencrypt.org/directory`, when needed.**

## Building images

To build the `docker-gen-docker-cloud` and `nginx-ghost-proxy-template` images, use [Docker Compose](docker-compose.yml) 
with `docker-compose build`.



