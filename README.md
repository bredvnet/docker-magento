# Docker for Magento 1.x Extension Development

## How do I use this?

1. Clone it.
1. Type `docker-compose up -d`.
1. Develop

## How should we build this?

Normally with Magento you get a plain LAMP stack; Apache with `mod_php`.
That's fine, but since Docker containers are so nicely isolated,
I want this approach:

     ---------------------       -------------       -------
    | HTTPD/FastCGI Proxy | <-> | FastCGI PHP | <-> | MySQL |
     ---------------------       -------------       -------
                                        \               /
                                     -----------------------
                                    | Data Volume Container |
                                     -----------------------

Separating the HTTP server from the PHP process gives us a more true-to-form
web architecture where the web application server is distinct from the
web server. It means we can scale and reconfigure the different server layers
independently. It means we can destroy or replace a component without
destroying the other containers or their data.

## What do we need to get started?

1. A container for the HTTPD. We'll build from `nginx` and try to configure it for FastCGI.
1. A container for MySQL. `mysql:5` should do.
1. A container for PHP. We'll use the official Docker PHP images with additional extensions Magento requires.

Docker has a nice tool for orchestrating multiple containers for dev
environments called [Compose](http://docs.docker.com/compose/). I defined a
docker-compose file that builds and connects the aforementioned containers
from its Dockerfile in each of the directories named after the service:
_nginx_, _php_, _mysql_, _data_. So just run `docker-compose up`.

## How should I access the web server?

In Docker, the exposed ports run on the Docker host. If you're using
`boot2docker`, you can get the `ip` with `boot2docker ip`. The included
`browse` command should be a shortcut for OS X users.

## How do I get to my data?

How Docker actually houses live data is a little confusing, particularly if
you're viewing it from a workstation instead of the Docker daemon host, where
the volume actually resides. It might help to review
[Docker's own documentation](https://docs.docker.com/userguide/dockervolumes/)
on the subject.


