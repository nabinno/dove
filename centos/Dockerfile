FROM centos:centos7
ENV PATH /home/action/.linuxbrew/bin:$PATH
ENV PATH /home/action/.jenv/bin:$PATH
ENV PATH /home/action/.local/bin:$PATH
ENV PATH /home/action/.local/rbenv/bin:$PATH
ENV PATH /home/action/.local/perl-$REQUIRED_PERL_VERSION/bin:$PATH
ENV PATH /home/action/.local/scala-$REQUIRED_SCALA_VERSION/bin:$PATH
ENV PATH /home/action/.cask/bin:$PATH
ENV PATH $PLAY_HOME:$PATH
ENV REQUIRED_JAVA_VERSION 1.7.0
ENV REQUIRED_SCALA_VERSION 2.11.5
ENV REQUIRED_PLAY_VERSION 2.2.3
ENV REQUIRED_PERL_VERSION 5.18.2
ENV REQUIRED_RUBY_VERSION 2.2.0
ENV REQUIRED_EMACS_VERSION 24.5
ENV RAILS_ENV development
ENV JAVA_HOME /usr/lib/jvm/java-$REQUIRED_JAVA_VERSION
ENV PLAY_HOME /usr/local/play-$REQUIRED_PLAY_VERSION
ENV MANPATH /home/action/.linuxbrew/share/man:$MANPATH
ENV INFOPATH /home/action/.linuxbrew/share/info:$INFOPATH
ENV LD_LIBRARY_PATH /home/action/.linuxbrew/lib:$LD_LIBRARY_PATH

# Timezone
RUN rm -f /etc/localtime
RUN cp -p /usr/share/zoneinfo/Japan /etc/localtime

# Basic installation: MySQL, NodeJS, Java, Supervisor, OpenSSH, and OpenSSL
RUN rpm -Uvh http://ftp.riken.jp/Linux/fedora/epel/6/x86_64/epel-release-6-8.noarch.rpm
RUN rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
RUN yum -y update; yum clean all
RUN yum install -y --enablerepo=remi \
    mysql \
    mysql-devel \
    mysql-server \
    mysqlclient \
    redis \
    memcached \
    ; yum clean all
RUN yum -y install epel-release; yum clean all
RUN yum install -y \
    bzip2-devel \
    cmake \
    cron \
    curl \
    curl-devel \
    eadline-devel \
    expat-devel \
    gcc \
    git \
    gnupg \
    graphviz \
    httpd-tools \
    java-$REQUIRED_JAVA_VERSION-openjdk \
    java-$REQUIRED_JAVA_VERSION-openjdk-devel \
    lib-devel \
    m4 \
    make \
    mongodb-server \
    ncurses-devel \
    nodejs \
    npm \
    openssh \
    openssh-clients \
    openssh-server \
    openssl \
    openssl-devel \
    passwd \
    postgresql \
    postgresql-contrib \
    postgresql-server \
    python-pip \
    readline \
    screen \
    sudo \
    supervisor \
    tar \
    texinfo \
    tree \
    unzip \
    useradd \
    vim \
    wget \
    yes \
    zip \
    zlib \
    zlib-devel \
    zsh \
    ; yum clean all

# UserAdd
WORKDIR /home/action
RUN useradd action && \
    USERPASS=password && \
    echo -e "$USERPASS\n$USERPASS" | (passwd --stdin action) && \
    chsh -s /usr/bin/zsh root && \
    chsh -s /usr/bin/zsh action
RUN chmod 640 /etc/sudoers && \
    sed -i "s/^\(# User privilege specification\)$/\1\naction  ALL=(ALL)       ALL/g" /etc/sudoers && \
    chmod 440 /etc/sudoers

# Ruby
WORKDIR /home/action
RUN git clone git://github.com/sstephenson/rbenv.git .local/rbenv && \
    mkdir .local/rbenv/shims .local/rbenv/versions .local/rbenv/plugins && \
    git clone git://github.com/sstephenson/ruby-build.git .local/rbenv/plugins/ruby-build && \
    git clone https://github.com/sstephenson/rbenv-gem-rehash.git .local/rbenv/plugins/rbenv-gem-rehash && \
    echo 'eval "$(rbenv init -)' && \
    exec $SHELL -l
RUN .local/bin/rbenv install $REQUIRED_RUBY_VERSION && \
    .local/bin/rbenv rehash && \
    .local/bin/rbenv global $REQUIRED_RUBY_VERSION
RUN gem install --no-rdoc --no-ri \
    bundler \
    compass \
    haml \
    rails \
    rubygems-bundler \
    sidekiq \
    unicorn
RUN gem regenerate_binstubs

# Linuxbrew
RUN ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/linuxbrew/go/install)"
RUN brew install \
    chruby \
    ctags \
    elixir \
    elixir-build \
    erlang \
    go \
    heroku_toolbelt \
    jq \
    lua \
    maven \
    mruby \
    rust \
    scalaenv \
    the_silver_searcher \
    tmux \
    tree \
    vert.x

# Java
WORKDIR /home/action
RUN git clone https://github.com/gcuisinier/jenv.git ~/.jenv && \
    eval "$(jenv init -)"
RUN wget http://www.scala-lang.org/files/archive/scala-$REQUIRED_SCALA_VERSION.tgz && \
    tar xvf scala-$REQUIRED_SCALA_VERSION.tgz && \
    mv scala-$REQUIRED_SCALA_VERSION ~/.local/
RUN wget http://downloads.typesafe.com/play/$REQUIRED_PLAY_VERSION/play-$REQUIRED_PLAY_VERSION.zip && \
    unzip play-$REQUIRED_PLAY_VERSION.zip && \
    mv play-$REQUIRED_PLAY_VERSION ~/.local/ && \
    rm -fr play-$REQUIRED_PLAY_VERSION.zip
RUN curl https://bintray.com/sbt/rpm/rpm | tee /etc/yum.repos.d/bintray-sbt-rpm.repo && \
    yum install -y sbt

# Perl
WORKDIR /home/action
RUN git clone https://github.com/tagomoris/xbuild.git ~/.local/xbuild && \
    ~/.local/xbuild/perl-install $REQUIRED_PERL_VERSION ~/.local/perl-$REQUIRED_PERL_VERSION && \

# NodeJS
WORKDIR /home/action
RUN npm install -g \
    bower \
    coffee-script \
    grunt-cli \
    gulp \
    http-server \
    less \
    node-plantuml \
    phantomjs2 \
    requirejs

# Python
WORKDIR /home/action
RUN pip install -U \
    awscli

# Emacs
WORKDIR /home/action
RUN current_pwd=`pwd` && \
    wget http://core.ring.gr.jp/pub/GNU/emacs/emacs-$REQUIRED_EMACS_VERSION.tar.gz;  wait && \
    tar zxf emacs-$REQUIRED_EMACS_VERSION.tar.gz;  wait && \
    cd emacs-$REQUIRED_EMACS_VERSION && \
    ./configure --with-xpm=no --with-gif=no --with-x-toolkit=no --with-tiff=no && \
    make && \
    yes | sudo make install;  wait && \
    cd $current_pwd; rm -fr emacs-$REQUIRED_EMACS_VERSION*

RUN chown -R action:action /home/action
EXPOSE 22 3000 3001 4000 4001 8080 9000
CMD /usr/sbin/sshd -D
