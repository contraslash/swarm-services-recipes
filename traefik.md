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
    --publish 80:80 --publish 8080:8080 \
    --mount type=bind,source=/var/run/docker.sock,target=/var/run/docker.sock \
    --network traefik-network \
    traefik \
    --docker \
    --docker.swarmmode \
    --docker.domain=example.com \
    --docker.watch \
    --web
```
