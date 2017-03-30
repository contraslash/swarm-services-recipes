# Docker swarm visualizer

```
docker service create \
  --label traefik.port=8080 \
  --network traefik-network \
  --name=swarmviz \
  --constraint=node.role==manager \
  --mount=type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock \
  manomarks/visualizer
```
