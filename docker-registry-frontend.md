# Docker Registry Frontend

## THIS RECIPE IS NOT STABLE

```
docker service create \
  --network traefik-network \
  --env ENV_DOCKER_REGISTRY_HOST=<registry host> \
  --env ENV_DOCKER_REGISTRY_PORT=<registry port> \
  --env ENV_DOCKER_REGISTRY_USE_SSL=1 \
  --label traefik.port=80 \
  --name registry-front \
  konradkleine/docker-registry-frontend:v2
```
