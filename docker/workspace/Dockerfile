FROM debian:bookworm

SHELL ["/bin/bash", "-c"]

ARG NODE_VERSION=24.13.1

RUN apt-get update -y && apt-get install -y \
  curl gcc make git zip unzip jq openssl ca-certificates gnupg lsb-release groff default-mysql-client pass gnupg2 iproute2 iputils-ping

RUN curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg \
  && echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null \
  && apt-get update -y && apt-get -y install docker-ce docker-ce-cli containerd.io docker-compose-plugin

RUN curl https://mise.run | sh \
  && echo 'eval "$(/root/.local/bin/mise activate bash)"' >> ~/.bashrc \
  && echo 'eval "$(/root/.local/bin/mise activate bash)"' >> ~/.bash_profile \
  && /root/.local/bin/mise use -g direnv \
  && /root/.local/bin/mise use -g bun \
  && /root/.local/bin/mise use -g node@${NODE_VERSION} \
  && /root/.local/bin/mise use -g awscli \
  && /root/.local/bin/mise use -g aws-vault \
  && echo 'export GPG_TTY="$(tty)"' >> ~/.bashrc \
  && eval "$(/root/.local/bin/mise activate bash)" \
  && npm install -g pnpm \
  && npm install -g @anthropic-ai/claude-code \
  && echo 'eval "$(direnv hook bash)"' >> ~/.bashrc \
  && aws configure set credential_process "aws-vault exec dev --json --prompt=terminal --duration 1h" --profile dev \
  && aws configure set credential_process "aws-vault exec stage --json --prompt=terminal --duration 1h" --profile stage \
  && aws configure set credential_process "aws-vault exec prod --json --prompt=terminal --duration 1h" --profile prod

WORKDIR /work/bun-sample
