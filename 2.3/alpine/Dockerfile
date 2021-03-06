FROM ruby:2.3.3-alpine

ENV LIBV8_BRANCH v3.16.14.19
ENV LIBV8_VERSION 3.16.14.19-x86_64-linux

RUN set -ex \
    \
    && apk add --update --no-cache --virtual .libv8-builddeps \
      make \
      python \
      git \
      bash \
      curl \
      findutils \
      binutils-gold \
      tar \
      linux-headers \
      build-base \
    \
    && git clone -b $LIBV8_BRANCH --recursive git://github.com/cowboyd/libv8.git \
    && cd ./libv8 \
    && sed -i -e 's/Gem::Platform::RUBY/Gem::Platform.local/' libv8.gemspec \
    && gem build --verbose libv8.gemspec \
    && export GYP_DEFINES="$GYP_DEFINES linux_use_bundled_binutils=0 linux_use_bundled_gold=0" \
    && gem install --verbose libv8-$LIBV8_VERSION.gem \
    \
    && apk del --purge .libv8-builddeps \
    \
    && cd ../ \
    && rm -rf ./libv8 \
    && cd /usr/local/bundle/gems/libv8-$LIBV8_VERSION/vendor/ \
    && mkdir -p /tmp/v8 \
    && mv ./v8/out /tmp/v8/. \
    && mv ./v8/include /tmp/v8/. \
    && rm -rf ./v8 ./depot_tools \
    && mv /tmp/v8 . \
    \
    && apk add --update --no-cache libstdc++ \
    && apk add --update --no-cache --virtual=build-dependencies \
      build-base \
    && gem install therubyracer \
    && rm -fr /usr/local/bundle/gems/libv8-$LIBV8_VERSION/vendor \
    && apk del --purge build-dependencies \
    && rm -rf /tmp/* /var/tmp/* /var/cache/apk/*
