# Deploy Malik Site With Docker + Nginx Proxy Manager

The DNS record already points `malik.murika.rw` to the server IP. This site runs as a small static Nginx container on port `8088`.

## 1. On the server

Clone or pull the repo, then run:

```bash
docker compose up -d --build
```

Check it locally on the server:

```bash
curl http://127.0.0.1:8088
```

## 2. In Nginx Proxy Manager

Create a new Proxy Host:

```text
Domain Names: malik.murika.rw
Scheme: http
Forward Hostname / IP: SERVER_PRIVATE_IP_OR_DOCKER_HOST_IP
Forward Port: 8088
Cache Assets: on
Block Common Exploits: on
Websockets Support: off
```

If Nginx Proxy Manager is running on the same Docker host and cannot reach `127.0.0.1`, use the server private IP or Docker gateway IP instead.

## 3. SSL

In the SSL tab:

```text
Request a new SSL Certificate
Force SSL: on
HTTP/2 Support: on
HSTS Enabled: optional
Email: your email
Agree to Let's Encrypt Terms
```

Save, then open:

```text
https://malik.murika.rw
```

## 4. GitHub Actions deploy

This repo includes `.github/workflows/deploy.yml`. It deploys every push to `main`.

Add these GitHub repository secrets:

```text
SERVER_HOST=206.189.211.25
SERVER_USER=root
SERVER_SSH_PORT=22
SERVER_APP_DIR=/opt/malik-site
SERVER_SSH_KEY=<private SSH key that can log in to the server>
```

The action copies the latest site files into `SERVER_APP_DIR`, then SSHes into the server and runs:

```bash
docker compose up -d --build
```

Make sure the server user can run Docker commands. If you use a non-root user, add it to the `docker` group or update the workflow to use `sudo docker compose`.
