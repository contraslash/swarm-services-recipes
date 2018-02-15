[Ticketator](https://github.com/sabueso/ticketator) is an open source Ticket Management System created by [Sabueso](https://github.com/sabueso)

We created a image to work ticketator in a swarm configuring TMS with enviromental variables.

Be sure you have static files served by a external static  file server

```
docker service create \
  --name tickets \
  --network traefik-network \
  --label traefik.port="8000" \
  --label traefik.enable=true \
  --label traefik.docker.network=traefik-network \
  --env TICKETATOR_USE_MYSQL=True \
  --env TICKETATOR_MYSQL_DB_NAME=ticketator_db  \
  --env TICKETATOR_MYSQL_DB_USER=ticketator_user \
  --env TICKETATOR_MYSQL_DB_PASS=ticketator_password \
  --env TICKETATOR_MYSQL_DB_HOST=ticketator_db_host \
  --env TICKETATOR_STATIC_URL="http://static-file-server-for-ticketator.com" \
  --env TICKETATOR_SLACK_WEBHOOK_URL="https://hooks.slack.com/services/ADF11111/AAAAA.../111..." \
  --env SLACK_CHANNEL='#general' \
contraslash/ticketator 
```

