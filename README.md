# NPXM (nginx, PHP, Xdebug, MariaDB)

![docker](https://img.shields.io/badge/Docker-compose-brightgreen.svg)  
![nginx](https://img.shields.io/badge/nginx-latest-brightgreen.svg)  
![php](https://img.shields.io/badge/PHP_FPM-latest-brightgreen.svg)  
![xdebug](https://img.shields.io/badge/Xdebug-latest-brightgreen.svg)  
![mariadb](https://img.shields.io/badge/MariaDB-latest-brightgreen.svg)  
![phpMyAdmin](https://img.shields.io/badge/phpMyAdmin-latest-brightgreen.svg)

I created this Docker Compose setup for software developers who want to use the latest software similar to Fedora Linux.  
I found lots of specialised Docker files, fixed version numbers, undocumented stuff and nothing really suitable for local PHP development.

Main goals:

- ✅ Simple environment with Nginx, PHP, Xdebug and MariaDB
- ✅ phpMyAdmin for easy database import and exports
- ✅ Accessible and documented configuration files
- ✅ Working Xdebug
- ✅ Colorful bash (`PS1`) , including some tweaks
   - `ll` = `ls -lah`
   - `..` = `cd ..`
   - `~/fix-permissions.sh` to set permissions to `1000:www-data`

- ✅ Useful extensions and tools like `zip`, `git`, `gd`, `vim`, `opcache`
- ✅ Working `$_SERVER['PATH_INFO']` for `nginx` (e.g.: [https://localhost/foo.php/any/path](https://localhost/foo.php/any/path))

I know that using `latest` is not the best practice in the Docker world because you want to ensure a working version. I want to make sure that I don't have to pull up versions every week and that my code is working with the latest software.

## How it works

Put all your HTML content into the `/app/web` folder, edit the `.env` file (database name, passwords etc.) and run

```bash
docker compose up
```

The first time takes much longer because my `php-fpm` has to be build first.

Afterwards you can visit the website on [http://localhost](http://localhost). `phpMyAdmin` is available as [http://localhost:4000](http://localhost:4000)

To enable Xdebug, install a browser plugin to trigger step debugging. I use Firefox and `Xdebug Starter`, `IDE key` has to match your IDE configuration, or just leave everything blank.

## Configuration

### Docker Environment (`.env`)

Use this file to set up your docker environment variables. At the moment it's only used for the database, such as database name, user, password, and root password.

### nginx (`config/nginx.conf`)

If you need a web root folder without hidden files like `/app/vendor`, you can change `root /app/web;` to `root /app;`

_TL;DR_: Everything should be in the /app folder to make development easier.  
PHP-fpm uses `/var/www/html`  by default, nginx `/usr/share/nginx/www` and assumes PHP scripts are in `/scripts`, the local environment is something like `/home/user/...` Usually navigating in different Docker shells is chaotic and mapping IDE folders and Xdebug isn't easy. I simplified it.  
Anyway, in the past there were "only" HTML and PHP files, today it can be anything. So I think `/app` and `/app/web` is okay and easy to search and replace - in case of need.

### PHP (`config/php-overrides.ini`)

These are various settings (common `PHP`, `Xdebug`, `OPcache`). If you miss something, please send a request. If it's reasonable and universal, I will update it... and support is very welcome ❤️

### php-fpm permissions script (`config/fix-permissions.sh`)

This script is mounted as `/root/fix-permissions.sh` and can be used to update the permissions and user/groups of the `/app` directory. Using `sudo chown/chmod ...` on the host system was a mess and the `www-data` user/group id might differ on the host.
If you have any better solution, please let me know.

### MariaDB import (`config/initdb.d/`)

You can put import files into this directory which is mapped to [`/docker-entrypoint-initdb.d`](https://hub.docker.com/_/mariadb/). Shell scripts and (compressed) SQL files will be executed when you start the container the first time.

## Docker php-fpm image (`docker-images/php-fpm`)

This docker image is based on the latest `php:fpm. It includes many PHP extensions, composer, git, ~/fix-permissions.sh, and some more.

After starting `docker compose up` you can use something like this to connect to it's bash

```bash
docker exec -it $(docker ps --filter name=-php-fpm --latest --quiet) bash
```

If you want less/more extensions, please update the php-fpm Dockerfile and test the commands in the container beforehand. To restart the php-fpm container you can use

```bash
docker restart $(docker ps --filter name=-php-fpm --latest --quiet)
```

To rebuild the image, use

```bash
docker compose up --build
```
