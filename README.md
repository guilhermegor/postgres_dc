
### REFERENCES

- https://danieldcs.com/postgresql-e-pgadmin-com-docker-compose/



### STARTING SERVICES

* open docker

```bash
(bash)

# optional: check availability of docker CLI
#   check for docker greetings: Hello from Docker!
docker run hello-world
```

* running docker compose

```bash
(bash)

# check previously created data
docker network ls
docker ps
docker images
# run docker compose
docker compose -f postgres_docker-compose.yml up -d
```

* checking network integration between containers

```bash
(bash)

# inspect if both conteiners are integrated through network
docker network inspect postgres_compose_network
# check network connectivity
docker exec -it pgadmin_container ping postgres_container
```

* checking for remaning errors
```bash
(bash)

docker logs <CONTAINER_NAME>
```

### RESTARTING CONTAINER

```bash
(bash)

# dropping all services
docker compose -f postgres_docker-compose.yml down -v --remove-orphans
docker system prune --volumes --force
docker network prune -f
docker image prune -a
# printing docker compose config
docker compose -f postgres_docker-compose --env-file .env config
# run "starting all" services steps
```


### SETTING PERMISSIONS

```bash
(bash)

# error: 
#   2025-01-24 07:25:43 creating configuration files ... ok
#   2025-01-24 07:25:43 2025-01-24 10:25:43.406 UTC [83] FATAL:  data directory "/var/lib/postgresql/data" has invalid permissions
#   2025-01-24 07:25:43 2025-01-24 10:25:43.406 UTC [83] DETAIL:  Permissions should be u=rwx (0700) or u=rwx,g=rx (0750).

chmod -R 0700 ./data/postgres
chown -R $(id -u):$(id -g) ./data/postgres

docker exec -it dcs-postgres bash
chmod 0700 /var/lib/postgresql/data
chmod 0700 ./data/postgres

docker run --rm -it -v $(pwd)/data/postgres:/var/lib/postgresql/data alpine sh
$ (sh) rm -rf /var/lib/postgresql/data/*
$ (sh) exit

# check for permission - ensure the output shows the correct permissions (drwx------ or 0700).
ls -ld ./data/postgres

# ! obs: relative path cah raise errors in windows, opt for absolute paths
```