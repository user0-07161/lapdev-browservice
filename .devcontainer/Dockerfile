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
RUN apt update && apt upgrade -y && apt install -y \
    systemd \
    systemd-sysv \
    openssh-server \
    sudo \
    dbus \
    vim \
    docker.io \
    lxc \
    curl \
    git \
    && apt clean && rm -rf /var/lib/apt/lists/*

# Configure SSH
RUN echo "root:root" | chpasswd && \
    echo "PermitRootLogin yes" >> /etc/ssh/sshd_config && \
    echo "PasswordAuthentication yes" >> /etc/ssh/sshd_config

# Create the systemd service file for code-server
RUN echo '[Unit] \
\nDescription=code-server \
\n# Start code-server early in the boot process \
\nDefaultDependencies=no \
\nBefore=sysinit.target \
\n\n[Service] \
\nType=simple \
\nExecStart=/usr/bin/code-server --auth none --bind-addr 0.0.0.0:30000 --app-name Lapdev --disable-workspace-trust /workspaces/lapdev-systemd \
\nRestart=always \
\nUser=root \
\n\n[Install] \
\nWantedBy=sysinit.target' > /usr/lib/systemd/system/code-server-lapdev.service

# COPY install_guest_agent.sh /root/install_guest_agent.sh

# Set up systemd for Docker
ENV container docker
STOPSIGNAL SIGRTMIN+3

RUN systemctl set-default multi-user.target
RUN systemctl enable docker
RUN systemctl enable code-server-lapdev

# Set systemd as the entrypoint
CMD ["/lib/systemd/systemd"]
