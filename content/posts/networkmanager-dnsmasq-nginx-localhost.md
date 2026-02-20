+++
title = "NetworkManager dnsmasq - NGINX + Localhost"
date = "2021-01-08"
description = "Using NetworkManager's dnsmasq plugin with NGINX on the localhost."
+++

This post serves as an overview of my configs for using domains with my local NGINX instance with SSL/HTTPS.

# Forward DNS requests to localhost

Create a dnsmasq conf:

**/etc/NetworkManager/dnsmasq.d/lh.conf**

```ini
address=/lh/127.0.0.1
```

Accessing **domain**.lh will be forwarded to localhost for NGINX to handle.

# Setup a Trusted SSL Certificate

I use a self-signed cert so that my localhost pages get a nice **Connection secure** padlock.

Create `/etc/nginx/ssl`

## OpenSSL Config

**lh.cnf**

```ini
[req]
default_bits = 2048
distinguished_name = req_distinguished_name
prompt = no

[req_distinguished_name]
C = CA
ST = Ontario
L = Ottawa
O = Localhost CA
OU = Development
CN = lh

[v3_ca]
subjectAltName = @alt_names

[alt_names]
DNS.1 = hugo.lh
DNS.2 = startpage.lh
```

This alt_names contains all the subdomains you'd like to use and be valid for this cert.

## Root Certification Authority

I create a RootCA for signing my cert. This RootCA can later be added to the system so that Firefox automatically trusts the cert without having to accept it manually.

```bash
# Create the private key
$ sudo openssl genrsa -out /etc/nginx/ssl/rootCA.key 2048

# Create the PEM cert
$ sudo openssl req -x509 -new -nodes -key /etc/nginx/ssl/rootCA.key -sha256 -days 3650 -out /etc/nginx/ssl/rootCA.pem
```

## SSL Certificates

```bash
# Create private key and CSR key
$ sudo openssl req -new -sha256 -nodes -newkey rsa:2048 -keyout /etc/nginx/ssl/lh.key -out /etc/nginx/ssl/ lh.csr -config lh.cnf

# Create cert using rootCA
$ sudo openssl x509 -req -in /etc/nginx/ssl/lh.csr -CA /etc/nginx/ssl/rootCA.pem -CAkey /etc/nginx/ssl/rootCA.key -CAcreateserial -out /etc/nginx/ssl/lh.crt -sha256 -days 3650 -extfile lh.cnf -extensions v3_ca
```

## Adding Trusted CA to System

On Arch Linux or Fedora, p11-kit can be used to add the RootCA system-wide.

```bash
$ sudo trust anchor --store /etc/nginx/ssl/rootCA.pem
```

# NGINX

Create the initial NGINX config.

**/etc/nginx/nginx.conf**

```nginx
worker_processes 1;

events
{
    worker_connections 1024;
}

http
{
    include mime.types;
    default_type application/octet-stream;

    sendfile on;
    keepalive_timeout 65;
}
```

## SSL

Configure NGINX to use SSL and redirect http → https.

```nginx
http
{
    ...

    ssl_certificate ssl/lh.crt;
    ssl_certificate_key ssl/lh.key;

    server
    {
        listen 80;
        server_name _;
        return 301 https://$host$request_uri;
    }
}
```

## Proxy Config

Common proxy config to be included.

**/etc/nginx/proxy.conf**

```nginx
proxy_set_header Host $http_host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto $scheme;
```

## Startpage

I use NGINX to host my Startpage locally at startpage.lh.

```nginx
http
{
    ...

    server
    {
        listen 443 ssl;
        server_name startpage.lh;
        root /home/tryton-vanmeer/Code/Startpage/;
    }
}
```

## Hugo

I use hugo.lh to access hugo's server while developing hugo websites.

```nginx
http
{
    ...

    server
    {
        listen 443 ssl;
        server_name hugo.lh;

        location /
        {
            proxy_pass http://localhost:1313;
            include proxy.conf;
        }

        location /livereload
        {
            proxy_pass http://localhost:1313;
            include proxy.conf;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "Upgrade";
        }
    }
}
```

And I run hugo as such: `hugo serve --baseURL http://hugo.lh --appendPort=false --liveReloadPort=443 --buildDrafts`
