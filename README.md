# RDF SKOS

## Development setup

### Requirements

* [Virtuoso 7 (Triplestore database)](https://github.com/openlink/virtuoso-opensource)

### Initial setup

You can build the test site by running the following steps.

* Install Virtuoso. The easiest way to do this is by using the OpenEuropa [Triple store](https://github.com/openeuropa/triple-store-dev) development Docker container which also pre-imports the main Europa vocabularies.

* Install all the composer dependencies:

```
$ composer install
```

* Customize build settings by copying `runner.yml.dist` to `runner.yml` and
changing relevant values, like your database credentials.

* Setup test site by running:

```
$ ./vendor/bin/run drupal:site-setup
```

This will symlink the theme in the proper directory within the test site and
perform token substitution in test configuration files such as `behat.yml.dist`.

* Install test site by running:

```
$ ./vendor/bin/run drupal:site-install
```

Your test site will be available at `./build`.

### Using Docker Compose

Alternatively you can build a test site using Docker and Docker-compose with the provided configuration.

Requirements:

- [Docker](https://www.docker.com/get-docker)
- [Docker-compose](https://docs.docker.com/compose/)

You can make any alterations you need for your local Docker setup. However, the defaults should be enough to set the project up.

Run:

```
$ docker-compose up -d
```

Then:

```
$ docker-compose exec web composer install
$ docker-compose exec web ./vendor/bin/run drupal:site-install
```

Your test site will be available at [http://localhost:8080/build](http://localhost:8080/build).

To run the grumphp test:

```
$ docker-compose exec web ./vendor/bin/grumphp run
```

To run the phpunit test:

```
$ docker-compose exec web ./vendor/bin/phpunit
```

To run the behat test:

```
$ docker-compose exec web ./vendor/bin/behat
```

### Working with content

The project ships with the following Task Runner commands to work with content in the RDF store, they require Docker Compose
services to be up and running.

Purge all data:

```
$ docker-compose exec sparql ./vendor/bin/robo purge
```

Or, if you can run commands on your host machine:

```
$ ./vendor/bin/run sparql:purge
```

Import default data:

```
$ docker-compose exec sparql ./vendor/bin/robo import
```

Or, if you can run commands on your host machine:

```
$ ./vendor/bin/run sparql:import
```

Reset all data, i.e. run purge and import:

```
$ docker-compose exec sparql ./vendor/bin/robo purge
$ docker-compose exec sparql ./vendor/bin/robo import
```

Or, if you can run commands on your host machine:

```
$ ./vendor/bin/run sparql:reset
```

Updating from 0.10.0 to 0.11.0

On 0.11.0, the rdf_entity module dependency has been removed and instead the sparql_entity_storage module has been
introduced (see the [rdf_entity module's Readme](https://github.com/ec-europa/rdf_entity#updating-from-10-alpha16-to-alpha17) for more information).
As suggested by the rdf_entity module itself, the following steps can be taking in order to update rdf_skos in production:

The update process is split in three deployments.

* First deployment
1. Install an empty version of the `drupal/sparql_entity_storage` module:
   ```
   $ composer require drupal/sparql_entity_storage:dev-empty-module
   ```
1. Deploy to production.
1. Enable the module.

* Second deployment
1. Remove the empty `drupal/sparql_entity_storage` module requirement
1. Require `drupal/rdf_entity` with the new `1.0-alpha21` version and `drupal/rdf_skos` with the new `0.11.0` version.
1. Deploy to production.
1. Uninstall the `drupal/rdf_entity` module.

* Third deployment
1. Remove the `drupal/rdf_entity` dependency.
1. Deploy to production.

After this steps your site should have the latest version `drupal/rdf_skos` module using `drupal/sparql_entity_storage`
and the `drupal/rdf_entity` module should no longer be in your codebase.
