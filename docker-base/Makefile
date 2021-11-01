VERSION?=20.04
REVISION?=$(shell date --rfc-3339=date)
TAG=${VERSION}-${REVISION}
REGISTRY?=jaspnas
UID_GID=$(shell id -u):$(shell id -g)

all: build
build:
	sed -e "s|UBUNTU_VERSION|${VERSION}|g" Dockerfile | \
		docker build -t ${REGISTRY}/docker-base:${TAG} -f - .

push-latest:
	docker tag ${REGISTRY}/docker-base:${TAG} ${REGISTRY}/docker-base:${VERSION}
	docker push ${REGISTRY}/docker-base:${VERSION}
ifeq ($(VERSION),20.04)
	docker tag ${REGISTRY}/docker-base:${TAG} ${REGISTRY}/docker-base:latest
	docker push ${REGISTRY}/docker-base:latest
endif

push-tagged:
	docker push ${REGISTRY}/docker-base:${TAG}
