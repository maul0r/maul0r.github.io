---
title: "Installing Matrix Home Server w/ Postgress on Debian 10"
date: 2020-12-29T18:00:00
excerpt: "Basic RHEL8.1 installation, activation, autoupdate, cockpit setup"
categories:
  - blog
tags:
  - debian
  - matrix
  - howto
  - basic
---

wget gnupg2 pwgen

Install Postgress: 
```
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo apt-get update
sudo apt-get -y install postgresql
```

Next install Synapse:
```
curl https://packages.matrix.org/debian/matrix-org-archive-keyring.gpg |sudo dd of=/etc/apt/trusted.gpg.d/matrix-org.gpg
echo "deb https://packages.matrix.org/debian/ buster main" |sudo tee -a /etc/apt/sources.list
sudo apt update && sudo apt install matrix-synapse-py3
```

During the installation a window will pop up, asking for the name of the Matrix Server. As you probably would like your matrix name to be user@domain.com instead user@matrix.domain.com configure your top level domain name here. 

In order to enable passwordless logon to the database the db user needs to be the same name as the matrix-synapse user. Verify that user using ```systemctl show -pUser matrix-synapse.service```. Then create the database like so:

```sudo -u postgres sh -c 'createuser matrix-synapse && createdb -O matrix-synapse -l C.UTF-8 -T template0 matrix'````

Next we configure that Synapse will only start after the db has been loaded. Paste all after first line into the resulting window. This will ensure the system will not become unresponsive due to memory exhaustion. 
```
sudo systemctl edit matrix-synapse.service
[Unit]
After=network-online.target postgresql.service
[Service]
MemoryMax=80%
```

The configuration file for the homeserver is located in ``` /etc/matrix-synapse/homeserver.yml```

The following changes were made: 
```
web_client_location: https://chat.pete.no/
public_baseurl: https://pete.no/
database:
  name: psycopg2
  args:
    user: matrix-synapse
    database: matrix
    host: /run/postgresql
    application_nae : synapse
allow_public_rooms_over_federation: true
filter_timeline_limit: 100
 - port: 8008
    tls: false
    type: http
    x_forwarded: true
    bind_addresses: ['::1', 'local ip']

    resources:
      - names: [client, federation]
        compress: false
admin_contact: 'mailto:webmaster@pete.no'
url_preview_enabled: true
url_preview_ip_range_blacklist:
  - '127.0.0.0/8'
  - '10.0.0.0/8'
  - '172.16.0.0/12'
  - '192.168.0.0/16'
  - '100.64.0.0/10'
  - '169.254.0.0/16'
  - '::1/128'
  - 'fe80::/64'
  - 'fc00::/7'

```

The matrix.conf file on the external reverse proxy: 
```
server {
    listen 443 ssl;
    listen [::]:443 ssl;

    server_name matrix.pete.no;

    ssl_certificate /etc/letsencrypt/live/site-name/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/site-name/privkey.pem; # managed by Certbot
    ssl_trusted_certificate /etc/letsencrypt/live/site-name/chain.pem; # managed by Certbot
    ssl_stapling on; # managed by Certbot
    ssl_stapling_verify on; # managed by Certbot
    add_header Strict-Transport-Security "max-age=31536000" always; # managed by Certbot

    location ~* ^(\/_matrix|\/_synapse\/client) {
        proxy_pass http://internal_ip:8008;
        proxy_set_header X-Forwarded-For $remote_addr;
        # Nginx by default only allows file uploads up to 1M in size
        # Increase client_max_body_size to match max_upload_size defined in homeserver.yaml
        client_max_body_size 50M;
    }

}

server {

    # For the federation port
    listen 8448 ssl;
    listen [::]:8448 ssl;

    server_name pete.no;

    ssl_certificate /etc/letsencrypt/live/base-domain/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/base-domain/privkey.pem; # managed by Certbot
    ssl_trusted_certificate /etc/letsencrypt/live/base-domain/chain.pem; # managed by Certbot
    ssl_stapling on; # managed by Certbot
    ssl_stapling_verify on; # managed by Certbot
    add_header Strict-Transport-Security "max-age=31536000" always; # managed by Certbot

    location /_matrix/ {
        proxy_pass http://interal_ip:8008;
        proxy_set_header X-Forwarded-For $remote_addr;
        # Nginx by default only allows file uploads up to 1M in size
        # Increase client_max_body_size to match max_upload_size defined in homeserver.yaml
        client_max_body_size 50M;
    }

}
```
