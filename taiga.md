# Taiga

Taiga is a agile project management tool written in Django

To deploy your taiga, first you need create a persistent database in postgres.

```sql
create database taiga;
create user taiga_user identified by 'taiga_password';
grant all privileges on database taiga to taiga_user;
```

Then configure initial data using a stand alone container

```
docker run \ 
  -e TAIGA_POSTGRES_DB_NAME=taiga \
  -e TAIGA_POSTGRES_DB_USER=taiga_user \
  -e TAIGA_POSTGRES_DB_PASS=taiga_password \
  -e TAIGA_POSTGRES_DB_HOST=postgres.example.com \
  contraslash/taiga-back
```

Ant the attach to the container 

```
docker exec -it <container_id> /bin/ash
```

And execute migrations

```
python manage.py migrate --noinput
python manage.py loaddata initial_user
python manage.py loaddata initial_project_templates
```

Then you can remove the container and execute as a service

```
docker service create \ 
  --name taiga \
  --network traefik-network \
  --label traefik.port=8000 \
  --env TAIGA_POSTGRES_DB_NAME=taiga \
  --env TAIGA_POSTGRES_DB_USER=taiga_user \
  --env TAIGA_POSTGRES_DB_PASS=taiga_password \
  --env TAIGA_POSTGRES_DB_HOST=postgres.example.com \
  contraslash/taiga-back
```

Remember to expose your subdomain that should be taiga.<your_domain>

Then just execute taiga front

```
docker service create \
  --name taiga-front-dist \
  --network traefik-network \
  --label traefik.port=80 \
  --env TAIGA_FULL_URL=http://taiga.<your_domain>
  contraslash/taiga-front-dist
```
