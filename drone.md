# Drone for CI/CD

Take a look to [Server Install Guide](http://readme.drone.io/0.5/install/server/) and [Agent Install Guide](http://readme.drone.io/0.5/setup-agents/) to understand the requirements

I also prefeer use a env-file instead a lot of -e flags

`dronerc` looks like that:
```
DRONE_ADMIN= admin_name, other_admin_name

DATABASE_DRIVER=mysql
DATABASE_CONFIG=user_of_database:password_for_user@tcp(my_db:y_db_port)/drone?parseTime=true

DRONE_OPEN=true
DRONE_SECRET=ultra_scret_phrase

# In my case I use Gogs
DRONE_GOGS=true
DRONE_GOGS_URL=http://gogs.example.com
```

And `droneagentrc` looks like that
```
DRONE_SERVER=ws://drone.example.com/ws/broker
# This must be the same phrase
DRONE_SECRET=ultra_scret_phrase
```

### Install Server
```
docker service create \
    --name drone\
    --network traefik-network \
    --label traefik.port=8000 \
    --mount=type=bind,src=/var/lib/drone,dst=/var/lib/drone \
    --mount=type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock \
    --env-file /etc/drone/dronerc \
    drone/drone:latest
```

### Install Agent
```
docker service create \
  --name drone-agent\
  --env-file /etc/drone/droneagentrc \
  drone/drone:0.5 agent
```
