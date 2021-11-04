FROM jaspnas/alpine-base:ALPINE_VERSION

RUN apk update && \
    apk add docker

ENV DOCKER_COMPOSE_VERSION=1.23.2
RUN curl -sSL https://github.com/docker/compose/releases/download/${DOCKER_COMPOSE_VERSION}/docker-compose-Linux-x86_64 > /usr/local/bin/docker-compose && \
    chmod +x /usr/local/bin/docker-compose
