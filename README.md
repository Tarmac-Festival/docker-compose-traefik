docker compose traefik
======================

This repo contains a `docker-compose` project which bootstraps a biased `traefik` instance.

The following is assumed:

* let's encrypt certificate retrivale via hetzners dns api for all exposed containers
* all containers which should be exposed are also attached to created `ingress` network
* admin dashboard is exposed via own domain and credentials

## quick start

1. clone repository
2. create admin dashboard credentials
```
htpasswd -bnBC 8 admin "${password}"
```
3. create `.env` file (configure **all** variables according to your setup)
```
cp env.sample .env
vim .env
```
4. start project
```
docker compose up -d
```

## expose containers

To expose containers add the following override `docker-compose.override.yml` file to your project:
```
services:
  example:
    labels:
      - "traefik.enable=true"
      - "traefik.docker.network=ingress"
      - "traefik.http.services.frontend.loadbalancer.server.port=${port}"
      - "traefik.http.routers.frontend.rule=Host(`${domain}`)"
    networks:
      - default
      - ingress

networks:
  ingress:
    name: ingress
    external: true
```