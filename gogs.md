This asume you have a git repositories folder in `/home/git/gogs-repositories`
```
docker service create \
    --name git\
    --network traefik-network \
    --label traefik.port=3000 \
    --publish 10022:22 \
    --mount=type=bind,src=/var/gogs,dst=/data \
    gogs/gogs
```

If you have a previous repositories or configuration, `/var/gogs` should have the next folder structure 
```
/var/gogs
|-- git
|   |-- gogs-repositories
|-- ssh
|   |-- # ssh public/private keys for Gogs
|-- gogs
    |-- conf
    |-- data
    |-- log
```

Taked from [here](https://github.com/gogits/gogs/tree/master/docker)
