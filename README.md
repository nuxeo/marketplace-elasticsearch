Elasticsearch Engine for Nuxeo Platform
=======================================

## About

This project allows to build the Marketplace package for the
`nuxeo-elasticsearch` addon.

The goal of this package is to provide an easy way to test
`Elasticsearch` with Nuxeo: elasticsearch.indexNumberOfShards
 - simple installation
 - default configuration for embedded Elasticsearch
 - default configuration to replace default PageProviders by
   `Elasticsearch` based ones


## Building

To build and run the tests, simply start the Maven build:

    mvn clean install


## Installing

To install the package:

 1. Take a fresh Nuxeo CAP (>= 5.9.4).

 2. Install the nuxeo-elasticsearch package:
      - From the AdminCenter (Upload + install)
      - From the command line using `nuxeoctl mp-install package.zip`

## Configuring

The default configuration uses an embedded Elasticsearch instance
which is running in the same JVM as the Nuxeo Platform. By default the
Elasticsearch indexes will be located in
`nxserver/data/elasticsearch`.

This embedded mode *is only for testing purpose* and should not be used
in production.

To connect to an existing `Elasticsearch 1.1.2` cluster you need to edit the
`nuxeo.conf` and add the following lines:


    elasticsearch.addressList=localhost:9300,anothernode:9300
    elasticsearch.clusterName=elasticsearch
    elasticsearch.indexName=nuxeo


Where:
- `addressList` points to one or many Elasticsearch nodes.
- `clusterName` is the cluster name to join, `elasticsearch` being the
  default cluster name of a debian package.

Look at the
[`nuxeo.defaults`](https://github.com/nuxeo/marketplace-elasticsearch/blob/master/package/src/main/resources/install/templates/elasticsearch/nuxeo.defaults)
in the Elasticsearch template for more configuration options.


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
        </elasticSearchIndex>
      </extension>
    </component>

You can see the
[default configuration here](https://github.com/nuxeo/nuxeo-elasticsearch/blob/master/nuxeo-elasticsearch-core/src/main/resources/OSGI-INF/elasticsearch-default-index-contrib.xml),
and mapping
[extension point documentation here](https://github.com/nuxeo/nuxeo-elasticsearch/blob/master/nuxeo-elasticsearch-core/src/main/resources/OSGI-INF/elasticsearch-service.xml).


## Upon First Startup

Initially the `elasticsearch` index will be empty.

To populate it you have two options:

 - Using the admin center:

     1. Go to Admin Center > Elasticsearch > Admin 

     2. Use the ReIndex button
        This is an asynchron job, you can see in the Admin Center > Elasticsearch > Info
        when the indexing is done.

 - Using the REST API you can dump data, then run a bulk import:
   https://github.com/nuxeo/nuxeo-elasticsearch/tree/master/scripts


## Reporting Problems

To understand why a document is not present in search results or not
indexed, you can activate a debug trace, insert the following lines in
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

The `elastic.log` will contain all the requests done by Nuxeo to
Elasticsearch including the `curl` command ready to be copied/pasted in a
term.

If you run in default embedded mode you need to enable the HTTP access
to perform request, just add `elasticsearch.httpEnabled=true` in your
`nuxeo.conf`. Note that this is only for debug purpose you should never
ever expose publicly the Elasticsearch ports.

## Limitations

See the [nuxeo-elasticsearch addon limitations](https://github.com/nuxeo/nuxeo-elasticsearch/blob/master/README.md).

