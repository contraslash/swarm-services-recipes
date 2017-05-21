# Deploy traefik as reverse proxy

First create a internal docker network

```
docker network create --driver=overlay traefik-network
```

Then Deploy traefik over the network

```
docker service create \
    --name traefik \
    --constraint=node.role==manager \
    --publish 80:80 \
    --publish 443:443 \
    --publish 5808:8080 \
    --mount type=bind,source=/etc/traefik,target=/etc/traefik \
    --mount type=bind,source=/etc/letsencrypt,target=/etc/letsencrypt \
    --mount type=bind,source=/var/run/docker.sock,target=/var/run/docker.sock \
    --network traefik-network \
    traefik \
    --docker \
    --docker.swarmmode \
    --docker.domain=contraslash.com \
    --docker.watch \
    --web
```

In this example we are maping a folder /etc/traefik to the container. This is to allow Automatic HTTPS with Let's Encrypt

The configuration file /etc/traefik/traefik.toml

```
defaultEntryPoints = ["http", "https"]

[entryPoints]
  [entryPoints.http]
  address = ":80"
    [entryPoints.http.redirect]
    entryPoint = "https"
  [entryPoints.https]
  address = ":443"
    [entryPoints.https.tls]

[acme]
email = "info@finantic.co"
storage = "/etc/traefik/acme.json"
entryPoint = "https"
onDemand = true
# onHostRule = true
```

Also remember to add this label to the containers 

```
docker service update <service-name> --label-add traefik.frontend.entryPoints=http,https
```
