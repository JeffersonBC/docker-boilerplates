# Træfik

This folder contains configuration files that can be used to deploy a Træfik container which provides reverse proxy and automatic generation of SSL certificates for other Docker containers.

### Prerequisites

Before deploying the Træfik container its necessary to create a `proxy` network, which will be used by Træfik to communicate with other containers.

```
docker network create proxy
```

After doing that, it's necessary to replace `<domain.com>` in the docker-compose.yml file for the real domain you will be using, and `email@server.com` in the traefik.toml file for an email you own. To access the træfik monitor, it's also necessary to create a password with `htpasswd` (which is usually contained in the `apache2-utils` or `apache-tools` package) using `htpasswd -nb admin secure_password` and pasting the result inside the array of the line `users = ["admin:<password-hash>"]` in `traefik.toml`.

Also, it's necessary to make sure ports 80 and 443 are exposed in the firewall.

### Use

To register a container in Træfik it's necessary to add the following labels:

```
traefik.backend: <service name>
traefik.frontend.entryPoints: http, https
traefik.frontend.rule: Host:<domain>
traefik.docker.network: proxy
traefik.port: "<container internal door>"
```

The rule `traefik.frontend.entryPoints: http, https` is necessary because of how `traefik.toml` is configured (otherwise if the user tries accessing a domain with http they won't be properly redirected to a https access correctly), but it may be different (or not necessary) with a different configuration.

It's also necessary to make the container part of the external `proxy` network, and to add the label `traefik.enable: "false"` in containers which are not meant to be directly exposed to the internet (like a data base container meant to only be directly accessed by a backend container). Since Træfik will handle all the traffic with the outside world and it connects to other containers using Docker's proxy network, it's a good idea to not expose containers' internal ports to the host machine.

An example docker-compose.yml file for services routed by Træfik could look something like this (plus things like environment variables, volumes, etc):

```
version: '3'

services:
  db:
    image: <db-image>
    networks:
      - default
    labels:
      traefik.enable: "false"

  back:
    build:
      context: <back-folder>
      dockerfile: <back-prod-dockerfile>
    networks:
      - default
      - proxy
    labels:
      traefik.backend: back
      traefik.frontend.entryPoints: http, https
      traefik.frontend.rule: Host:<domain.com>
      traefik.docker.network: proxy
      traefik.port: "8000"      # back is accesed by other containers in `back:8000`

  front:
    build:
      context: <front-folder>
      dockerfile: <front-prod-dockerfile>
    networks:
      - proxy
    labels:
      traefik.backend: front
      traefik.frontend.entryPoints: http, https
      traefik.frontend.rule: Host:<domain.com>
      traefik.docker.network: proxy
      traefik.port: "80"        # front is accesed by other containers in `front:80`

networks:
  proxy:
    external: true
```

### Tips

The way `traefik.toml` is configured every domain being routed through Træfik will use a https connection, which may not be what you want. There's an [examples page](https://docs.traefik.io/user-guide/examples/) in the Træfik configuration with examples of how to configure `traefik.toml` for other common uses.
