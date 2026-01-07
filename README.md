# paperless-ngx-hardening

This repo contains a script that deploys a hardened paperless-ngx in Docker. The hardening itself is done through launching paperless-ngx inside a network-isolated environment. The user calls into Paperless-ngx through reverse-proxying instance of hardened [Caddy](https://caddyserver.com/).

## Pre-requisites

```sh
# update all packages on the system - always a good idea for better security posture
sudo apt update
sudo apt upgrade -y
# install docker with a single line script from the official website
curl -fsSL https://get.docker.com | sh
```

## Launching

```sh
cd ~

# clone the repo
git clone https://github.com/pluromech/paperless-ngx-hardening.git
cd paperless-ngx-hardening

# specify docker project name
read -p "Enter desired docker project name [paperless]: " COMPOSE_PROJECT_NAME
COMPOSE_PROJECT_NAME=${COMPOSE_PROJECT_NAME:-paperless}
echo "COMPOSE_PROJECT_NAME=${COMPOSE_PROJECT_NAME}" >> .env
sed -i "s/COMPOSE_PROJECT_NAME/${COMPOSE_PROJECT_NAME}/g" Caddyfile

# specify host
read -p "Enter desired hostname [localhost]: " PAPERLESS_HOST
PAPERLESS_HOST=${PAPERLESS_HOST:-localhost}
echo "PAPERLESS_HOST=$PAPERLESS_HOST" >> .env
sed -i "s/PAPERLESS_HOST/${PAPERLESS_HOST}/g" Caddyfile

# specify port
read -p "Enter desired port [12345]: " PAPERLESS_PORT
PAPERLESS_PORT=${PAPERLESS_PORT:-12345}
echo "PAPERLESS_PORT=$PAPERLESS_PORT" >> .env
sed -i "s/PAPERLESS_PORT/${PAPERLESS_PORT}/g" Caddyfile

# auto-generate a secret key used for creating app sessions
echo "PAPERLESS_SECRET_KEY=$(openssl rand -hex 32)" >> .env

# pull & launch
sudo docker compose pull
sudo docker compose up -d
```
