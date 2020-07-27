ARG LAGOON_IMAGE_VERSION
ARG PHP_IMAGE_VERSION

FROM amazeeio/php:${PHP_IMAGE_VERSION}-cli-drupal-${LAGOON_IMAGE_VERSION} as builder

#ARG GOVCMS_PROJECT_VERSION='1.4.0'
ARG DRUPAL_CORE_VERSION='9.0.1'
ARG COMPOSER_AUTH

RUN mkdir -p /app/config/sync

# No distro is available right now, let's mount one:
COPY images/govcms/9.x/custom /app/custom

COPY images/govcms/9.x/composer.* /app/

#RUN sed -i -e "/govcms\/govcms/ s!\^1.0!${GOVCMS_PROJECT_VERSION}!" /app/composer.json \
RUN sed -i -e "/drupal\/core-recommended/ s!\^9.0!${DRUPAL_CORE_VERSION}!" /app/composer.json

COPY images/govcms/9.x/scripts/composer/ScriptHandler.php /app/scripts/composer/ScriptHandler.php

ENV COMPOSER_MEMORY_LIMIT=-1
RUN cat composer.json
RUN composer install -d /app \
# Force drupal-scaffold to run to ensure that the needed files are always created, even if a composer.lock is provided
    && composer drupal:scaffold \
    && composer clearcache

COPY images/govcms/9.x/scripts/bin/sanitize.sh /app/sanitize.sh

COPY images/govcms/9.x/scripts/bin /usr/bin/
RUN chmod 755 /usr/bin/govcms-deploy
COPY images/govcms/9.x/config/govcms.site.yml /app/drush/sites/

# Ensure MySQL client can accept server max_allowed_packet
COPY images/govcms/9.x/config/mariadb-client.cnf /etc/my.cnf.d

RUN mkdir -p /app/web/sites/default/files/private \
    && fix-permissions /home/.drush \
    && fix-permissions /app/drush/sites \
    && fix-permissions /etc/my.cnf.d \
    && chmod +x /app/sanitize.sh \
    && /app/sanitize.sh

#COPY images/govcms/9.x/modules/ /app/web/sites/all/modules/

COPY images/govcms/9.x/settings/ /app/web/sites/default/
RUN chmod 444 /app/web/sites/default/settings.php

# Define where the Drupal Root is located
ENV WEBROOT=web
