# GovCMS Lagoon

[![GitHub Actions](https://github.com/govcms/lagoon/workflows/check/badge.svg)](https://github.com/govcms/lagoon/actions)

## Background

This project is to streamline the Lagoon images provided by the Australian Government Department of Finance's GovCMS programme to their clients for running Drupal 7, 8 and 9 projects on the amazeeio Lagoon platform.

## Building locally

This project uses GitHub actions at the moment, and to do this you can use the `act` comand line tool [found on GitHub](https://github.com/nektos/act).

The container which most replicates GitHub's upstream functionality is over 18GB, but you can invoke the pipeline using:
```
$ act -P ubuntu-latest=nektos/act-environments-ubuntu:18.04
```

## Running the GovCMS Lagoon images locally.

You'll need to have the variables set in the shell session or environment, or at the least you will need to copy one of the provided `.env` files to `.env` before running.
```
$ cp .env.govcms9 .env
$ docker-compose up -d
$ docker-compose exec -T cli drush si -y govcms
$ drush uli
``` 

### Notes

The pipeline will give you three sets of dissimilar images, one for GovCMS 7, 8 and 9. These will be reflected as `govcms7lagoon/*`, `govcms8lagoon/*` and `govcms9lagoon/*` `DOCKERHUB_NAMESPACE` variable. These artifacts can be re-tagged or scripted as necessary.

## License

This repository ships with the same license as the GovCMS8 Distribution and Drupal. 