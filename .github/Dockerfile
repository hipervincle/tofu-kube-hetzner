FROM alpine:3.22

ARG PRODUCT="packer"
ARG VERSION="1.14.2"
ARG TERRAFORM_DOCS_VERSION="v0.20.0"
ARG GIT_USER_NAME=${GIT_USER_NAME}
ARG GIT_USER_EMAIL=${GIT_USER_EMAIL}

RUN apk update && apk add --no-cache \
    bat \
    coreutils \
    curl \
    eza \
    fd \
    git \
    github-cli \
    gnupg \
    grep \
    hcloud \
    kubectl \
    libffi-dev \
    linux-headers \
    musl-dev \
    nodejs \
    openjdk21-jre \
    openssh-client \
    opentofu \
    pre-commit \
    sed \
    the_silver_searcher \
    tzdata \
    unzip \
    wget \
    which \
    zip \
    zsh 

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
    # Install terraform-docs
    curl -Lo /terraform-docs.tar.gz https://github.com/terraform-docs/terraform-docs/releases/download/${TERRAFORM_DOCS_VERSION}/terraform-docs-${TERRAFORM_DOCS_VERSION}-$(uname)-amd64.tar.gz && \
    tar -xzf /terraform-docs.tar.gz -C /usr/local/bin && \
    chmod +x /usr/local/bin/terraform-docs && \
    rm /terraform-docs.tar.gz

WORKDIR /root

RUN git config --global --add safe.directory '*' && \
    git config --global user.name ${GIT_USER_NAME} && \
    git config --global user.email ${GIT_USER_EMAIL} && \
    git config --global pull.rebase true && \
    git config --global gpg.format "ssh" && \
    git config --global commit.gpgsign true && \
    git config --global user.signingkey ~/.ssh/id_ed25519.pub