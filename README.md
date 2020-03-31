# Bootstrap Django Gatsby for development




## Getting Started :snowman:

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See deployment for notes on how to deploy the project on a live system.



## Prerequisites :neckbeard:

What things you need to install the software and how to install them

- Docker Community Edition (Docker Engine Community)

    *e.g: Docker Desktop (Mac), Docker Desktop (Windows)*

    [https://docs.docker.com/install/](https://docs.docker.com/install/)

    **You may need to create a dockerhub account to download docker.**

- docker-compose

    *I've personally installed `docker-compose` as a container*

    [https://docs.docker.com/compose/install/#install-as-a-container](https://docs.docker.com/compose/install/#install-as-a-container)

- nginx-proxy

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

- mkcert

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

