ARG CLI_IMAGE
ARG LAGOON_IMAGE_VERSION
FROM ${CLI_IMAGE} as cli

FROM amazeeio/nginx-drupal:${LAGOON_IMAGE_VERSION}

# nginx config.
COPY images/drupal/nginx/scripts/no-robots.sh /lagoon/entrypoints/20-no-robots.sh

COPY images/drupal/nginx/helpers/ /etc/nginx/helpers/
COPY images/drupal/nginx/drupal /etc/nginx/conf.d/drupal

RUN fix-permissions /etc/nginx

COPY --from=cli /app /app
COPY --from=cli /app/sanitize.sh /app/sanitize.sh

RUN /app/sanitize.sh \
  && rm -rf /app/sanitize.sh

# Define where the Drupal Root is located
ENV WEBROOT=web
