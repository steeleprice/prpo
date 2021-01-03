# prpo Dockerfile 
# Copyright (c) Amaranthos Labs, LLC. All rights reserved.

# Image we want from the repo: debian 10 - Slim see: https://stackoverflow.com/questions/59794891/how-does-debian-differ-from-debian-slim
FROM debian:10-slim

# Install needed packages and setup non-root user.
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

ARG INSTALL_ESSENTIALS="apt-utils rsync curl wget sudo snapd git-all uidmap apt-transport-https ca-certificates lsb-release gnupg2"
ARG INSTALL_DOTNET_PKGS="dotnet-sdk-3.1 dotnet-sdk-5.0 powershell" 
ARG INSTALL_DEBIAN_PKGS="original-awk \
build-essential \
procps \
lsof \
htop \
net-tools \
fonts-powerline \
psmisc \
unzip \
zip \
less \
lsb-release \
libc6 \
libgcc1 \
libgssapi-krb5-2 \
libicu[0-9][0-9] \
liblttng-ust0 \
libstdc++6 \
locales \
zlib1g \
ncdu \
man-db \
sed \
strace" 

# Enable correct mode for dotnet watch (only mode supported in a container)
ENV DOTNET_USE_POLLING_FILE_WATCHER=true \
    # Skip extraction of XML docs - generally not useful within an image/container - helps performance
    NUGET_XMLDOC_MODE=skip \
    # PowerShell telemetry for docker image usage
    POWERSHELL_DISTRIBUTION_CHANNEL=PSDocker-DotnetSDK-Debian-10 \
    # set apt prompt removal
    DEBIAN_FRONTEND=noninteractive
    
# get Microsoft Repos and
# install utils for setup
RUN apt-get update && apt-get install -y --no-install-recommends $INSTALL_ESSENTIALS \
    && wget https://packages.microsoft.com/config/debian/10/packages-microsoft-prod.deb -O packages-microsoft-prod.deb \
    && dpkg -i packages-microsoft-prod.deb  \
    ### DON'T FORGET TO UPDATE
    && apt-get update

# Create a non-root user to match UID/GID and put a profile in the home folder.
RUN groupadd --gid ${USER_GID} ${USERNAME} \
    && useradd -s /bin/bash --uid ${USER_UID} --gid ${USER_GID} -m $USERNAME \
    && usermod -aG sudo ${USERNAME} 

COPY dotfiles/.* /tmp/dotfiles/
COPY dotfiles/* /tmp/dotfiles/

RUN rsync -a /tmp/dotfiles/ /home/${USERNAME}/dotfiles/ \
    && chown -R ${USERNAME}:${USERNAME} /home/${USERNAME}/dotfiles 

#setup the rest of the environment
RUN apt-get install -y --no-install-recommends \
    $INSTALL_DEBIAN_PKGS \
    $INSTALL_DOTNET_PKGS

# Clean up
RUN apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/* /tmp/dotfiles 

# Set Timezone & locale
RUN echo "America/Phoenix" > /etc/timezone \
    && dpkg-reconfigure -f noninteractive tzdata \
    && echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen \
    && locale-gen

USER ${USERNAME}

WORKDIR /home/$USERNAME/dotfiles

RUN cd /home/${USERNAME}/dotfiles \
    && make default

ENTRYPOINT [ "/bin/bash" ]
