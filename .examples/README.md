# Examples section

In this subfolders are some examples how to use the docker image. There are two sections:
 
 * [`dockerfiles`](https://github.com/nextcloud/docker/tree/master/.examples/dockerfiles)
 * [`docker-compose`](https://github.com/nextcloud/docker/tree/master/.examples/docker-compose)

The `dockerfiles` are derived images, that add or alter certain functionalities of the default docker images. In the `docker-compose` subfolder are examples for deployment of the application, including database, redis, collabora and other services.

## Dockerfiles
The Dockerfiles use the default images as base image and build on top of it.


Example | Description
------- | -------
[cron](https://github.com/nextcloud/docker/tree/master/.examples/dockerfiles/cron) | uses supervisor to run the cron job inside the container (so no extra container is needed).
[imap](https://github.com/nextcloud/docker/tree/master/.examples/dockerfiles/imap) | adds dependencies required to authenticate users via imap
[smb](https://github.com/nextcloud/docker/tree/master/.examples/dockerfiles/smb) | adds dependencies required to use smb shares





## docker-compose
In `docker-compose` additional services are bundled to create a complete nextcloud installation. The examples are designed to run out-of-the-box.
Before running the examples you have to modify the `db.env` and `docker-compose.yml` file and fill in your custom information.

The docker-compose examples make heavily use of dereived Dockerfiles to add configuration files into the containers. This way they should also work on remote docker systems as _Docker for Windows_. When running docker-compose on the same host as the docker daemon, another possibility would be to simply mount the files in the volumes section in the `docker-compose.yml` file.


### insecure
This example should only be used for testing on the local network because it uses a unencrypted http connection.
When you want to have your server reachable from the internet adding HTTPS-encryption is mandatory!
For this use one of the [with-nginx-proxy](#with-nginx-proxy) examples.

To use this example complete the following steps:

1. if you use mariadb or mysql choose a root password for the database in `docker-compose.yml` behind `MYSQL_ROOT_PASSWORD=`
2. choose a password for the database user nextcloud in `db.env` behind `MYSQL_PASSWORD=` (for mariadb/mysql) or `POSTGRES_PASSWORD=` (for postgres)
3. run `docker-compose build --pull` to pull the most recent base images and build the custom dockerfiles
4. start nextcloud with `docker-compose up -d`


If you want to update your installation to a newer version of nextcloud, repeat the steps 3 and 4.


### with-nginx-proxy
The nginx proxy adds a proxy layer between nextcloud and the internet. The proxy is designed to serve multiple sites on the same host machine.
The advantage in adding this layer is the ability to add a container for [Let's Encrypt](https://letsencrypt.org/) certificate handling.
This combination of the [jwilder/nginx-proxy](https://github.com/jwilder/nginx-proxy) and [jrcs/docker-letsencrypt-nginx-proxy-companion](https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion) containers creates a fully automated https encryption of the nextcloud installation without worrying about certificate generation, validation or renewal.

To use this example complete the following steps:

1. open `docker-compose.yml`
   1. insert your nextcloud domain behind `VIRTUAL_HOST=`and `LETSENCRYPT_HOST=`
   2. enter a valid email behind `LETSENCRYPT_EMAIL=`
   3. if you use mariadb or mysql choose a root password for the database behind `MYSQL_ROOT_PASSWORD=`
2. choose a password for the database user nextcloud in `db.env` behind `MYSQL_PASSWORD=` (for mariadb/mysql) or `POSTGRES_PASSWORD=` (for postgres)
3. run `docker-compose build --pull` to pull the most recent base images and build the custom dockerfiles
4. start nextcloud with `docker-compose up -d`


If you want to update your installation to a newer version of nextcloud, repeat the steps 3 and 4.
