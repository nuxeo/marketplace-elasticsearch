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

 1. Take a fresh Nuxeo CAP 5.9.3

 2. Start the configuration wizard and switch to PostgreSQL (or an
    other external database). For now we don't support the default H2
    database because of incompatible lucene libs dependencies.

 3. Install the nuxeo-elasticsearch package
      - from the AdminCenter (Upload + install)
      - from the command line using `nuxeoctl mp-install package.zip --nodeps`

## Configuring

The default configuration uses an embedded elasticsearch instance
which is running in the same JVM as Nuxeo. By default the
elasticsearch indexes will be located in
`nxsverver/data/elasticsearch`.

This embedded mode is only for testing purpose and should not be used
in production.

To connect to an existing `elasticsearch 1.1.0` cluster you need to edit the
`nuxeo.conf` and add the following lines:


    elasticsearch.addressList=localhost:9300,anothernode:9300
    elasticsearch.clusterName=elasticsearch


Where:
- `addressList` point to one or many elasticsearch nodes.
- `clusterName` is the cluster name to join.

Look at the
[`nuxeo.defaults`](https://github.com/nuxeo/marketplace-elasticsearch/blob/master/package/src/main/resources/install/templates/elasticsearch/nuxeo.defaults)
in the elasticsearch template for more configuration options.

If you want to fine tune the indexing, for instance if you want to
customize the anlyzer or changing the mapping, you need to add a
contribution to override the default one.

Just create a new config file `myelasticsearch-config.xml` with
something like this:


    <?xml version="1.0"?>
    <component name="myorg.elasticsearch.contrib">
      <require>org.nuxeo.elasticsearch.index.defaultMarketPlaceContrib</require>
      <extension target="org.nuxeo.elasticsearch.ElasticSearchComponent"
        point="elasticSearchIndex">
        <elasticSearchIndex name="nuxeo" type="doc">
    	  <settings />
    	  <mapping />
    	  <fulltext />
    	</elasticSearchInde>
      </extension>
    </component>

You can see the
[default configuration here](https://github.com/nuxeo/nuxeo-elasticsearch/blob/master/nuxeo-elasticsearch-core/src/main/resources/OSGI-INF/elasticsearch-default-index-contrib.xml),
and mapping
[extension point documentation here](https://github.com/nuxeo/nuxeo-elasticsearch/blob/master/nuxeo-elasticsearch-core/src/main/resources/OSGI-INF/elasticsearch-service.xml).


## Upon first startup

Initially the `elasticsearch` index will be empty.

To populate it you have 2 options :

 - if you have a small repository : 
     - Go in Admin Center / elasticsearch / config 
     - use the Reindex button (and wait a few seconds)

- if you have a big repository
     - check
	   https://github.com/nuxeo/nuxeo-elasticsearch/tree/master/scripts
	   you will find a script to dump Nuxeo documents and import them
	   into Elasticsearch.

## Reporting problems

To understand why a document is not present in a search results or not
indexed. You can activate a debug trace, insert the following lines in
the `lib/log4j.xml` file:

      <appender name="ELASTIC" class="org.apache.log4j.FileAppender">
        <errorHandler class="org.apache.log4j.helpers.OnlyOnceErrorHandler" />
        <param name="File" value="${nuxeo.log.dir}/elastic.log" />
        <param name="Append" value="false" />
        <layout class="org.apache.log4j.PatternLayout">
          <param name="ConversionPattern" value="%d{ISO8601} %-5p [%t][%c] %m%X%n" />
        </layout>
      </appender>

      <category name="org.nuxeo.elasticsearch" additivity="false">
        <priority value="TRACE" />
        <appender-ref ref="ELASTIC" />
      </category>

The `elastic.log` will contains all the requests done by Nuxeo to
Elasticsearch including the `curl` command ready to be copy/paste in a
term.

If you run in default embedded mode you need to enable the HTTP access
to perform request, just add `elasticsearch.httpEnabled=true` on your
`nuxeo.conf`.

## Limitations

See the [nuxeo-elasticsearch addon limitations](https://github.com/nuxeo/nuxeo-elasticsearch/blob/master/README.md).

