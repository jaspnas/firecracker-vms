FROM scratch
ARG KERNEL_VERSION=""
ARG KERNEL_EXTRA=""
ARG ALPINE_VERSION=""
ARG MIRROR_PREFIX="fi."

ADD build/rootfs-${ALPINE_VERSION}.tar.gz /
ADD build/kernel-${KERNEL_VERSION}.tar.gz /

RUN echo 'root:root' | chpasswd

RUN ln -sf /boot/Kconfig-${KERNEL_VERSION} /boot config-${KERNEL_VERSION} && \
  mkdir -p /usr/src/linux && ln -sf /boot/Kconfig-${KERNEL_VERSION} /usr/src/linux/.config

RUN apk add --no-cache polkit \
    bash-completion \
    command-not-found \
    openssh-server \
    rng-tools \
    nano \
    curl

COPY interfaces /etc/network/
RUN chown root:root /etc/network/interfaces
COPY .bash_profile /root/
