# swarm-services-recipes
Recipes for common services to deploy in Docker Swarm

> Al recipes are configured to run with traefik and under this network

```
docker network create --driver=overlay traefik-network
```

Be sure to ommit or create your custom network

List of recipes:

- [Traefik](traefik.md)
- [Wordpress](wordpress.md)
- [Swarm Visualizer](visualizer.md)
- [Drone](drone.md)
- [Caddy](caddy.md)
