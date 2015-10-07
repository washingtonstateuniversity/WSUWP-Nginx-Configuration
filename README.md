# WSUWP-Prod Nginx Configuration

This repository maintains the nginx configuration for the WSUWP Platform at Washington State University.

## Configuration Structure

### Standard subdomain at *.wsu.edu

All sites on WSUWP with `*.wsu.edu` domains should have a configuration that handles the following:

* `servername.wsu.edu` and `www.servername.wsu.edu` respond to HTTP port 80 requests and redirect to a forced HTTPS port 443 connection at the canonical `servername.wsu.edu`.
* `www.servername.wsu.edu` responds to HTTPS port 443 and redirects to HTTPS port 443 at the canonical servername.wsu.edu.
* `servername.wsu.edu` responds to HTTPS port 443 and handles all requests.

```
# BEGIN server block for servername.wsu.edu
server {
    listen 80;
    server_name servername.wsu.edu www.servername.wsu.edu;
    return 301 https://servername.wsu.edu$request_uri;
}

server {
    server_name www.servername.wsu.edu;

    include /etc/nginx-wsuwp-common-header.conf;

    ssl on;
    ssl_certificate     /etc/nginx/ssl/servername.wsu.edu.cer;
    ssl_certificate_key /etc/nginx/ssl/servername.wsu.edu.key;

    include /etc/nginx/wsuwp-ssl-common.conf;
    return 301 https://servername.wsu.edu$request_uri;
}

server {
    server_name servername.wsu.edu;

    include /etc/nginx-wsuwp-common-header.conf;

    ssl on;
    ssl_certificate     /etc/nginx/ssl/servername.wsu.edu.cer;
    ssl_certificate_key /etc/nginx/ssl/servername.wsu.edu.key;

    include /etc/nginx/wsuwp-ssl-common.conf;
    include /etc/nginx/wsuwp-common.conf;
}
# END server block for servername.wsu.edu
```

### Standard sub-subdomain at *.*.wsu.edu

All sites on WSUWP with `*.*.wsu.edu` domains should have a configuration that handles the following:

* `subdomain.servername.wsu.edu` responds to HTTP port 80 requests and redirects to a forced HTTPS port 443 connection at `subdomain.servername.wsu.edu`.
* `subdomain.servername.wsu.edu` responds to HTTPS port 443 and handles all requests.
* No `www.subdomain.servername.wsu.edu` is handled.

```
# BEGIN server block for subdomain.servername.wsu.edu
server {
    listen 80;
    server_name subdomain.servername.wsu.edu;
    return 301 https://subdomain.servername.wsu.edu$request_uri;
}

server {
    server_name subdomain.servername.wsu.edu;

    include /etc/nginx/wsuwp-common-header.conf;

    ssl on;
    ssl_certificate     /etc/nginx/ssl/subdomain.servername.wsu.edu.cer;
    ssl_certificate_key /etc/nginx/ssl/subdomain.servername.wsu.edu.key;

    include /etc/nginx/wsuwp-ssl-common.conf;
    include /etc/nginx/wsuwp-common.conf;
}
# END server block for subdomain.servername.wsu.edu
```