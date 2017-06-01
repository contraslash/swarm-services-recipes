# Setry

Sentry is a crash analytics tool

To run sentry you need multiple services

*redis*
```
docker service create \
  --name sentry-redis \
  --network traefik-network \
  redis:3.2-alpine
```

*postgres*
```
docker service create \
  --name sentry-postgres \
  --network traefik-network \
  --env POSTGRES_PASSWORD=secret \
  --env POSTGRES_USER=sentry \
  postgres:9.5
```

Then generate a secret key 

```
docker run \
  --rm sentry \
  config generate-secret-key
```

Store this key becouse you will need it in few moments

Then we need to configure the db. Like we're not using regular containers and docker swarn doesn't allow interactive console, we need to make a little hack

We're goingo to start a container in the swarm but instead of make an action, we put the container to sleep

```
docker service create \
  --network traefik-network \
  --name sentry-upgrade \
  --env SENTRY_SECRET_KEY="<the secret key from previous step>" \
  --env SENTRY_POSTGRES_HOST=sentry-postgres \
  --env SENTRY_REDIS_HOST=sentry-redis \
  --env SENTRY_DB_USER=sentry \
  --env SENTRY_DB_PASSWORD=secret \
  sentry \
  sleep 3000
```
> Taked from [here](http://phenixdotnet.fr/2016/12/14/installer-sentry-sur-un-cluster-docker-swarm/)

The you have to locate the swarm node that execute this container and use a bash terminal to execute the command update

```
docker exec -it <container id> /bin/bash
/entrypoint.sh upgrade
```

Now execute the web server
```
docker service create \
--network traefik-network \
--env SENTRY_SECRET_KEY="<the secret key from previous step>" \
--env SENTRY_POSTGRES_HOST=sentry-postgres \
--env SENTRY_REDIS_HOST=sentry-redis \
--env SENTRY_DB_USER=sentry \
--env SENTRY_DB_PASSWORD=secret \
--name sentry \
--label traefik.port=9000 \
  sentry \
  run web
```

And the worker 

```
docker service create \
--network traefik-network \
--env SENTRY_SECRET_KEY="<the secret key from previous step>" \
--env SENTRY_POSTGRES_HOST=sentry-postgres \
--env SENTRY_REDIS_HOST=sentry-redis \
--env SENTRY_DB_USER=sentry \
--env SENTRY_DB_PASSWORD=secret \
--name sentry-worker \
  sentry \
  run worker
```

And the cron

```
docker service create \
--network traefik-network \
--env SENTRY_SECRET_KEY="<the secret key from previous step>" \
--env SENTRY_POSTGRES_HOST=sentry-postgres \
--env SENTRY_REDIS_HOST=sentry-redis \
--env SENTRY_DB_USER=sentry \
--env SENTRY_DB_PASSWORD=secret \
--name sentry-cron \
  sentry \
  run worker
```

> If you have an existing Postgres Server you can run the with the following env vars

```
docker service create \
  --network traefik-network \
  --env SENTRY_SECRET_KEY='_nz@*2u1+z4(0)@6*@tyb!b3ik6-uu#(4zl_k!s%x9jtinm-6z'\
  --env SENTRY_REDIS_HOST=sentry-redis \
  --env SENTRY_POSTGRES_HOST=<host of postgres> \
  --env SENTRY_POSTGRES_PORT=5432 \
  --env SENTRY_DB_NAME=<sentry db name> \
  --env SENTRY_DB_USER=<sentry db user> \
  --env SENTRY_DB_PASSWORD=<sentry password> \
  --label traefik.port=9000 \
  --label traefik.frontend.entryPoints=http,https \
  --name sentry \
  sentry \
  run web
  ```
  
## Troubleshooting
  
Databases in swarm could become a pain in the ass and also configuring the first upgrade of sentry. We highly recomend to use a separate db server and configure the db using a stand alone container instead a service.
  
  Something like this
  
  ```
  docker run -it --rm 
    --env SENTRY_SECRET_KEY='<secret-key>' \
    --env SENTRY_REDIS_HOST=sentry-redis \
    --env SENTRY_POSTGRES_HOST=<host of postgres> \
    --env SENTRY_POSTGRES_PORT=5432 \
    --env SENTRY_DB_NAME=<sentry db name> \
    --env SENTRY_DB_USER=<sentry db user> \
    --env SENTRY_DB_PASSWORD=<sentry password> \
    --env SENTRY_REDIS_HOST=<redis host> \
    sentry upgrade
  ```
  
Then execute the services from above.

You can create a temp redis server for this pass and then remove it, replacing REDIS_HOST with a link

First create the temp redis container
```
docker run -d --name sentry-redis redis
```
Then link to the other container
```
docker run -it --rm 
    --env SENTRY_SECRET_KEY='<secret-key>' \
    --env SENTRY_REDIS_HOST=sentry-redis \
    --env SENTRY_POSTGRES_HOST=<host of postgres> \
    --env SENTRY_POSTGRES_PORT=5432 \
    --env SENTRY_DB_NAME=<sentry db name> \
    --env SENTRY_DB_USER=<sentry db user> \
    --env SENTRY_DB_PASSWORD=<sentry password> \
    --link sentry-redis:redis \
    sentry upgrade
```

