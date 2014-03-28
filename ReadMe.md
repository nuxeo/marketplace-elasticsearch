marketplace-nuxeo-elasticsearch
===============================

## About

This is project allows to build the marketplace package for `nuxeo-elastic-search` addon.

The goal of this package is to provide an easy way to test `elasticsearch` with Nuxeo :

 - simple installation
 - default configuration for embedded ElasticSeach
 - default configuration to replace default PageProviders by `elasticsearch` based ones


## Building

To build and run the tests, simply start the maven build :

    mvn clean install


## Installing

To install the package :

 - take a fresh nuxeo CAP 5.9.3
 - start the configuration wizard and switch to PGSQL (or an other external database)
      - for now the package will remove the old lucene libs that are required for embedded H2 to run
 - then install the package
      - from the AdminCenter (Upload + install)
      - from the command line using `nuxeoctl mp-install package.zip --nodeps`

## Upon first startup

Initially the `elasticsearch` index will be empty.

To populate it you have 2 options :

 - if you have a small repository : 
     - Go in Admin Center / elasticsearch / config 
     - use teh Reindex button ()and wait a few seconds)
 - if you have a big repository
     - check https://github.com/nuxeo/nuxeo-elasticsearch/tree/master/scripts


 
