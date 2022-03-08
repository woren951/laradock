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

RUN pacman -S --needed --noconfirm base-devel

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
# GZIP
###########################################################################

USER root

RUN pacman -S --noconfirm gzip

###########################################################################
# GIT
###########################################################################

USER root

RUN pacman -S --noconfirm git

###########################################################################
# PYTHON
###########################################################################

USER root

RUN pacman -S --noconfirm python

###########################################################################
# GO
###########################################################################

USER root

RUN pacman -S --noconfirm go

# ###########################################################################
# # GCC
# ###########################################################################

# USER root

# RUN pacman -S --noconfirm gcc

###########################################################################
# OTHER
###########################################################################

USER root

# RUN pacman -S --noconfirm pkgconf oniguruma diffutils make autoconf bison re2c tidy
RUN pacman -S --noconfirm re2c

###########################################################################
# VIRTUAL:WORLD
###########################################################################

USER root

RUN pacman -S --noconfirm libpng libjpeg gd oniguruma libxslt libzip libmcrypt

###########################################################################
# Non-root user
###########################################################################

USER root

ADD add-aur.sh /root

RUN bash /root/add-aur.sh "laradock" "yay"

RUN yay -Syu --timeupdate

###########################################################################
# ZSH
###########################################################################

USER root

RUN pacman -S --noconfirm zsh

RUN wget https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh

RUN sh install.sh

USER laradock

RUN sh install.sh

###########################################################################
# ASDF
###########################################################################

USER laradock

RUN yay -S asdf-vm

USER root

RUN echo "" >> ~/.bash_profile && \
    echo '# asdf' >> ~/.bash_profile && \
    echo 'source /opt/asdf-vm/asdf.sh' >> ~/.bash_profile

RUN echo "" >> ~/.zshrc && \
    echo '# asdf' >> ~/.zshrc && \
    echo 'source /opt/asdf-vm/asdf.sh' >> ~/.zshrc

ENV PATH="/opt/asdf-vm/bin:$PATH"

###########################################################################
# NODE
###########################################################################

USER root

RUN asdf plugin add nodejs https://github.com/asdf-vm/asdf-nodejs.git

RUN asdf install nodejs 10.24.1 12.22.10 14.19.0 16.14.0

###########################################################################
# PHP
###########################################################################

USER root

RUN asdf plugin-add php https://github.com/asdf-community/asdf-php.git

# RUN asdf install php 7.0.33 7.1.33 7.4.28 8.0.16 8.1.3
# RUN asdf install php 8.1.3

###########################################################################
# NGINX
###########################################################################

USER root

RUN pacman -S --noconfirm nginx

RUN touch /var/log/messages

COPY nginx/nginx.conf /etc/nginx/

COPY nginx/logrotate /etc/logrotate.d/

###########################################################################
# Startup script
###########################################################################

ADD ./startup.sh /opt/startup.sh
RUN sed -i 's/\r//g' /opt/startup.sh
CMD ["/bin/bash", "/opt/startup.sh"]

# Set default work directory
WORKDIR /var/www
