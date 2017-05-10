# Rancher

Rancher is an powerful orchestration tool that allow control over a set of repositories easilly

To use rancher

```
docker service create \
    --name orchestration \
    --network traefik-network \
    --label traefik.port=8080 \
    --label traefik.frontend.entryPoints="http,https" \
    rancher/server
```
