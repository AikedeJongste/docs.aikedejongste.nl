---
layout: default
title: Nginx ProxyPass
parent: Webservers
---

# Nginx ProxyPass

This example is port 80 only, use Certbot to enable 443 and related settings.

The / at the end or a proxy_pass line is important! 

```
listen 80;
server_name prom.company.nl;

# this is required to proxy Grafana Live WebSocket connections.
map $http_upgrade $connection_upgrade {
  default upgrade;
  '' close;
}

upstream grafana {
  server localhost:3000;
}

upstream prom {
  server localhost:9090;
}

server {
  server_name prom.company.nl;
  root /usr/share/nginx/www;
  index index.html index.htm;

  location / {
    proxy_set_header Host $http_host;
    auth_basic "Login with Company passwd";
    auth_basic_user_file /etc/nginx/.htpasswd;
    proxy_pass http://prom;
  }

  location /grafana/ {
    rewrite  ^/grafana/(.*)  /$1 break;
    auth_basic "Login with Company passwd";
    auth_basic_user_file /etc/nginx/.htpasswd;
    proxy_set_header Host $http_host;
    proxy_pass http://grafana;
  }

  # Proxy Grafana Live WebSocket connections.
  location /grafana/api/live/ {
    rewrite  ^/grafana/(.*)  /$1 break;
    # turn basic auth off if you have problems
    auth_basic "Login with Company passwd";
    auth_basic_user_file /etc/nginx/.htpasswd;
    # turn basic auth off if you have problems
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection $connection_upgrade;
    proxy_set_header Host $http_host;
    proxy_pass http://grafana;
  }

}

```
