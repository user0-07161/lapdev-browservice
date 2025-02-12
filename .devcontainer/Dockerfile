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
    lxc \
    curl \
    git \
    wget \
    && apt clean && rm -rf /var/lib/apt/lists/*

RUN wget https://github.com/ttalvitie/browservice/releases/download/v0.9.11.0/browservice-v0.9.11.0-x86_64.AppImage -O browservice.AppImage
RUN chmod +x browservice.AppImage
RUN cp browservice.AppImage /usr/bin/brows
# Configure SSH
RUN echo "root:root" | chpasswd && \
    echo "PermitRootLogin yes" >> /etc/ssh/sshd_config && \
    echo "PasswordAuthentication yes" >> /etc/ssh/sshd_config

# Create the systemd service file for code-server
# COPY install_guest_agent.sh /root/install_guest_agent.sh

# Set up systemd for Docker
ENV container docker
STOPSIGNAL SIGRTMIN+3

RUN echo '[Unit] \
\nDescription=code-server \
\n# Start code-server early in the boot process \
\nDefaultDependencies=no \
\nBefore=sysinit.target \
\n\n[Service] \
\nType=simple \
\nExecStart=/usr/bin/brows --vice-opt-http-listen-addr=0.0.0.0:30000 \
/workspaces/lapdev-systemd \
\nRestart=always \
\nUser=root \
\n\n[Install] \
\nWantedBy=sysinit.target' > /usr/lib/systemd/system/code-server-lapdev.service


RUN systemctl set-default multi-user.target
RUN systemctl enable docker

# Set systemd as the entrypoint
CMD ["/usr/bin/brows"]
