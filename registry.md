Based on official tutorial from docker [Deploying a registry server](https://docs.docker.com/registry/deploying/).

Even Registry does not belong to swarm is useful to have isolated private image containers.

Create a folder /home/docker/auth and inside this folder create a file htpasswd.

This file manage the authentication. Every line represents an user. To create every line append the output of htpasswd command

```
htpasswd -Bbn user password
```

Replace user and password for whatever you define

Also create SSL certs using letsencrypt. Visit [Certbot official page](https://certbot.eff.org/) to get your certificates. And remember to merge the certs

> If you have any problems, take a look to [this script](https://gist.github.com/ma0c/ccde47b8e92bc42d0f7aaec48f455a33) that work like a charm

Docker images will be stored on `/var/lib/registry` in the container, so feel free to map a local volume, in this case `/home/docker/registry`

```
docker run -d -p 5000:5000 \
  --restart=always \
  --name registry   \
  -v /home/docker/auth:/auth \
  -e REGISTRY_AUTH="htpasswd"   \
  -e REGISTRY_AUTH_HTPASSWD_REALM="Registry Realm"   \
  -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd   \
  -v /etc/letsencrypt/live/registry.example.com:/certs   \
  -v /home/docker/registry:/var/lib/registry   \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/registry.example.com.crt   \
  -e REGISTRY_HTTP_TLS_KEY=/certs/registry.example.com.key   \
  registry:2
```
