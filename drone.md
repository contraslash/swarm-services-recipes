# Drone for CI/CD

Take a look to [Server Install Guide](http://docs.drone.io/installation/)to understand the requirements

In my case I'm using Drone and MySQL

### Install Server
```
docker service create \
    --name drone\
    --network traefik-network \
    --publish 9051:9000 \
    --label traefik.port="8000" \
    --label traefik.enable=true \
    --label traefik.docker.network=traefik-network\
    --env DRONE_HOST=http://drone.example.com \
    --env DRONE_ADMIN='ma0,ma0' \
    --env DATABASE_DRIVER=mysql \
    --env DATABASE_CONFIG='drone_new_user:new_drone_pass@tcp(db.example.com:3306)/drone08?parseTime=true' \
    --env DRONE_OPEN=true \
    --env DRONE_SECRET=secret_pass_code \
    --env DRONE_GOGS=true \
    --env DRONE_GOGS_URL=https://gogs.example.com \
    drone/drone:0.8-alpine
```

### Install Agent
```
docker service create \
  --name drone-agent\
  --env DRONE_SERVER=drone.example.com:9051 \
  --env DRONE_SECRET=secret_pass_code \
  --mount=type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock \
  drone/agent:0.8-alpine agent
```
