---
layout: post
title: How to Reverse Proxy Sophos UTM with nginx
---

This is one of those blog posts I fully expect to get around zero hits per year. 

I use [Sophos UTM](https://www.sophos.com/en-us/products/unified-threat-management.aspx)'s
HTML5 remote access components to provide me with remote access to my home lab's
bits and pieces; mostly RDP and SSH. I also want to serve a few other services
over HTTPS from my single home IP, so this requires some form of content switching
reverse proxy. I elected to use [nginx](https://nginx.org/) for this, both for 
ease of use and ease of configuration with [LetsEncrypt](https://letsencrypt.org/)
for free certificates.

For the most part, this works well. I have the following configuration in nginx:

    server {
            listen 80;
            listen [::]:80;
            server_name myremoteaccessdomain.net;

            underscores_in_headers on;

        listen 443 ssl default_server; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/myremoteaccessdomain.net/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/myremoteaccessdomain.net/privkey.pem; # managed by Certbot
        include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot


        if ($scheme != "https") {
            return 301 https://$host$request_uri;
        } # managed by Certbot

        location / {
            # Proxy all requests to the UTM
            proxy_pass https://10.1.2.200/;
        }

    }

This seems to work well, though when initiating a session, an error presents:

![Sophos UTM Websocket Error]({{ site.baseurl }}/assets/2017-10-17-sophos-utm-error.png)

> Error: Websocket Error

After some Fiddler troubleshooting, I noticed some requests for the `wss://` 
protocol. This indicates that the traffic is web sockets traffic. nginx supports
proxying of web sockets traffic with a few extra lines of config to ensure the
critical headers `Upgrade` and `Connection` are passed appropriately:

    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";

My configuration now looks like this:

    server {
            listen 80;
            listen [::]:80;
            server_name myremoteaccessdomain.net;

            underscores_in_headers on;

        listen 443 ssl default_server; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/myremoteaccessdomain.net/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/myremoteaccessdomain.net/privkey.pem; # managed by Certbot
        include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot


        if ($scheme != "https") {
            return 301 https://$host$request_uri;
        } # managed by Certbot

        location / {
            # Proxy all requests to the UTM
            proxy_pass https://10.1.2.200/;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
        }

    }

