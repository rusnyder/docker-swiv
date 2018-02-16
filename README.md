Docker Swiv
===========

[![Docker Build Status](https://img.shields.io/docker/build/rusnyder/swiv.svg)][dockerhub]
[![Docker Image Stats](https://images.microbadger.com/badges/image/rusnyder/swiv.svg)](https://microbadger.com/images/rusnyder/swiv)
[![Docker Pulls](https://img.shields.io/docker/pulls/rusnyder/swiv.svg)][dockerhub]

Tags:

- latest ([Dockerfile](https://github.com/rusnyder/docker-swiv/blob/master/Dockerfile))

[dockerhub]: https://hub.docker.com/r/rusnyder/swiv


What is Swiv?
===================

From the [Swiv Github page](https://github.com/yahoo/swiv):

> Swiv is a web-based exploratory visualization UI for [Druid](https://github.com/druid-io/druid) built on top of [Plywood](https://github.com/implydata/plywood).

How to use?
===========

This is best used as part of a docker-compose.yml file with Druid also being
created.  By default, the container launches the Swiv server with no pre-loaded
configs, but you can also forward any configs that Swiv knows to read via
environment variables.

```shell
docker run -it \
  -e DRUID_BROKER=druid-broker:8082 \
  -p 9090:9090 \
  rusnyder/swiv
```

Alternately, you could add this as a service in a docker-compose file as follows:

```yaml
version: "2"
services:
  ########### Zookeeper ###########
  #
  zookeeper:
    image: confluentinc/cp-zookeeper:latest
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000
    ports:
      - 2181:2181

  ############ Druid #############
  #
  overlord:
    image: rusnyder/druid:latest
    environment:
      DRUID_ZK_HOST: zookeeper:32181
    ports:
      - "8090:8090"
    command:
      - overlord
  coordinator:
    image: rusnyder/druid:latest
    environment:
      DRUID_ZK_HOST: zookeeper:32181
    expose:
      - "8081"
    command:
      - coordinator
  middlemanager:
    image: rusnyder/druid:latest
    environment:
      DRUID_ZK_HOST: zookeeper:32181
      DRUID_PORT:
    expose:
      - "8091"
    command:
      - middleManager
  historical:
    image: rusnyder/druid:latest
    environment:
      DRUID_ZK_HOST: zookeeper:32181
    expose:
      - "8093"
    command:
      - historical
  druid-broker:
    image: rusnyder/druid:latest
    environment:
      DRUID_ZK_HOST: zookeeper:32181
    ports:
      - "8082:8082"
    command:
      - broker

  ############ Druid #############
  #
  swiv:
    image: rusnyder/swiv:latest
    ports:
      - "9090:9090"
    environment:
      DRUID_BROKER: druid-broker:8082
```
