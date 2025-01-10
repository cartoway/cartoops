# Cartoops

- Watchtower: update local Docker images from remote repository webhoock, and restart the containers.
- Vector: collects Docker logs containers, send log to OpenObservable.
- OpenObservable: store and logs query.

## Config

Copy the config template and adapt it.
```sh
cp .env.template .env
```

## Watchtower Setup

Allow Github Docker package update to trigger Watchtower.

On Github project, add a Webhook to point to the server secret URL.

On local Nginx, redirect the secret URL to Watchtower API. Adjuste the secret URL.

```
upstream watchtower {
    server localhost:9483;
}

server {
    listen 80;
    server_name watchtower.dev.cartoway.com;

    access_log /var/log/nginx/watchtower.dev.cartoway.com_access.log;
    error_log /var/log/nginx/watchtower.dev.cartoway.com_error.log;

    # Secret URL to be adjusted
    location /____________________SECRET_URL_TO_BE_CHANGED____________________ {
        rewrite (.*) /v1/update  break;
        proxy_pass http://watchtower;
        proxy_http_version 1.1;
        proxy_set_header  Host              $http_host;
        proxy_set_header  X-Real-IP         $remote_addr;
        proxy_set_header  X-Forwarded-For   $proxy_add_x_forwarded_for;
        proxy_set_header  X-Forwarded-Proto $scheme;

        # https://github.com/containrrr/watchtower/blob/main/docs/http-api-mode.md
        proxy_set_header Authorization "Bearer Mie9buzeiW1cahc4xahZee9AhJ1oawae";
    }
}
```
