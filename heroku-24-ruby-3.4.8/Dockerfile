FROM cimg/base:current

MAINTAINER Blake Israel

# This is Heroku 24 basically (Ubuntu 24.04 "noble").
#
# Unlike the heroku-22 images, there is no noble-based cimg/ruby image to build
# on top of, so we compile Ruby ourselves into /usr/local (the same place cimg
# puts it) on top of cimg/base:current (which is noble and already provides the
# circleci user + passwordless sudo the rest of this image family relies on).
#
# The whole reason this series exists: noble ships libvips 8.15, which satisfies
# Rails/Active Storage's requirement of libvips >= 8.13. Heroku-22 (jammy) only
# had libvips 8.12 and cannot be upgraded in place.

ENV RUBY_MAJOR=3.4
ENV RUBY_VERSION=3.4.8
ENV GEM_HOME=/home/circleci/.rubygems
ENV PATH=/home/circleci/.rubygems/bin:/home/circleci/bin:/home/circleci/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

USER root

RUN apt-get update

RUN apt-get upgrade -y

# Node (matches the heroku-22 "-node" images, which shipped Node 24)
RUN curl -fsSL https://deb.nodesource.com/setup_24.x | bash - \
 && apt-get install -y --no-install-recommends nodejs \
 && corepack enable

# Build dependencies needed to compile Ruby from source
RUN apt-get install -y --no-install-recommends \
    autoconf \
    bison \
    build-essential \
    libffi-dev \
    libgdbm-dev \
    libgmp-dev \
    libncurses-dev \
    libreadline-dev \
    libssl-dev \
    libyaml-dev \
    zlib1g-dev

# Compile Ruby ${RUBY_VERSION} into /usr/local so `ruby` is directly on PATH,
# then mirror cimg/ruby's gem layout (user-writable GEM_HOME, circleci-owned
# /usr/local/bin) so `gem install` / `bundle install` work without sudo.
RUN git clone --depth 1 https://github.com/rbenv/ruby-build.git /tmp/ruby-build \
 && PREFIX=/usr/local /tmp/ruby-build/install.sh \
 && ruby-build "${RUBY_VERSION}" /usr/local \
 && rm -rf /tmp/ruby-build \
 && mkdir -p "${GEM_HOME}" \
 && chown -R circleci:circleci "${GEM_HOME}" \
 && chown circleci:circleci /usr/local/bin

# The Heroku-22 runtime library set, ported to noble (24.04) package names.
# Renames vs jammy: libdav1d5->libdav1d7, libgs9->libgs10,
# libsvtav1enc0->libsvtav1enc1d1, libunistring2->libunistring5,
# libvpx7->libvpx9, libx264-163->libx264-164, libvips42->libvips42t64
# (+libvips-tools); libgnutlsxx28 dropped (no noble equivalent).
RUN apt-get install -y --no-install-recommends \
    apt-transport-https \
    apt-utils \
    bind9-host \
    bzip2 \
    coreutils \
    curl \
    dnsutils \
    ed \
    file \
    fontconfig \
    gcc \
    geoip-database \
    ghostscript \
    gir1.2-harfbuzz-0.0 \
    git \
    gsfonts \
    imagemagick \
    iproute2 \
    iputils-tracepath \
    language-pack-en \
    less \
    libaom3 \
    libargon2-1 \
    libass9 \
    libc-client2007e \
    libc6-dev \
    libcairo2 \
    libcurl4 \
    libdatrie1 \
    libdav1d7 \
    libev4 \
    libevent-2.1-7 \
    libevent-core-2.1-7 \
    libevent-extra-2.1-7 \
    libevent-openssl-2.1-7 \
    libevent-pthreads-2.1-7 \
    libexif12 \
    libfreetype6 \
    libfribidi0 \
    libgd3 \
    libgdk-pixbuf2.0-0 \
    libgdk-pixbuf2.0-common \
    libgnutls-openssl27 \
    libgnutls30 \
    libgraphite2-3 \
    libgs10 \
    libharfbuzz-gobject0 \
    libharfbuzz-icu0 \
    libharfbuzz0b \
    libheif1 \
    liblzf1 \
    libmagickcore-6.q16-3-extra \
    libmcrypt4 \
    libmemcached11 \
    libmp3lame0 \
    libmysqlclient21 \
    libnuma1 \
    libogg0 \
    libonig5 \
    libopencore-amrnb0 \
    libopencore-amrwb0 \
    libopus0 \
    libpango-1.0-0 \
    libpangocairo-1.0-0 \
    libpangoft2-1.0-0 \
    libpixman-1-0 \
    librabbitmq4 \
    librsvg2-2 \
    librsvg2-common \
    libsasl2-modules \
    libseccomp2 \
    libsodium23 \
    libspeex1 \
    libsvtav1enc1d1 \
    libthai-data \
    libthai0 \
    libtheora0 \
    libunistring5 \
    libuv1 \
    libvips-tools \
    libvips42t64 \
    libvorbis0a \
    libvorbisenc2 \
    libvorbisfile3 \
    libvpx9 \
    libwebp7 \
    libwebpdemux2 \
    libwebpmux3 \
    libx264-164 \
    libx265-199 \
    libxcb-render0 \
    libxcb-shm0 \
    libxrender1 \
    libxslt1.1 \
    libyaml-0-2 \
    libzip4 \
    libzstd1 \
    locales \
    lsb-release \
    make \
    netcat-openbsd \
    openssh-client \
    openssh-server \
    patch \
    poppler-utils \
    python-is-python3 \
    python3 \
    rename \
    rsync \
    shared-mime-info \
    socat \
    stunnel \
    tar \
    telnet \
    tzdata \
    unzip \
    wget \
    xz-utils \
    zip \
    zlib1g \
    zstd

RUN apt-get clean && rm -rf /var/lib/apt/lists/*

USER circleci
WORKDIR /home/circleci
