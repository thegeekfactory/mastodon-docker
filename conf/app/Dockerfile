FROM ruby:2.4-alpine

LABEL maintainer="https://github.com/thegeekfactory/mastodon-docker" \
      description="Docker files for easier mastodon setup"

ENV UID=991 GID=991 \
    RAILS_SERVE_STATIC_FILES=true \
    RAILS_ENV=production NODE_ENV=production

EXPOSE 3000 4000

RUN set -ex \
    && echo "@edge https://nl.alpinelinux.org/alpine/edge/main" >> /etc/apk/repositories \
    && apk add --no-cache \
        ca-certificates \
        ffmpeg \
        file \
        git \
        imagemagick@edge \
        libpq \
        libxml2 \
        libxslt \
        nodejs-npm@edge \
        nodejs@edge \
        protobuf \
        su-exec \
        tini \
    && update-ca-certificates \
    && npm install -g npm@3 && npm install -g yarn

WORKDIR /mastodon
COPY mastodon/Gemfile mastodon/Gemfile.lock mastodon/package.json \
        mastodon/yarn.lock ./

RUN set -ex \
    && apk add --no-cache -t build-dependencies \
        build-base \
        libxml2-dev \
        libxslt-dev \
        postgresql-dev \
        protobuf-dev \
        python \
    && bundle install --clean --no-cache --deployment --without test development \
    && yarn --ignore-optional --pure-lockfile

COPY mastodon/ .

RUN set -ex \
    && mkdir /mastodon/public/assets /mastodon/public/packs \
    && bundle exec rake SECRET_KEY_BASE=f0aee3707669309a42f6cca62064aea9af221400b6efe1f5afbf17d6d4024d07f22cfaa6bfbdb17ff11097db70eab018cefabe40eef995b9eb77b618472935bf assets:precompile \
    && apk del --no-cache build-dependencies \
    && apk del --no-cache \
        nodejs-npm \
        nodejs \
    && yarn cache clean \
    && rm /root/.??* -fr

COPY mastodon/docker_entrypoint.sh /usr/local/bin/run
RUN chmod +x /usr/local/bin/run

VOLUME /mastodon/public/system

ENTRYPOINT ["/usr/local/bin/run"]
