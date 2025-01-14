# Base Image
FROM debian:unstable

RUN apt update

RUN apt install wget curl -y

RUN rm /etc/apt/sources.list.d/debian.sources

RUN echo "deb https://repo.pureos.net/pureos landing main" > /etc/apt/sources.list

RUN curl -O http://repo.pureos.net/pureos/pool/main/p/pureos-archive-keyring/pureos-archive-keyring_2024.04.0_all.deb &&\
    apt install ./pureos-archive-keyring_2024.04.0_all.deb -y

RUN apt update && apt dist-upgrade -y

# Update and install required packages
RUN apt install -y \
    systemd \
    systemd-sysv \
    openssh-server \
    sudo \
    dbus \
    vim \
    docker \
    curl \
    git \
    && apt clean && rm -rf /var/lib/apt/lists/*

# Configure SSH
RUN echo "root:root" | chpasswd && \
    echo "PermitRootLogin yes" >> /etc/ssh/sshd_config && \
    echo "PasswordAuthentication yes" >> /etc/ssh/sshd_config

# Create a non-root user for development
RUN useradd -ms /bin/bash dev && echo "dev:dev" | chpasswd && adduser dev sudo

# Set up systemd for Docker
ENV container docker
STOPSIGNAL SIGRTMIN+3

RUN systemctl set-default multi-user.target

# Set systemd as the entrypoint
CMD ["/lib/systemd/systemd"]
