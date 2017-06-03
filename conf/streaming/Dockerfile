FROM alpine:3.6

LABEL maintainer="https://github.com/thegeekfactory/mastodon-docker" \
      description="Docker files for easier mastodon setup"

EXPOSE 4000

RUN set -ex \
    && apk add --no-cache \
        nodejs \
        yarn

WORKDIR /app
COPY mastodon/streaming/index.js \
     mastodon/yarn.lock \
     conf/streaming/package.json \
     conf/streaming/.babelrc ./

RUN set -ex \
    && yarn --ignore-optional --pure-lockfile \
    && yarn run build \
    && rm -rf node_modules \
    && apk add --no-cache --virtual build-dependencies \
        build-base \
        python \
    && yarn --ignore-optional --pure-lockfile --production=true \
    && apk del --no-cache build-dependencies \
    && yarn cache clean

RUN set -ex \
    && addgroup mastodon \
    && adduser -h /mastodon -s /bin/sh -D -G mastodon mastodon

USER mastodon

CMD yarn start
