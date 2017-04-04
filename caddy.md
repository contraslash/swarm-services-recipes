# Caddy

Caddy is a lightweight web server written in go.

The configuration is simple

We asume that our static html lives in `/var/www/html/site-root`

We also map `/etc/letsencrypt/live/static-html-server` in case we're going to use HTTPS
```
docker service create \
  --name static-html-server \
  --network traefik-network \
  --label traefik.port=2015 \
  --mount=type=bind,src=/var/www/html/site-root,dst=/srv \
  --mount=type=bind,src=/etc/letsencrypt/live/static-html-server,dst=/etc/caddycerts \
  --mount=type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock \
  --env=CADDYPATH=/etc/caddycerts \
  abiosoft/caddy
```
