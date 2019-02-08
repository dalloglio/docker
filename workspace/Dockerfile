ARG PHP_VERSION

FROM laradock/workspace:2.2-${PHP_VERSION}

LABEL maintainer="Ricardo Pires <ricardo.tech@live.com>"

ENV DEBIAN_FRONTEND noninteractive

USER root

###########################################################################
# Laradock non-root user:
###########################################################################

# Add a non-root user to prevent files being created with root permissions on host machine.
ARG PUID=1000
ENV PUID ${PUID}
ARG PGID=1000
ENV PGID ${PGID}

# always run apt update when start and after add new source list, then clean up at end.
RUN apt-get update -yqq && \
  pecl channel-update pecl.php.net && \
  groupadd -g ${PGID} laradock && \
  useradd -u ${PUID} -g laradock -m laradock -G docker_env && \
  usermod -p "*" laradock

###########################################################################
# Set Timezone
###########################################################################

ARG TZ=UTC
ENV TZ ${TZ}

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

###########################################################################
# Node / NVM:
###########################################################################

# Check if NVM needs to be installed
ARG NODE_VERSION=node
ENV NODE_VERSION ${NODE_VERSION}
ENV NVM_DIR /home/laradock/.nvm

# Install nvm (A Node Version Manager)
RUN mkdir -p $NVM_DIR && \
  curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash \
  && . $NVM_DIR/nvm.sh \
  && nvm install ${NODE_VERSION} \
  && nvm use ${NODE_VERSION} \
  && nvm alias ${NODE_VERSION} \
  && ln -s `npm bin --global` /home/laradock/.node-bin

# Wouldn't execute when added to the RUN statement in the above block
# Source NVM when loading bash since ~/.profile isn't loaded on non-login shell
RUN echo "" >> ~/.bashrc && \
  echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.bashrc && \
  echo '[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"  # This loads nvm' >> ~/.bashrc

# Add NVM binaries to root's .bashrc
USER root

RUN echo "" >> ~/.bashrc && \
  echo 'export NVM_DIR="/home/laradock/.nvm"' >> ~/.bashrc && \
  echo '[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"  # This loads nvm' >> ~/.bashrc

# Add PATH for node
ENV PATH $PATH:/home/laradock/.node-bin

# Make it so the node modules can be executed with 'docker-compose exec'
# We'll create symbolic links into '/usr/local/bin'.
RUN find $NVM_DIR -type f -name node -exec ln -s {} /usr/local/bin/node \; && \
  NODE_MODS_DIR="$NVM_DIR/versions/node/$(node -v)/lib/node_modules" && \
  ln -s $NODE_MODS_DIR/npm/bin/npm-cli.js /usr/local/bin/npm && \
  ln -s $NODE_MODS_DIR/npm/bin/npx-cli.js /usr/local/bin/npx

###########################################################################
# YARN:
###########################################################################

USER laradock

ARG YARN_VERSION=latest
ENV YARN_VERSION ${YARN_VERSION}

RUN [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh" && \
  if [ ${YARN_VERSION} = "latest" ]; then \
  curl -o- -L https://yarnpkg.com/install.sh | bash; \
  else \
  curl -o- -L https://yarnpkg.com/install.sh | bash -s -- --version ${YARN_VERSION}; \
  fi && \
  echo "" >> ~/.bashrc && \
  echo 'export PATH="$HOME/.yarn/bin:$PATH"' >> ~/.bashrc

# Add YARN binaries to root's .bashrc
USER root

RUN echo "" >> ~/.bashrc && \
  echo 'export YARN_DIR="/home/laradock/.yarn"' >> ~/.bashrc && \
  echo 'export PATH="$YARN_DIR/bin:$PATH"' >> ~/.bashrc

# Add PATH for YARN
ENV PATH $PATH:/home/laradock/.yarn/bin

###########################################################################
# Check PHP version:
###########################################################################

RUN php -v | head -n 1 | grep -q "PHP ${PHP_VERSION}."

#
#--------------------------------------------------------------------------
# Final Touch
#--------------------------------------------------------------------------
#

USER root

# Clean up
RUN apt-get clean && \
  rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
  rm /var/log/lastlog /var/log/faillog

# Set default work directory
WORKDIR /var/www
