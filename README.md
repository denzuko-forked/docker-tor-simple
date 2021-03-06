# docker-tor-simple

[![](https://img.shields.io/docker/build/osminogin/tor-simple.svg)](https://hub.docker.com/r/osminogin/tor-simple/builds/) [![](https://img.shields.io/docker/stars/osminogin/tor-simple.svg)](https://hub.docker.com/r/osminogin/tor-simple) [![](https://images.microbadger.com/badges/image/osminogin/tor-simple.svg)](https://microbadger.com/images/osminogin/tor-simple) [![License: MIT](https://img.shields.io/badge/License-MIT-lightgrey.svg)](https://opensource.org/licenses/MIT)

**Smallest minimal docker container for Tor network proxy daemon.**

Suitable for relay, exit node or hidden service modes with SOCKS5 proxy enabled. It works well as a single self-contained container or in cooperation with other containers (like `nginx` and `osminogin/php-fpm`) for organizing complex hidden services on the Tor network.

The image is based on great [Alpine Linux](https://alpinelinux.org/) distribution so it is has extremely low size (less than 5 MB).

Star this project on Docker Hub :star2: https://hub.docker.com/r/osminogin/tor-simple/


## Getting started


### Installation

Automated builds of the image are available on [Docker Hub](https://hub.docker.com/r/osminogin/tor-simple/) and is the recommended method of installation.

```bash
docker pull osminogin/tor-simple
```

Alternatively you can build the image yourself.

```bash
docker build -t tor github.com/osminogin/docker-tor-simple
```


### Quickstart

```bash
docker run -p 127.0.0.1:9050:9050 --name tor osminogin/tor-simple
```

After start Tor proxy available on `localhost:9050`

:exclamation:**Warning**:exclamation:

Don't bind SOCKSv5 port 9050 to public network addresses if you don't know exactly what you are doing (better bind to localhost as in the example above).


## Ports

* `9050` SOCKSv5 (without auth)


## Unit file for systemd

#### tor.service

```ini
[Unit]
Description=Tor service
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
Restart=always
RestartSec=15s
ExecStartPre=/usr/bin/docker pull osminogin/tor-simple
ExecStart=/usr/bin/docker run --rm --name tor -p 127.0.0.1:9050:9050 osminogin/tor-simple
ExecStop=/usr/bin/docker stop tor

[Install]
WantedBy=multi-user.target
```


## Examples

Example webserver deployment config with microservice architecture to setup Tor hidden service.


#### docker-compose.yml

```yaml
tor-node:
  image: osminogin/tor-simple
  links:
    - nginx:myservice

nginx:
  image: nginx
  links:
    - drupal:drupalhost
  volumes:
    - /srv/drupal:/srv/www:ro
    - /srv/nginx/nginx.conf:/etc/nginx/nginx.conf:ro

drupal:
  image: osminogin/php-fpm
  links:
    - mysql:mysqlhost
  volumes:
    - /srv/drupal:/srv/www

mysql:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: changeme
```


## License

MIT
