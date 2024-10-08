---
layout: default
title: Caddy
parent: Webservers and load balancing
---

# Caddy

## Simple proxy pass

```
  caddy:
    image: caddy:latest
    restart: unless-stopped
    ports:
      - "443:443"
      - "443:443/udp"
    volumes:
      - /opt/backuppc/Caddyfile:/etc/caddy/Caddyfile
      - /opt/backuppc/caddy_data:/data
      - /opt/backuppc/caddy_config:/config
    networks:
      - proxy

```

## Caddy with manual certs compose file

```yaml
version: "3.7"
services:
  caddy:
    image: caddy:latest
    ports:
      - 80:80
      - 443:443
    networks:
      - proxy
    volumes:
      - /etc/ssl/certs/star.company.app.pem:/config/cert.pem
      - /etc/ssl/private/star.company.app.key:/config/cert.key
      - ./Caddyfile:/etc/caddy/Caddyfile
      - ./caddy_data:/data
    restart: unless-stopped

networks:
  proxy:
    external: true
```

## Caddyfile

```
backup.company.nl

reverse_proxy backuppc-app:80

tls /config/cert.pem /config/cert.key
```

## Caddyfile with auth

### Hash password

```bash
docker exec -it caddy caddy hash-password
Enter password:
Confirm password:
$2a$14$6KvVECx7VWkAwFMxtbaXwu/3cfBH9BLTvDFTfWETqKXmuG5wq8x1y
````

If you want to use this hash directly in Docker Compose then you have to preface
every `$` with another `$`.

TODO: add regex to automate this.

```
mon.aike.be {
  log {
    output stdout
  }
  handle /loki* {
     reverse_proxy http://loki:3100
     basicauth /loki/* {
       {$LOKI_USER} {$LOKI_PASS}
     }
  }
  handle {
    reverse_proxy http://grafana:3000
  }
}
```

## With IP allow list

```yaml
test.you.nl {

  @denied not remote_ip 84.83.123.123
  respond @denied "Access denied - Go play somewhere else." 403

  reverse_proxy http://app:3000
}
```
