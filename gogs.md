This asume you have a git repositories folder in `/home/git/gogs-repositories`

docker service create \
    --name gogs\
    --network traefik-network \
    --label traefik.port=3000 \
    --publish 22:22 \
    --mount=type=bind,src=/home/git/gogs-repositories,dst=/data \
    --mount=type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock \
    gogs/gogs
