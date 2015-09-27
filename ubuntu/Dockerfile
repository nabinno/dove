FROM ubuntu:14.04
ENV HOME /home/action
ENV PATH $HOME/.parts/autoparts/bin:${PATH}
ENV PATH $HOME/.linuxbrew/bin:$PATH
ENV PATH $HOME/.jenv/bin:$PATH
ENV PATH $HOME/.local/bin:$PATH
ENV PATH $HOME/.local/rbenv/bin:$PATH
ENV PATH $HOME/.local/perl-$REQUIRED_PERL_VERSION/bin:$PATH
ENV PATH $HOME/.local/scala-$REQUIRED_SCALA_VERSION/bin:$PATH
ENV PATH $HOME/.cask/bin:$PATH
ENV PATH $PLAY_HOME:$PATH
ENV REQUIRED_JAVA_VERSION 1.7.0
ENV REQUIRED_SCALA_VERSION 2.11.5
ENV REQUIRED_PLAY_VERSION 2.2.3
ENV REQUIRED_PERL_VERSION 5.18.2
ENV REQUIRED_RUBY_VERSION 2.2.0
ENV REQUIRED_EMACS_VERSION 24.5
ENV AUTOPARTS_DEV true
ENV RAILS_ENV development
ENV JAVA_HOME /usr/lib/jvm/java-$REQUIRED_JAVA_VERSION
ENV PLAY_HOME /usr/local/play-$REQUIRED_PLAY_VERSION
ENV MANPATH $HOME/.linuxbrew/share/man:$MANPATH
ENV INFOPATH $HOME/.linuxbrew/share/info:$INFOPATH
ENV LD_LIBRARY_PATH $HOME/.linuxbrew/lib:$LD_LIBRARY_PATH
ENV DEBIAN_FRONTEND noninteractive

# Locale
RUN sed -i "s/^\(PATH=\"\/usr\/local\/sbin:\/usr\/local\/bin:\/usr\/sbin:\/usr\/bin:\/sbin:\/bin:\/usr\/games\"\)$/\1\nLC_ALL=en_US.UTF-8/g" /etc/environment
RUN locale-gen en_US.UTF-8

# Basic installation
RUN echo "deb http://archive.ubuntu.com/ubuntu precise main universe" > /etc/apt/sources.list
RUN echo "deb http://archive.ubuntu.com/ubuntu precise-updates main universe" >> /etc/apt/sources.list
RUN echo "deb http://archive.ubuntu.com/ubuntu precise-security main universe" >> /etc/apt/sources.list
RUN add-apt-repository -y "deb http://archive.ubuntu.com/ubuntu $(lsb_release -sc) universe"
RUN add-apt-repository -y ppa:chris-lea/node.js
RUN apt-get -y update; apt-get install -y -q \
    autoconf \
    automake \
    autotools-dev \
    base-files \
    base-passwd \
    binutils \
    bison \
    build-essential \
    bzip2 \
    cmake \
    cron \
    curl \
    dnsutils \
    gawk \
    gdb \
    git \
    git-core \
    gnupg \
    imagemagick \
    libarchive12 \
    libarchive-dev \
    libbz2-1.0 \
    libbz2-dev \
    libc6 \
    libc6-dev \
    libcurl3 \
    libcurl3-gnutls \
    libcurl4-openssl-dev \
    libdb5.1 \
    libdb5.1-dev \
    libevent-1.4-2 \
    libevent-core-1.4-2 \
    libevent-dev \
    libevent-extra-1.4-2 \
    libffi-dev \
    libgdbm-dev \
    libglib2.0-dev \
    libicu-dev \
    libldap-2.4-2 \
    libldap2-dev \
    libltdl7 \
    libltdl-dev \
    liblzma5 \
    liblzma-dev \
    liblzma-doc \
    libmagickcore-dev \
    libmagickwand-dev \
    libmysqlclient-dev \
    libncap44 \
    libncap-dev \
    libncurses5-dev \
    libncursesw5 \
    libncursesw5-dev \
    libpam0g-dev \
    libpng12-0 \
    libpng12-dev \
    libpq-dev \
    libqt4-dev \
    libreadline6 \
    libreadline6-dev \
    libreadline-dev \
    libsndfile1-dev \
    libsqlite3-dev \
    libssl0.9.8 \
    libssl-dev \
    libtool \
    libxml2 \
    libxml2-dev \
    libxslt1-dev \
    libxslt-dev \
    libxt6 \
    libxt-dev \
    libyaml-dev \
    make \
    mysql-client \
    mysql-server \
    nano \
    ncurses-dev \
    nodejs \
    openssh-server \
    openssl \
    pkg-config \
    postgresql \
    postgresql-contrib \
    postgresql-client \
    psmisc \
    python-software-properties \
    screen \
    sqlite3 \
    subversion \
    sudo \
    telnet \
    tsconf \
    unzip \
    util-linux \
    vim \
    wget \
    whiptail \
    xz-utils \
    zip \
    zlib1g \
    zlib1g-dev \
    zsh

# AddUser
WORKDIR /home/action
RUN adduser --disabled-password action && \
    echo 'root:password' | chpasswd && \
    echo 'action:password' | chpasswd && \
    chsh -s /usr/bin/zsh root && \
    chsh -s /usr/bin/zsh action
RUN chmod 640 /etc/sudoers && \
    sed -i "s/^\(# User privilege specification\)$/\1\naction  ALL=(ALL)       ALL/g" /etc/sudoers && \
    chmod 440 /etc/sudoers
RUN chmod 775 /var/run/screen && \
    chmod 777 -R /var/spool/cron

# Ruby
WORKDIR /home/action
RUN git clone git://github.com/sstephenson/rbenv.git .local/rbenv && \
    mkdir .local/rbenv/shims .local/rbenv/versions .local/rbenv/plugins && \
    git clone git://github.com/sstephenson/ruby-build.git .local/rbenv/plugins/ruby-build && \
    git clone https://github.com/sstephenson/rbenv-gem-rehash.git .local/rbenv/plugins/rbenv-gem-rehash && \
    echo 'eval "$(rbenv init -)' && \
    exec $SHELL -l
RUN rbenv install $REQUIRED_RUBY_VERSION && \
    rbenv rehash && \
    rbenv global $REQUIRED_RUBY_VERSION
RUN gem install --no-rdoc --no-ri \
    bundler \
    compass \
    haml \
    rails \
    rubygems-bundler \
    sidekiq \
    unicorn
RUN gem regenerate_binstubs

# Autopats
WORKDIR /home/action
RUN mkdir -p /home/action/.parts
RUN git clone https://github.com/nitrous-io/autoparts.git /home/action/.parts/autoparts
RUN parts install \
    chruby \
    ctags \
    elixir \
    erlang \
    go \
    heroku_toolbelt \
    maven \
    nodejs \
    phantomjs \
    pip \
    the_silver_searcher \
    tree

# Linuxbrew
WORKDIR /home/action
RUN ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/linuxbrew/go/install)"
RUN brew install \
    jq \
    scalaenv \
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
