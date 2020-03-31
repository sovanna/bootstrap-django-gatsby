# Bootstrap Django Gatsby for development


The purpose is to mimic almost a production setup:

- custom local domain with SSL certificate *(works with firefox, chrome, safari without them complaining about self-signed certificate)*
- containers for backend (Django) and frontend (Gatsby) run with non-root user
- static files are served from Nginx
- Django is deployed with Gunicorn managed by Supervisord
- the project is using ENVIRONMENT VARIABLE

But it's still a development environement, so:

- Gastby is running through `gatsby develop`
- Django has `DEBUG=True`
- Gunicorn has `reload=True`

Also, here are some actual tools versions used in this project:

- Django 3
- Pypy 3
- Gatsby 2.19
- React 16.12


Note: :guardsman:

- It is just a setup between tools. There is no extra abstraction. The goal is the bootstrap only.
- **The `.env` file is put here on purpose. Don't forget to update the gitignore and remove it on your private project!**
- you can also adapt the setup to your needs (removing gatsby and using nextjs for example)



## Getting Started :snowman:

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See deployment for notes on how to deploy the project on a live system.



## Prerequisites :neckbeard:

What things you need to install the software and how to install them

- **Docker Community Edition (Docker Engine Community)**

    *e.g: Docker Desktop (Mac), Docker Desktop (Windows)*

    [https://docs.docker.com/install/](https://docs.docker.com/install/)

    **You may need to create a dockerhub account to download docker.**

- **docker-compose**

    *I've personally installed `docker-compose` as a container*

    [https://docs.docker.com/compose/install/#install-as-a-container](https://docs.docker.com/compose/install/#install-as-a-container)

- **nginx-proxy**

    *It allows to automatically config upstream in nginx, thus using `docker-compose scale up` your container and never taking care of ports. Also, it manages SSL etc*

    I have make my own `docker-compose` setup using container from `Jason Wilder` [https://github.com/jwilder](https://github.com/jwilder).

    But I've just seen that he has made [https://github.com/nginx-proxy/nginx-proxy](https://github.com/nginx-proxy/nginx-proxy).

    So, basically, my work is not up-to-date, still, it is working well as I use it everyday.

    ```
    cd PATH_TO_YOUR_WORKSPACE/

    git clone https://github.com/sovanna/nginx-proxy \

        && cd nginx-proxy \

        && docker network create nginx-proxy \

        && docker-compose up -d
    ```

    For more information, you can also look:

    - [https://hub.docker.com/r/jwilder/nginx-proxy](https://hub.docker.com/r/jwilder/nginx-proxy)
    - [http://jasonwilder.com/blog/2014/03/25/automated-nginx-reverse-proxy-for-docker/](http://jasonwilder.com/blog/2014/03/25/automated-nginx-reverse-proxy-for-docker/)

- **mkcert**

    *It's a wonderful work allowing us to have SSL on your local machine without your browser crying out loud! :scream:*

    [https://github.com/FiloSottile/mkcert](https://github.com/FiloSottile/mkcert)



## Setup :feet:

### Configuration of the domain project with SSL

```
cd PATH_TO_YOUR_WORKSPACE/nginx-proxy/certs/

mkcert local.bootstrap-django-gatsby.com

mv local.bootstrap-django-gatsby.com.pem local.bootstrap-django-gatsby.com.crt

mv local.bootstrap-django-gatsby.com-key.pem local.bootstrap-django-gatsby.com.key
```

### Configuration of your host

```
sudo vi /etc/hosts
```

add the following line at the end of the file

`127.0.0.1 local.bootstrap-django-gatsby.com`

save and quit!



## Installing :see_no_evil: :fire:

```
git clone https://github.com/sovanna/bootstrap-django-gatsby.git \
    && cd bootstrap-django-gatsby/ \
    && docker-compose -f docker-compose.dev.yml up -d
```

At this point when `docker-compose up` is finished, you can open your favorite browser and try:

`https://local.bootstrap-django-gatsby.com`

You should see a **Welcome to Gatsby!**

`https://local.bootstrap-django-gatsby.com/api`

You should see **Welcome to Django!**



**Now, don't forget to apply default django migration and collect static files**

**Look below to see how!!!**



# In your day-to-day, don't forget to :star:

**Everythings is inside docker container, so every commands should be run inside container**

Some useful command just in case:

### checks container logs (split your terminal)

- `docker-compose -f docker-compose.dev.yml logs -f server`
- `docker-compose -f docker-compose.dev.yml logs -f frontend`
- `docker-compose -f docker-compose.dev.yml logs -f nginx`

    etc

### going inside containers

- `docker-compose -f docker-compose.dev.yml exec server /bin/bash`

    (then cd src because django is inside src folder)

- `docker-compose -f docker-compose.dev.yml exec frontend /bin/bash`

### going inside postgre container

- `docker-compose -f docker-compose.dev.yml exec db psql -h db bootstrap-django-gatsby daisie`

    (password daisie)


### Collect Django static files when needed (one line without entering inside container)

```
docker-compose -f docker-compose.dev.yml exec server /bin/bash -c "cd src && pypy3 manage.py collectstatic"
```

### Django migration (one line without entering inside container)

```
docker-compose -f docker-compose.dev.yml exec server /bin/bash -c "cd src && pypy3 manage.py migrate"
```

### Install a package in server (django) container (you must be root to install a package), here is the one-liner

*for example, installing djangorestframework*

```
docker-compose -f docker-compose.dev.yml exec -u root server /bin/bash -c "pip install djangorestframework && pip freeze > requirements.txt"
```

### Install a package in frontend container (same for Django, you must be root)

```
docker-compose -f docker-compose.dev.yml exec -u root frontend /bin/bash -c "npm install --save redux"
```


# Warnings :boom:


### Careful of pypy container

**you need to use pypy3 command as it's a pypy container, python command does not exist**


### Django is behing Nginx

if needed, update nginx default conf e.g `client_max_body_size` for upload stuff


### supervisorctl (even though I've never used it in development)

supervisord is running on non-root, plus, he is protected by a password.

For example, inside `server` container

```
supervisorctl -c /etc/supervisor/conf.d/supervisord.conf -u ludock -p daisie status all
```

or without going inside container

```
docker-compose -f docker-compose.dev.yml exec server /bin/bash -c "supervisorctl -c /etc/supervisor/conf.d/supervisord.conf -u ludock -p daisie status all"
```
