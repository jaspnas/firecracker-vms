VERSION?=3.15.0
REVISION?=$(shell date --rfc-3339=date)
TAG?=${VERSION}-${REVISION}
REGISTRY?=jaspnas
KERNEL_BUILDER_IMAGE=jaspnas/kernel-builder:gcc-10
UID_GID=$(shell id -u):$(shell id -g)
MIRROR_PREFIX?=fi
KERNEL_VERSION=5.15

builddir:
	rm -r $(shell pwd)/build
	mkdir $(shell pwd)/build

all: build
rootfs: build/rootfs-$(VERSION).tar.gz
build/rootfs-$(VERSION).tar.gz:
	# Create new alpine linux filesystem
	touch $(shell pwd)/build/rootfs-${VERSION}.tar.gz && wget https://dl-cdn.alpinelinux.org/alpine/v3.14/releases/x86_64/alpine-minirootfs-${VERSION}-x86_64.tar.gz -O $(shell pwd)/build/rootfs-${VERSION}.tar.gz

kernel: build/kernel-$(KERNEL_VERSION).tar.gz
build/kernel-$(KERNEL_VERSION).tar.gz:
	docker run -it \
		-v $(shell pwd)/build:/build \
		-v $(shell pwd)/Kconfig-${KERNEL_VERSION}:/tmp/.config \
		${KERNEL_BUILDER_IMAGE} /bin/bash -c "\
			git fetch --tags && git checkout v${KERNEL_VERSION} && \
			make clean && make mrproper && cp /tmp/.config . && \
			make EXTRAVERSION=${KERNEL_EXTRA} LOCALVERSION= olddefconfig && \
			make EXTRAVERSION=${KERNEL_EXTRA} LOCALVERSION= oldconfig && \
			make EXTRAVERSION=${KERNEL_EXTRA} LOCALVERSION= -j32 && \
			make EXTRAVERSION=${KERNEL_EXTRA} LOCALVERSION= modules_install && \
			cp vmlinux /boot/vmlinux-${KERNEL_VERSION}${KERNEL_EXTRA} && \
			ln -s /boot/vmlinux-${KERNEL_VERSION}${KERNEL_EXTRA} /boot/vmlinux && \
			cp .config /boot/Kconfig-${KERNEL_VERSION}${KERNEL_EXTRA} && \
			tar -czf /build/kernel-${KERNEL_VERSION}${KERNEL_EXTRA}.tar.gz /boot /lib/modules && \
			chown ${UID_GID} /build/kernel-${KERNEL_VERSION}${KERNEL_EXTRA}.tar.gz"

kshell:
	docker run -it \
		-v $(shell pwd)/build:/build \
		-v $(shell pwd)/Kconfig-${KERNEL_VERSION}:/tmp/.config \
		${KERNEL_BUILDER_IMAGE} /bin/bash

update-kconfig:
	docker run -it \
			-v $(shell pwd)/build:/build \
			-v $(shell pwd )/Kconfig-${KERNEL_VERSION}:/tmp/.config \
			${KERNEL_BUILDER_IMAGE} /bin/bash -c "\
		git fetch --tags && git checkout v${KERNEL_VERSION} && \
		make clean && make mrproper && cp /tmp/.config . && \
		make EXTRAVERSION=${KERNEL_EXTRA} LOCALVERSION= olddefconfig && \
		cp .config /tmp/.config"

build: builddir rootfs kernel
	docker build --build-arg KERNEL_VERSION=${KERNEL_VERSION} \
	--build-arg KERNEL_EXTRA=${KERNEL_EXTRA} \
	--build-arg ALPINE_VERSION=${VERSION} \
	--build-arg MIRROR_PREFIX=${MIRROR_PREFIX} \
	-t ${REGISTRY}/alpine-base:${TAG} .

push-latest:
	docker tag ${REGISTRY}/alpine-base:${TAG} ${REGISTRY}/alpine-base:${VERSION}
	docker push ${REGISTRY}/alpine-base:${VERSION}
ifeq ($(VERSION),3.14.2)
	docker tag ${REGISTRY}/alpine-base:${TAG} ${REGISTRY}/alpine-base:latest
	docker push ${REGISTRY}/alpine-base:latest
endif

clean:
	umount /tmp/alpine-rootfs && rm -r /tmp/alpine-rootfs
