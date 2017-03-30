# Alpine nginx wordpress

Be sure to replace the follow variables:

- `--label traefik.frontend.rule`
- `name`
- `mount src`
- `--env VIRTUAL_HOST`

```
docker service create \
  --label traefik.port=80 \
  --label traefik.frontend.rule=Host:domain.com \
  --network traefik-network \
  --name=domain_name \
  --mount=type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock \
  --mount=type=bind,src=/path/to/your/wordpress/root,dst=/usr/html \
  --env=VIRTUAL_HOST=domain.com \
  yobasystems/alpine-php-wordpress
```
