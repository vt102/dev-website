#
# Dockerfile for a general purpose AWS development environment on Ubuntu.
#
# Why Ubuntu when it is so heavyweight? Well, Alpine's libs are compiled against
# some unorthodox alternative to libc and so whilst we can get CDk to run, we can't
# install AWS CLI v2 and that's not acceptable.
#

# Set Ubuntu version. Default to `latest`.
#
# Override default by supplying `--build-arg UBUNTU_VERSION=<arg value>`
# to `docker build`.
ARG UBUNTU_VERSION="22.04"

# Specify base image.
FROM ubuntu:${UBUNTU_VERSION}

# Recent versions create the ubuntu user as UID:1000. Older versions do not.
# This line will remove that user allowing older and newer versions to operate
# in the same manner.
# RUN touch /var/mail/ubuntu && chown ubuntu /var/mail/ubuntu && userdel -r ubuntu

# Set the locale. Default to `en_AU.UTF-8`.
#
# Override default by supplying `--build-arg LOCALE=<arg value>`
# to `docker build`.
ARG LOCALE="en_US.UTF-8"

# Set the username and user/group IDs that we want containers to run as.
#
# Override defaults by:
#   * Supplying `--build-arg <arg name>=<arg value>` if building the image
#     from the command line, or
#   * Settting the value in ARGS in `devcontainer.json` if building the image
#     from VSCode
#
# It is best practice for containers to run non-root, so here we set the
# username and user/group IDs for the user that will run in containers
# built from this image. It is a convenience that we use USER_UID 1000
# because this typically corresponds with the USER_UID in the container
# host. Thus, when we mount volumes, the container user is able to
# interact with the files from the host without having to change their
# permissions.
ARG USER_NAME="ubuntu"
ARG USER_UID="1000"
ARG USER_GID="1000"

# Update Ubuntu and install common tools/dependencies. Add to the list
# as required and maintain in alphabetical order for maintenance.
#
# We install `sudo` so we can switch to a non-root user later.
#
# No need to run `apt-get clean -y` because this is automatically done by
# Ubuntu. Removing `/var/lib/apt/lists/*` keeps the image layer size down.
RUN set -x \
  && deps=" \
  ca-certificates \
  curl \
  git \
  jq \
  less \
  locales \
  openssh-client \
  sudo \
  unzip \
  vim \
  wget \
  " \
  && apt-get update && apt-get install -y ${deps} --no-install-recommends \
  && locale-gen ${LOCALE}

# Create the non-root user and enable sudo for them.
RUN groupadd --gid ${USER_GID} ${USER_NAME} \
  && useradd --create-home --uid ${USER_UID} --gid ${USER_GID} -s /bin/bash ${USER_NAME} \
  && echo "${USER_NAME} ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers \
  && chmod 0440 /etc/sudoers

# Switch to non-root user.
USER ${USER_NAME}

# Switch working directory to non-root user home.
WORKDIR /home/${USER_NAME}

# Install Node.js.
# RUN curl -sSL https://deb.nodesource.com/setup_lts.x | sudo -E bash - \
#   && deps="nodejs" \
#   && sudo apt-get update && sudo apt-get install -y ${deps} --no-install-recommends

# Install global Node.js dependencies.
# RUN sudo npm install --location=global npm \
#   && sudo npm install --location=global typescript \
#   && sudo npm install --location=global aws-cdk-lib \
#   && sudo npm install --location=global aws-cdk

# Install AWS CLI v2 and enable AWS CLI auto-completion.
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-$(uname -m).zip" -o "awscliv2.zip" \
  && unzip awscliv2.zip \
  && sudo ./aws/install \
  && rm ./awscliv2.zip \
  && rm -rf ./aws \
  && echo "" >> ~/.bashrc \
  && echo "# Enables AWS CLI auto-completion." >> ~/.bashrc \
  && echo "export PATH=/usr/local/bin:$PATH" >> ~/.bashrc \
  && echo "complete -C '/usr/local/bin/aws_completer' aws" >> ~/.bashrc

VOLUME [ "/home/${USER_NAME}/.aws" ]

RUN sudo apt-get install -y ruby-full build-essential zlib1g-dev && \
    echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc && \
    echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc && \
    echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc && \
    export GEM_HOME="$HOME/gems" && \
    gem install jekyll bundler

# You might enable these if using the dev container outside of VS Code. When using VS Code
# dev containers a volume is automatically created that exposes the project directory as
# `/workspaces/<project name>`.
# VOLUME [ "/opt/app" ]
# WORKDIR /opt/app