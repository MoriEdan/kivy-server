# Kivy Server

## Quickly deploy changes

Make sure to also [configure Matomo](#configure-matomo),
if the `piwik` container has changed.

```shell
ssh root@kivy.org
cd ~/docker/kivy-server
git pull
docker-compose build
docker-compose up -d
```

## Usage

These are some of the most common tasks that are performed by maintainers.

You can connect directly to the `downloads` container to maintain
the directory served at https://kivy.org/downloads/.

```shell
ssh -p 2458 root@kivy.org
cd /web/downloads
```

Connect to the host to manage the server.

```shell
ssh root@kivy.org
```

Keep the host up to date.

```shell
apt-get update
apt-get dist-upgrade
```

The server repository is located at `~/docker/kivy-server`.
Secrets are kept in the `.env` folder, and they are referenced
by services in `docker-compose.yml` and certain Dockerfiles.

```shell
cd ~/docker/kivy-server
```

Pull changes from GitHub.

```shell
git pull
```

Images and the containers created from them can be referenced
by their service names.

```shell
# list services declared in `docker-compose.yml`
docker-compose config --services
```

Build the images.

```shell
# build new/changed images
docker-compose build
# build all images without using the cache
docker-compose build --no-cache
# build a specific image
docker-compose build nginx
```

Create containers from updated or new images and start all services.
Use this command to deploy changes.

```shell
docker-compose up -d
```

Stop and remove containers and networks.

```shell
docker-compose down
```

Start existing containers.

```shell
docker-compose start
# start a specific container
docker-compose start nginx
```

Stop running containers.

```shell
docker-compose stop
# stop a specific container
docker-compose stop nginx
```

Inspect the logs.

```shell
# list logs for all containers
docker-compose logs
# follow logs for all containers
docker-compose logs -f
# follow logs for a specific container
docker-compose logs -f nginx
# follow logs starting from the last 100 lines
docker-compose logs -f --tail 100 nginx
```

Get a shell in a running container. If `bash` is not available in the container,
use `sh`.

```shell
docker-compose exec docs bash
```

Start, stop and restart the Docker service.

```shell
service docker start
service docker stop
service docker restart
```

#### Configure Matomo

The `piwik` container
[needs manual configuration](https://github.com/matomo-org/matomo/issues/10257)
whenever it is recreated. If Matomo is not configured,
visitor logs will not be kept. Visit https://pw.kivy.org after the container
has started and follow the installation steps.

These are the two installation steps that require further action,
besides clicking Next.

Database Setup:

* Database Server: `piwik_mysql`
* Login: `root`
* Password: see the `MYSQL_ROOT_PASSWORD` variable in `.env/envfile/piwik-mysql`
* Database Name: `piwik`
* Table Prefix: `piwik_`


Creating the Tables:

Make sure to **reuse the existing tables**, otherwise they will be overwritten.

The installation is completed when you are redirected
to the authentication form.

## Troubleshoot low disk space

Inspect disk space usage of the host.

```shell
df -h
```

Inspect disk space usage of Docker.

```shell
# overview
docker system df
# verbose
docker system df -v
```

Inspect a folder within a running container.

```shell
docker-compose exec downloads bash
# check directory size
du -h -s /web/downloads
```

#### Clear Docker data

Check out the [docs](https://docs.docker.com/config/pruning/)
to learn more about the commands listed below.

Remove stopped containers.

```shell
docker container prune
```

Remove images that are not used by existing containers.

```shell
docker image prune -a
```

Remove volumes that are not used by existing containers.
**WARNING! Persistent data is stored in volumes.**

```shell
docker volume prune
```

Remove networks that are not used by existing containers.

```shell
docker network prune
```

Shortcut for all of the above.

```shell
docker system prune --volumes
```
