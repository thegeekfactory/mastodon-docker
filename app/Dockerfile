FROM ruby:2.4-alpine

LABEL maintainer="https://github.com/tootsuite/mastodon" \
      description="A GNU Social-compatible microblogging server"

ENV UID=991 GID=991 \
    RAILS_SERVE_STATIC_FILES=true \
    RAILS_ENV=production NODE_ENV=production

EXPOSE 3000 4000

WORKDIR /mastodon

RUN echo "@edge https://nl.alpinelinux.org/alpine/edge/main" >> /etc/apk/repositories \
    && apk -U upgrade \
    && apk add -t build-dependencies \
        build-base \
        libxml2-dev \
        libxslt-dev \
        postgresql-dev \
        protobuf-dev \
        python \
    && apk add \
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
    && npm install -g npm@3 && npm install -g yarn \
    && update-ca-certificates \
    && rm -rf /tmp/* /var/cache/apk/*

COPY mastodon/Gemfile mastodon/Gemfile.lock mastodon/package.json \
        mastodon/yarn.lock ./

RUN bundle install --deployment --without test development \
    && yarn --ignore-optional --pure-lockfile

COPY mastodon/ .

COPY mastodon/docker_entrypoint.sh /usr/local/bin/run

RUN chmod +x /usr/local/bin/run

VOLUME /mastodon/public/system /mastodon/public/assets /mastodon/public/packs

ENTRYPOINT ["/usr/local/bin/run"]
