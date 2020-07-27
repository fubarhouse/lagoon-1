ARG LAGOON_IMAGE_VERSION
ARG PHP_IMAGE_VERSION
FROM amazeeio/php:${PHP_IMAGE_VERSION}-cli-drupal-${LAGOON_IMAGE_VERSION} as builder

ARG GOVCMS_PROJECT_VERSION='7.x-3.x'

RUN rm -rf /app \
    && mkdir /src \
    && cd /src \
    && echo "memory_limit=-1" >> /usr/local/etc/php/conf.d/memory.ini \
    && echo "https://raw.githubusercontent.com/govCMS/GovCMS7/${GOVCMS_PROJECT_VERSION}/drupal-org-core.make" \
    && wget https://raw.githubusercontent.com/govCMS/GovCMS7/${GOVCMS_PROJECT_VERSION}/drupal-org-core.make -O /src/drupal-org-core.make \
    && { \
        echo "core = 7.x"; \
        echo "api = 2"; \
        echo "includes[] = drupal-org-core.make"; \
        echo "projects[govcms][type] = profile"; \
        echo "projects[govcms][download][type] = git"; \
        echo "projects[govcms][download][url] = https://github.com/govCMS/GovCMS7"; \
        echo "projects[govcms][download][branch] = ${GOVCMS_PROJECT_VERSION}"; \
    } | tee -a "/src/build-govcms.make" \
    && drush make /src/build-govcms.make /app --contrib-destination \
    && echo "version = \"${GOVCMS_PROJECT_VERSION}\"" >> /app/profiles/govcms/govcms.info

FROM amazeeio/php:${PHP_IMAGE_VERSION}-cli-drupal-${LAGOON_IMAGE_VERSION}

COPY --from=builder /app /app
COPY images/govcms/7.x/scripts/sanitize.sh /app/sanitize.sh

COPY images/govcms/7.x/scripts /usr/bin/
COPY images/govcms/7.x/config/aliases.drushrc.php /home/.drush/site-aliases/
COPY images/govcms/7.x/config/mariadb-client.cnf /etc/my.cnf.d

# findutils can be removed after amazeeio/lagoon PR1077
RUN apk add --no-cache python findutils \
    && drush @none dl registry_rebuild-7.x \
    && drush dl --destination=sites/all/modules/contrib -y \
    fast_404-7.x-1.5 \
    lagoon_logs-7.x-1.0 \
    redis-7.x-3.17 \
    stage_file_proxy-7.x-1.9 \
    && mkdir -p /app/sites/default/files/private \
    && fix-permissions /home/.drush \
    && fix-permissions /etc/my.cnf.d \
    && chmod +x /app/sanitize.sh \
    && /app/sanitize.sh

# Ensure tmp folder always exists.
RUN mkdir -p /app/sites/default/files/private/tmp/

#COPY modules/ /app/sites/all/modules/

COPY images/govcms/7.x/settings/ /app/sites/default/
RUN chmod 444 /app/sites/default/settings.php
