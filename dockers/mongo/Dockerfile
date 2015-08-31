# Base image
FROM ubuntu:14.04
RUN rm /bin/sh && ln -s /bin/bash /bin/sh

#-----------------------------------------------------------------------------------------------------------------------
# Installations
#-----------------------------------------------------------------------------------------------------------------------

# Install MongoDB.
RUN \
  apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 7F0CEB10 && \
  echo 'deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen' > /etc/apt/sources.list.d/mongodb.list && \
  apt-get update && \
  apt-get install -y mongodb-org=2.6.9 && \
  rm -rf /var/lib/apt/lists/*

## Install supervisor
RUN apt-get install -y python-setuptools inotify-tools \
    && easy_install supervisor

#-----------------------------------------------------------------------------------------------------------------------
# Configuration
#-----------------------------------------------------------------------------------------------------------------------

## Configure supervisor
#ADD settings/supervisord.conf /etc/supervisord.conf

# Define mountable directories.
VOLUME ["/data/db"]

# Define working directory.
WORKDIR /data

#-----------------------------------------------------------------------------------------------------------------------
# Configure log folders
#-----------------------------------------------------------------------------------------------------------------------

RUN mkdir -p /data/db
RUN mkdir -p -p /var/log/supervisor

# Define default command.
CMD ["supervisord", "-c", "/etc/supervisord.conf"]

# Expose ports.
#   - 27017: process
#   - 28017: http
EXPOSE 27017
EXPOSE 28017