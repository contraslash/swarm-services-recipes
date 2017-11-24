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

For debugging purposes, you can add this flag to the container

```
--logLevel=DEBUG
```

Also if you want to secure web dashboard interface, you can first create a password and then use HTTP auth to enter site
[Taked from here](https://www.digitalocean.com/community/tutorials/how-to-use-traefik-as-a-reverse-proxy-for-docker-containers-on-ubuntu-16-04)

```
htpasswd -n admin
```

Then prompt for your secure password and after in /etc/traefik/traefik.toml

```
...
[web]
address = ":8080"
  [web.auth.basic]
  users = ["admin:your_encrypted_password"]
```
