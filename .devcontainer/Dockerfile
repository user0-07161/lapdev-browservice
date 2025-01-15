# Base Image
FROM debian:unstable

# Update and install required packages
RUN apt update && apt upgrade -y && apt install -y \
    systemd \
    systemd-sysv \
    openssh-server \
    sudo \
    dbus \
    vim \
    docker.io \
    curl \
    git \
    && apt clean && rm -rf /var/lib/apt/lists/*

# Configure SSH
RUN echo "root:root" | chpasswd && \
    echo "PermitRootLogin yes" >> /etc/ssh/sshd_config && \
    echo "PasswordAuthentication yes" >> /etc/ssh/sshd_config

RUN ln -sf /lib/systemd/systemd /lapdev-guest-agent && \
    chmod +x /lapdev-guest-agent

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
\nUser=dev \
\nGroup=dev \
\n\n[Install] \
\nWantedBy=sysinit.target' > /usr/lib/systemd/system/code-server-lapdev.service

# COPY install_guest_agent.sh /root/install_guest_agent.sh

# Create a non-root user for development
RUN useradd -ms /bin/bash dev && echo "dev:dev" | chpasswd && adduser dev sudo

# Set up systemd for Docker
ENV container docker
STOPSIGNAL SIGRTMIN+3

RUN systemctl set-default multi-user.target

RUN systemctl enable code-server-lapdev

# Set systemd as the entrypoint
CMD ["/lib/systemd/systemd"]
