---
layout: default
title: Zot
has_children: false
parent: Self Hosted Apps
---

# Zot Registry

[link](https://zotregistry.io)

## Create htpasswd file

```bash
apt install apache2-utils

htpasswd -bBn aike 123456789 >> htpasswd
```

or

```bash
htpasswd -n username
```

## Config

```yaml
{
  "storage": {
      "rootDirectory": "/var/lib/zot"
  },
  "http": {
    "address": "0.0.0.0",
    "port": "5000",
    "auth": {
      "HTPasswd": {
        "path": "/etc/zot/passwd"
      }
    }
  },
  "log": {
      "level": "debug"
  },
  "extensions": {
      "metrics": {
          "enable": true,
          "prometheus": {
              "path": "/metrics"
          }
      }
  }
}
```

## Compose file

```yaml
version: '3.8'
services:

  caddy:
    image: lucaslorentz/caddy-docker-proxy:ci-alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /opt/caddy_data:/data

  zotregistry:
    image: ghcr.io/project-zot/zot-linux-amd64:latest
    container_name: zotregistry
    ports:
      - "5000:5000"
    volumes:
      - /opt/zot.aike.be/data:/var/lib/zot
      - /opt/zot.aike.be/zot-config.json:/etc/zot/config.json
      - /opt/zot.aike.be/htpasswd:/etc/zot/passwd
    labels:
      caddy: zot.aike.be
      caddy.reverse_proxy: "{{upstreams 5000}}"
```

## With Kamal

```
  zotregistry:
    image: ghcr.io/project-zot/zot-linux-amd64:latest
    host: server1
    port: 5000
    container_name: zotregistry
    directories:
      - /opt/zot/data:/var/lib/zot
    files:
      - infra/zot-config.json:/etc/zot/config.json
      - infra/htpasswd:/etc/zot/passwd
    env:
      clear:
        ZOT_LOG_LEVEL: DEBUG
    labels:
      traefik.enable: true
      traefik.http.routers.zot_secure.service: .....-zotregistry@docker
      traefik.http.routers.zot_secure.entrypoints: websecure
      traefik.http.routers.zot_secure.rule: Host(`zot.company.com`)
      traefik.http.routers.zot_secure.tls: true
      traefik.http.routers.zot_secure.tls.certresolver: letsencrypt
    options:
      network: "private"
```

The zot-config.json file is the same as the one above.

The htpasswd file is the same as the one above.

You can put both in the infra directory and Kamal will copy them to the right place.
