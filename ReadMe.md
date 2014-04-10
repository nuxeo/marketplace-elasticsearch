marketplace-nuxeo-elasticsearch
===============================

## About

This is project allows to build the marketplace package for
`nuxeo-elasticsearch` addon.

The goal of this package is to provide an easy way to test
`elasticsearch` with Nuxeo : elasticsearch.indexNumberOfShards
 - simple installation
 - default configuration for embedded Elasticsearch
 - default configuration to replace default PageProviders by
   `elasticsearch` based ones


## Building

To build and run the tests, simply start the maven build :

    mvn clean install


## Installing

To install the package :

 - take a fresh nuxeo CAP 5.9.3

 - start the configuration wizard and switch to PGSQL (or an other
   external database)
      - for now the package will remove the old lucene libs that are
        required for embedded H2 to run

 - then install the package
      - from the AdminCenter (Upload + install)
      - from the command line using `nuxeoctl mp-install package.zip --nodeps`

## Configuring

The default `elasticsearch` template will use an embedded
elasticsearch instance, this is only for testing purpose.

The elasticsearch data

You can easily install an `elasticsearch` cluster and setup the
`nuxeo.conf`:

    elasticsearch.addressList=localhost:9300,anothernode:9300
    elasticsearch.clusterName=elasticsearch

Where:
- the addressList point ot one or many known elasticsearch nodes.
- the clusterName is set to the current cluster name

Look at the `templates/elasticsearch/nuxeo.defaults` for configuration
options.

If you want to change the default anylisers for fulltext fields or
change the elastic index settings you need to add a contribution to
override

## Upon first startup

Initially the `elasticsearch` index will be empty.

To populate it you have 2 options :

 - if you have a small repository : 
     - Go in Admin Center / elasticsearch / config 
     - use the Reindex button (and wait a few seconds)

- if you have a big repository
     - check https://github.com/nuxeo/nuxeo-elasticsearch/tree/master/scripts
	   you will find a script to dump and import Nuxeo documents.


 
