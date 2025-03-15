ARG UBUNTU_VERSION="22.04"

# Specify base image.
FROM ubuntu:${UBUNTU_VERSION}

ARG LOCALE="en_US.UTF-8"
ARG USER_NAME="ubuntu"
ARG USER_UID="1000"
ARG USER_GID="1000"

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

RUN SNIPPET="export PROMPT_COMMAND='history -a' && export HISTFILE=/commandhistory/.bash_history" \
    && sudo mkdir /commandhistory \
    && sudo touch /commandhistory/.bash_history \
    && sudo chown -R ${USER_NAME} /commandhistory \
    && echo "$SNIPPET" >> "/home/${USER_NAME}/.bashrc"

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

# VOLUME [ "/home/${USER_NAME}/.aws" ]

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