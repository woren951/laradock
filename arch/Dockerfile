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

# USER root

# RUN pacman -S --noconfirm base-devel re2c

# RUN wget https://www.php.net/distributions/php-7.4.27.tar.gz

# RUN tar -xf php-7.4.27.tar.gz

# RUN ./php-7.4.27/configure \
#     --prefix=/usr/local/php/php7.4 \
#     # --with-zlib-dir \
#     # --with-freetype-dir \
#     # --enable-mbstring \
#     # --with-libxml-dir=/usr \
#     # --enable-soap \
#     # --enable-calendar \
#     # --with-curl \
#     # --with-mcrypt \
#     # --with-zlib \
#     # --with-gd \
#     # --disable-rpath \
#     # --enable-inline-optimization \
#     # --with-bz2 \
#     # --with-zlib \
#     # --enable-sockets \
#     # --enable-sysvsem \
#     # --enable-sysvshm \
#     # --enable-pcntl \
#     # --enable-mbregex \
#     # --with-mhash \
#     # --enable-zip \
#     # --with-pcre-regex \
#     # --with-mysql \
#     # --with-pdo-mysql \
#     # --with-mysqli \
#     # --with-jpeg-dir=/usr \
#     # --with-png-dir=/usr \
#     # --enable-gd-native-ttf \
#     --with-openssl \
#     --with-fpm-user=http \
#     --with-fpm-group=http \
#     --with-gettext \
#     --enable-fpm

# RUN make

# RUN make install

# RUN rm php-7.4.27.tar.gz

# RUN rm -rf php-7.4

# RUN pacman -S --noconfirm composer

###########################################################################
# Startup script
###########################################################################

ADD ./startup.sh /opt/startup.sh
RUN sed -i 's/\r//g' /opt/startup.sh
CMD ["/bin/bash", "/opt/startup.sh"]

# Set default work directory
WORKDIR /var/www
