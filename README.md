# Docker for Magento 1 Extension Development

## Tl;dr How do I use this?

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
_nginx_, _php_, _mysql_, _fs_. So just run `docker-compose up`.

## How should I access the web server?

In Docker, the exposed ports run on the Docker host. If you're using
`boot2docker`, you can get the `ip` with `boot2docker ip`. The included
`browse` command should be a shortcut for OS X users.

## How do I get to my data?

How Docker actually houses live data is a little confusing, particularly if
you're viewing it from a workstation instead of the Docker daemon host, where
the volume actually resides. It might help to review
[Docker's own documentation](https://docs.docker.com/userguide/dockervolumes/)
on the subject. Anyway, the tl;dr version is _that's what the file share
container is for_.

### How do I use the file share container?

The file share container creates a CIFS share for the Magento directory.

#### OS X

```sh
mkdir -p <mountpoint>
mount_smbfs -N //guest@<docker host ip>/magento_data_1 <mountpoint>
```

#### Windows

```
net use <drive letter>: \\guest@<docker host ip>\magento_data_1
```

#### Linux

Similar to the OS X one, probably uses `mount -t cifs`. I didn't try it.
Alternatively, you can run docker directly on the linux machine and access
its volumes directly.

(The above commands assume the file share container name is `magento_data_1`.
That will be true if you work in a directory named `magento`. For how to make
this work with other directory names, see [my note on container names](https://github.com/kojiromike/docker-magento/tree/master/tools#note-container-names) over at the tools README.)

## Known issues

- Speed: The CIFS share is a little slow. I tried to set up an NFS share, but
couldn't get it working. Taking pull requests for faster shares.
- Disappearing data: Don't panic - if you try something like `docker cp` or
`docker export` on the _data_ container it will appear unchanged. The data is
safe (in fact, the data is still on the host machine even if you delete the
container, as long as you don't `docker rm -v` it.) Try something like

```
docker run --volumes-from magento_data_1 debian tar x /srv/magento > export.tar
```

to get a snapshot of your data. (Although it might be easier just to use the
share.)
