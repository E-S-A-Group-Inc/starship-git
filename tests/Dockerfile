FROM rust

# Create /src as root
RUN mkdir /src && chmod -R a+w /src

# Create non-root user
RUN useradd -ms /bin/bash nonroot
USER nonroot

# Install Node.js
ENV NODE_VERSION 12.0.0
ENV NVM_DIR /home/nonroot/.nvm
ENV PATH $NVM_DIR/versions/node/v$NODE_VERSION/bin:$PATH
RUN curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash
# Check that Node.js was correctly installed
RUN node --version

# Install Go
ENV GO_VERSION 1.12.1
ENV GOENV_ROOT /home/nonroot/.goenv
ENV PATH $GOENV_ROOT/bin:$GOENV_ROOT/shims:$PATH
RUN git clone https://github.com/syndbg/goenv.git $GOENV_ROOT \
  && eval "$(goenv init -)" \
  && goenv install $GO_VERSION \
  && goenv global $GO_VERSION \
  && chmod -R a+x $GOENV_ROOT
# Check that Go was correctly installed
RUN go version

# Install Ruby
ENV RUBY_VERSION 2.6.3
ENV RBENV_ROOT /home/nonroot/.rbenv
ENV PATH $RBENV_ROOT/bin:$RBENV_ROOT/shims:$PATH
RUN curl -fsSL https://github.com/rbenv/rbenv-installer/raw/master/bin/rbenv-installer | bash \
  && rbenv install $RUBY_VERSION \
  && rbenv global $RUBY_VERSION
# Check that Ruby was correctly installed
RUN ruby --version

# Install Python
ENV PYTHON_VERSION 3.7.5
ENV PYENV_ROOT /home/nonroot/.pyenv
ENV PATH $PYENV_ROOT/bin:$PYENV_ROOT/shims:$PATH
RUN curl https://pyenv.run | bash \
  && pyenv install $PYTHON_VERSION \
  && pyenv global $PYTHON_VERSION \
  && chmod -R a+x $PYENV_ROOT
# Check that Python was correctly installed
RUN python --version

# Install Dotnet
ENV DOTNET_HOME /home/nonroot/dotnet
ENV DOTNET_SDK_VERSION 2.2.402

RUN mkdir -p "$DOTNET_HOME" \
    && dotnet_download="$DOTNET_HOME/../dotnet.tar.gz" \
    && curl -SL --output "$dotnet_download" https://dotnetcli.blob.core.windows.net/dotnet/Sdk/$DOTNET_SDK_VERSION/dotnet-sdk-$DOTNET_SDK_VERSION-linux-x64.tar.gz \
    && tar -zxf "$dotnet_download" -C "$DOTNET_HOME" \
    && rm "$dotnet_download"

ENV PATH $DOTNET_HOME:$PATH
RUN dotnet help

# Install PHP
ENV PHP_VERSION 7.3.8
ENV PHPENV_ROOT /home/nonroot/.phpenv
ENV PATH $PHPENV_ROOT/bin:$PHPENV_ROOT/shims:$PATH
ENV CONFIGURE_OPTS "--without-tidy --disable-zip"
RUN curl -L https://raw.githubusercontent.com/phpenv/phpenv-installer/master/bin/phpenv-installer | bash \
  && phpenv install $PHP_VERSION \
  && phpenv global $PHP_VERSION \
  && chmod -R a+x $PHPENV_ROOT
# Check that PHP was correctly installed
RUN php --version

# Install Mercurial
RUN HGPYTHON3=1 pip install mercurial
# Check that Mercurial was correctly installed
RUN hg --version
# Install Terraform
ENV TERRAFORM_HOME /home/nonroot/terraform
ENV TERRAFORM_VERSION 0.12.14
ENV PATH ${TERRAFORM_HOME}:${PATH}

RUN mkdir -p ${TERRAFORM_HOME} \
    && terraform_download="${TERRAFORM_HOME}/terraform.zip" \
    && curl -SL --output "${terraform_download}" "https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_amd64.zip" \
    && unzip "${terraform_download}" -d "${TERRAFORM_HOME}" \
    && rm "${terraform_download}"
# Check that terraform was correctly installed
RUN terraform version

# Create blank project
RUN USER=nonroot cargo new --bin /src/starship
WORKDIR /src/starship

# Copy the whole project
COPY . .

# "-Z unstable-options" is required for "--include-ignored"
CMD ["cargo", "test", "--", "-Z", "unstable-options", "--include-ignored"]
