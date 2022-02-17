###########################################################################
#--------------------------------------------------------------------------
# Image Setup
#--------------------------------------------------------------------------
###########################################################################

FROM archlinux:base

# Start as root
USER root

# Generate packman key
RUN pacman-key --init

# Update packman database and dependencies
RUN pacman -Syyu --noconfirm

###########################################################################
# Non-root user
###########################################################################

# Add a non-root user to prevent files being created with root permissions on host machine.
ARG PUID=1000
ENV PUID ${PUID}
ARG PGID=1000
ENV PGID ${PGID}

RUN groupadd -g ${PGID} laradock && \
  useradd -l -u ${PUID} -g laradock -m laradock && \
  usermod -p "*" laradock -s /bin/bash

###########################################################################
# VI
###########################################################################

USER root

RUN pacman -S --noconfirm vi

###########################################################################
# WGET
###########################################################################

USER root

RUN pacman -S --noconfirm wget

###########################################################################
# Gzip
###########################################################################

USER root

RUN pacman -S --noconfirm gzip

###########################################################################
# GIT
###########################################################################

USER root

RUN pacman -S --noconfirm git

###########################################################################
# GCC
###########################################################################

USER root

RUN pacman -S --noconfirm gcc pkgconf libpng libjpeg

###########################################################################
# ZSH
###########################################################################

USER root

RUN pacman -S --noconfirm zsh

RUN wget https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh

RUN sh install.sh

###########################################################################
# Node
###########################################################################

USER root

RUN pacman -S --noconfirm npm yarn

ENV NVM_DIR /home/laradock/.nvm

RUN mkdir -p $NVM_DIR && \
    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash \
        && . $NVM_DIR/nvm.sh \
        && nvm install 10 \
        && nvm install 12 \
        && nvm install 14 \
        && nvm install 16 \
        && nvm use 16

RUN echo "" >> ~/.bashrc && \
    echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.bashrc && \
    echo '[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"  # This loads nvm' >> ~/.bashrc

RUN echo "" >> ~/.bashrc && \
    echo 'export NVM_DIR="/home/laradock/.nvm"' >> ~/.bashrc && \
    echo '[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"  # This loads nvm' >> ~/.bashrc

ENV PATH $PATH:/home/laradock/.node-bin

###########################################################################
# Nginx
###########################################################################

USER root

RUN pacman -S --noconfirm nginx

RUN touch /var/log/messages

COPY nginx/nginx.conf /etc/nginx/

COPY nginx/logrotate /etc/logrotate.d/

###########################################################################
# PHP
###########################################################################

USER root

RUN git clone git://github.com/phpenv/phpenv.git ~/.phpenv

RUN echo 'export PATH="$HOME/.phpenv/bin:$PATH"' >> ~/.bash_profile && \
    echo 'export PATH="$HOME/.phpenv/bin:$PATH"' >> ~/.zshrc

RUN echo 'eval "$(phpenv init -)"' >> ~/.bash_profile && \
    echo 'eval "$(phpenv init -)"' >> ~/.zshrc

RUN $HOME/.phpenv/bin/phpenv rehash

RUN git clone https://github.com/php-build/php-build $($HOME/.phpenv/bin/phpenv root)/plugins/php-build

RUN $HOME/.phpenv/bin/phpenv install 7.4.27

# RUN pacman -S --noconfirm composer

###########################################################################
# Startup script
###########################################################################

ADD ./startup.sh /opt/startup.sh
RUN sed -i 's/\r//g' /opt/startup.sh
CMD ["/bin/bash", "/opt/startup.sh"]

# Set default work directory
WORKDIR /var/www
