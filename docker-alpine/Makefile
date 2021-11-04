VERSION?=3.14.2
REVISION?=$(shell date --rfc-3339=date)
TAG=${VERSION}-${REVISION}
REGISTRY?=jaspnas
UID_GID=$(shell id -u):$(shell id -g)

all: build
build:
	sed -e "s|ALPINE_VERSION|${VERSION}|g" Dockerfile | \
		docker build -t ${REGISTRY}/docker-alpine:${TAG} -f - .

push-latest:
	docker tag ${REGISTRY}/docker-alpine:${TAG} ${REGISTRY}/docker-alpine:${VERSION}
	docker push ${REGISTRY}/docker-alpine:${VERSION}
ifeq ($(VERSION),3.14.2)
	docker tag ${REGISTRY}/docker-alpine:${TAG} ${REGISTRY}/docker-alpine:latest
	docker push ${REGISTRY}/docker-alpine:latest
endif

push-tagged:
	docker push ${REGISTRY}/docker-alpine:${TAG}
