FROM debian:trixie

ENV DEBIAN_FRONTEND=noninteractive

ARG PRODUCT="packer"
ARG VERSION="1.14.2"
ARG TERRAFORM_DOCS_VERSION="v0.20.0"
ARG GIT_USER_NAME=${GIT_USER_NAME}
ARG GIT_USER_EMAIL=${GIT_USER_EMAIL}

RUN \
    # Install basic tools
    apt-get update && apt-get install --no-install-recommends -y \
    ca-certificates \
    curl \
    gnupg \
    wget && \
    # Add keyrings and repositories
    mkdir -p -m 755 /etc/apt/keyrings && \
    out=$(mktemp) && \
    wget -nv -O "$out" https://cli.github.com/packages/githubcli-archive-keyring.gpg && \
    cat "$out" | tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null && \
    chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg && \
    mkdir -p -m 755 /etc/apt/sources.list.d && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | \
    tee /etc/apt/sources.list.d/github-cli.list > /dev/null && \
    curl -fsSL https://get.opentofu.org/opentofu.gpg | tee /etc/apt/keyrings/opentofu.gpg >/dev/null && \
    curl -fsSL https://packages.opentofu.org/opentofu/tofu/gpgkey | gpg --no-tty --batch --dearmor -o /etc/apt/keyrings/opentofu-repo.gpg >/dev/null && \
    chmod a+r /etc/apt/keyrings/opentofu.gpg /etc/apt/keyrings/opentofu-repo.gpg && \
    echo "deb [signed-by=/etc/apt/keyrings/opentofu.gpg,/etc/apt/keyrings/opentofu-repo.gpg] https://packages.opentofu.org/opentofu/tofu/any/ any main \
    deb-src [signed-by=/etc/apt/keyrings/opentofu.gpg,/etc/apt/keyrings/opentofu-repo.gpg] https://packages.opentofu.org/opentofu/tofu/any/ any main" | \
    tee /etc/apt/sources.list.d/opentofu.list > /dev/null && \
    chmod a+r /etc/apt/sources.list.d/opentofu.list && \
    # Install packages
    apt-get update && \
    apt-get install --no-install-recommends -y \
    bat \
    eza \
    fd-find \
    fzf \
    gh \
    git \
    hcloud-cli \
    kubectl \
    nodejs \
    openjdk-25-jre \
    openssh-client \
    pre-commit \
    silversearcher-ag \
    tofu \
    unzip \
    vim \
    zsh && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /tmp

# Install and verify packer
RUN wget https://releases.hashicorp.com/${PRODUCT}/${VERSION}/${PRODUCT}_${VERSION}_linux_amd64.zip && \
    wget https://releases.hashicorp.com/${PRODUCT}/${VERSION}/${PRODUCT}_${VERSION}_SHA256SUMS && \
    wget https://releases.hashicorp.com/${PRODUCT}/${VERSION}/${PRODUCT}_${VERSION}_SHA256SUMS.sig && \
    wget -qO- https://www.hashicorp.com/.well-known/pgp-key.txt | gpg --import && \
    gpg --verify ${PRODUCT}_${VERSION}_SHA256SUMS.sig ${PRODUCT}_${VERSION}_SHA256SUMS && \
    grep ${PRODUCT}_${VERSION}_linux_amd64.zip ${PRODUCT}_${VERSION}_SHA256SUMS | sha256sum -c && \
    unzip ${PRODUCT}_${VERSION}_linux_amd64.zip -d /usr/local/bin && \
    chmod +x /usr/local/bin/${PRODUCT} && \
    rm -rf /tmp/packer_* && \
    # Install terraform-docs
    curl -Lo /terraform-docs.tar.gz https://github.com/terraform-docs/terraform-docs/releases/download/${TERRAFORM_DOCS_VERSION}/terraform-docs-${TERRAFORM_DOCS_VERSION}-$(uname)-amd64.tar.gz && \
    tar -xzf /terraform-docs.tar.gz -C /usr/local/bin && \
    chmod +x /usr/local/bin/terraform-docs && \
    rm /terraform-docs.tar.gz

WORKDIR /root

RUN git config --global --add safe.directory '*' && \
    git config --global user.name "${GIT_USER_NAME}" && \
    git config --global user.email "${GIT_USER_EMAIL}" && \
    git config --global pull.rebase true && \
    git config --global gpg.format "ssh" && \
    git config --global commit.gpgsign true && \
    git config --global user.signingkey ~/.ssh/id_ed25519.pub
