# dokku neo4j (beta)  [![Build Status](https://img.shields.io/travis/dokku/dokku-neo4j.svg?branch=master "Build Status")](https://travis-ci.org/dokku/dokku-neo4j) [![IRC Network](https://img.shields.io/badge/irc-freenode-blue.svg "IRC Freenode")](https://webchat.freenode.net/?channels=dokku)

Unofficial Neo4j plugin for dokku. Currently defaults to installing [Neo4j 2.3.2](https://hub.docker.com/r/thetallgrassnet/alpine-neo4j/).
Forked from the [official Redis plugin for dokku](https://github.com/dokku/dokku-redis).

## requirements

- dokku 0.4.0+
- docker 1.8.x

## installation

```shell
# on 0.3.x
cd /var/lib/dokku/plugins
git clone https://github.com/thetallgrassnet/dokku-neo4j.git neo4j
dokku plugins-install

# on 0.4.x
dokku plugin:install https://github.com/thetallgrassnet/dokku-neo4j.git neo4j
```

## commands

```
neo4j:clone <name> <new-name>  Create container <new-name> then copy data from <name> into <new-name>
neo4j:create <name>            Create a neo4j service with environment variables
neo4j:destroy <name>           Delete the service and stop its container if there are no links left
neo4j:expose <name> [port]     Expose a neo4j service on custom port if provided (random port otherwise)
neo4j:info <name>              Print the connection information
neo4j:link <name> <app>        Link the neo4j service to the app
neo4j:list                     List all neo4j services
neo4j:logs <name> [-t]         Print the most recent log(s) for this service
neo4j:promote <name> <app>     Promote service <name> as NEO4J_URL in <app>
neo4j:restart <name>           Graceful shutdown and restart of the neo4j service container
neo4j:start <name>             Start a previously stopped neo4j service
neo4j:stop <name>              Stop a running neo4j service
neo4j:unexpose <name>          Unexpose a previously exposed neo4j service
neo4j:unlink <name> <app>      Unlink the neo4j service from the app
```

## usage

```shell
# create a neo4j service named lolipop
dokku neo4j:create lolipop

# you can also specify the image and image
# version to use for the service
# it *must* be compatible with the
# official neo4j image
export NEO4J_IMAGE="neo4j"
export NEO4J_IMAGE_VERSION="2.8.21"

# you can also specify custom environment
# variables to start the neo4j service
# in semi-colon separated forma
export NEO4J_CUSTOM_ENV="USER=alpha;HOST=beta"

# create a neo4j service
dokku neo4j:create lolipop

# get connection information as follows
dokku neo4j:info lolipop

# a neo4j service can be linked to a
# container this will use native docker
# links via the docker-options plugin
# here we link it to our 'playground' app
# NOTE: this will restart your app
dokku neo4j:link lolipop playground

# the following environment variables will be set automatically by docker (not
# on the app itself, so they won’t be listed when calling dokku config)
#
#   DOKKU_NEO4J_LOLIPOP_NAME=/lolipop/DATABASE
#   DOKKU_NEO4J_LOLIPOP_PORT=tcp://172.17.0.1:7474
#   DOKKU_NEO4J_LOLIPOP_PORT_7474_TCP=tcp://172.17.0.1:7474
#   DOKKU_NEO4J_LOLIPOP_PORT_7474_TCP_PROTO=tcp
#   DOKKU_NEO4J_LOLIPOP_PORT_7474_TCP_PORT=7474
#   DOKKU_NEO4J_LOLIPOP_PORT_7474_TCP_ADDR=172.17.0.1
#
# and the following will be set on the linked application by default
#
#   NEO4J_URL=http://dokku-neo4j-lolipop:7474/0
#
# NOTE: the host exposed here only works internally in docker containers. If
# you want your container to be reachable from outside, you should use `expose`.

# another service can be linked to your app
dokku neo4j:link other_service playground

# since NEO4J_URL is already in use, another environment variable will be
# generated automatically
#
#   DOKKU_NEO4J_BLUE_URL=http://dokku-neo4j-other-service:7474/0

# you can then promote the new service to be the primary one
# NOTE: this will restart your app
dokku neo4j:promote other_service playground

# this will replace NEO4J_URL with the url from other_service and generate
# another environment variable to hold the previous value if necessary.
# you could end up with the following for example:
#
#   NEO4J_URL=http://dokku-neo4j-other-service:7474/0
#   DOKKU_NEO4J_BLUE_URL=http://dokku-neo4j-other-service:7474/0
#   DOKKU_NEO4J_SILVER_URL=http://dokku-neo4j-lolipop:7474/lolipop

# you can also unlink a neo4j service
# NOTE: this will restart your app and unset related environment variables
dokku neo4j:unlink lolipop playground

# you can tail logs for a particular service
dokku neo4j:logs lolipop
dokku neo4j:logs lolipop -t # to tail

# you can clone an existing database to a new one
dokku neo4j:clone lolipop new_database

# finally, you can destroy the container
dokku neo4j:destroy lolipop
```
