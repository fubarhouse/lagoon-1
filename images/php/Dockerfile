ARG CLI_IMAGE
ARG LAGOON_IMAGE_VERSION
ARG PHP_IMAGE_VERSION
FROM ${CLI_IMAGE} as cli

FROM amazeeio/php:${PHP_IMAGE_VERSION}-fpm-${LAGOON_IMAGE_VERSION}

RUN apk add --no-cache --update clamav clamav-libunrar --repository http://dl-cdn.alpinelinux.org/alpine/edge/main \
    && freshclam

COPY images/php/config/00-govcms.ini /usr/local/etc/php/conf.d/

COPY --from=cli /app /app

RUN /app/sanitize.sh \
  && rm -rf /app/sanitize.sh
