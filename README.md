[![](https://images.microbadger.com/badges/image/alexandzors/caddy.svg)](https://microbadger.com/images/alexandzors/caddy "Get your own image badge on microbadger.com") [![](https://images.microbadger.com/badges/version/alexandzors/caddy.svg)](https://microbadger.com/images/alexandzors/caddy "Get your own version badge on microbadger.com")
# Caddy
Custom docker image based on [abiosoft/caddy](https://github.com/abiosoft/caddy-docker).

The only differences are that this image is built with the following plugins:

- [git](https://caddyserver.com/docs/http.git)
- [filebrowser](https://caddyserver.com/docs/http.filebrowser)
- [cors](https://caddyserver.com/docs/http.cors)
- [realip](https://caddyserver.com/docs/http.realip)
- [expires](https://caddyserver.com/docs/http.expires)
- [cache](https://caddyserver.com/docs/http.cache)
- [geoip](https://caddyserver.com/docs/http.geoip)
- [nobots](https://caddyserver.com/docs/http.nobots)
- [reauth](https://caddyserver.com/docs/http.reauth)
- [webdav](https://caddyserver.com/docs/http.webdav)
- [cloudflare](https://caddyserver.com/docs/tls.dns.cloudflare) 
- [googlecloud](https://caddyserver.com/docs/tls.dns.googlecloud)
- [login](https://caddyserver.com/docs/http.login)

and telemetry stats are disabled.

> I originally had issues with telemetry timing out causing Caddy to hang on startup so they are disabled.

## Using Cloudflare DNS plugin

When you deploy your docker container you must pass the following env variables in order for the cloudflare plugin to work.

```
-e CLOUDFLARE_EMAIL=<your-cf@email.com>
-e CLOUDFLARE_API_KEY=<your-cf-api-key>
```

```
domain.tld {
    tls {
        dns cloudflare
    }
}
```

> Your Cloudflare API key can be found under your Cloudflare account.

## Using GoogleCloud DNS plugin

Pass the following during deploy:

```
-e GCE_PROJECT=<project_name>
-e GCE_DOMAIN=<domain.tld>
-e GOOGLE_APPLICATION_CREDENTIALS=<credentials> 
~OR~ 
-e GCE_SERVICE_ACCOUNT_FILE=<credentials-json-file>
```

```
domain.tld {
    tls {
        dns googlecloud
    }
}
```

## Example docker-compose file.

```
version: '3.6'
services:
  caddy:
    deploy:
      replicas: 1
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-I", "http://localhost"]
      interval: 1m30s
      timeout: 10s
      retries: 3
      start_period: 40s
    image: alexandzors/caddy
    ports:
      - "81:80"
      - "444:443"
      - "2015:2015"
    volumes:
      - /path/to/Caddyfile:/etc/Caddyfile
      - /path/to/caddy/dir:/root/.caddy
      - /path/to/sites/root:/srv
    environment:
      - ACME_AGREE=true
      - CLOUDFLARE_EMAIL=<CF_EMAIL>
      - CLOUDFLARE_API_KEY=<CF_API_KEY>
```
