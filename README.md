# Configuration

Make a docker-compose.override.yml file and use it to set the ports you want
to use for your services. You can additionally set the environment variables
via env_file: .env

```yaml
services:
  postgres:
    env_file: [.env]
    ports:
      - '4001:5432'
  adminer:
    env_file: [.env]
    ports:
      - '4002:8080'
  hasura:
    env_file: [.env]
    ports:
      - '4003:8080'
```

# Launching

To start the containers, run

```sh
docker compose up -d
```

Then open a browser and point it to the ports you specified in your
docker-compose.override.yml file. For example, if adminer is running on port
4002, then you can access it at http://localhost:4002 and if hasura is running
on port 4003, then you can access it at http://localhost:4003.

# Connecting systems

For all the services in a Docker compose environment, the name of the service is
its hostname. So in order to connect to postgres, you can write just write
postgres.

Example:

```yaml
services:
  aaa:
    image: adminer
  bbb:
    image: postgres
```

In the above example, the adminer service can connect to the postgres service
using the hostname `bbb`.

Since our database services is called `postgres`, we can connect to it using
`postgres` as the hostname.

So in adminer, you can connect to the postgres database via:

```
database: postgres
username: postgres
password: postgres
host: postgres
```

# Volumes

The volumes in the containers are mounted locally do /docker/data/$service .
This is useful so you can destroy the container without losing its data. Try it
by running docker compose down, which will destroy and delete all your
containers. You'll notice the data is still there. When you bring them up with
`docker compose up -d` again, the data will still be there. If you want a fresh
start, you can delete the data folder.

# Pitfalls

## Changing configurations

You cannot change anything in the docker-compose.yml file after a container is
built. If you need to make a change to a docker-compose.yml or
docker-compose.override.yml file, you need to destroy the container and rebuild
it. You can destroy the container via one of the following:

```sh
# Destroy all containers
docker compose down

# Destroy a specific service
docker compose kill <service>
docker compose rm <service>
```

## No such file or directory

If docker complains it can't find the .env file, then it means you specified
a .env file in your docker-compose.override.yml file, but you didn't create it.
Either create an empty .env file, or delete the env_file line from your
docker-compose.override.yml file.
