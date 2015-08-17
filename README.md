[Trippi](http://trippi.sourceforge.net) RDF SPI implementation using SPARQL Update over HTTP.

To build (requires Java 8):

    cd trippi-sparql-update ; mvn clean install

This will result in a large JAR in the `target/` directory which contains the connector and its dependencies. To use this connector with Fedora 3, this large JAR can be dropped into the `WEB-INF/lib/` directory of a Fedora 3 web-application. Then, a `<datastore/>` element must be configured in `fedora.fcfg`. An example is shown below featuring the various parameters available. For each parameter except `connectorClassName` and `sparqlUpdateEndpoint`, a default will be used if no `<param/>` element for that parameter is supplied.

    <datastore id="sparqlUpdateTriplestore">
        <comment>Triplestore addressed by SPARQL-over-HTTP used by the Resource Index</comment>
        <param name="connectorClassName" value="com.asoroka.trippi.impl.sparqlupdate.SparqlUpdateConnector">
            <comment>The name of the Trippi Connector class used to communicate with the triplestore.</comment>
        </param>
        <param name="maxHttpConnections" value="10">
            <comment>The maximum number of clients in the HTTP client pool used for SPARQL Update requests.</comment>
        </param>
        <param name="sparqlUpdateEndpoint" value="http://localhost:3030/fuseki/update">
            <comment>The URL of a SPARQL Update endpoint for the triplestore to be used with this connector.</comment>
        </param>
        <param name="autoFlushDormantSeconds" value="5">
          <comment>Seconds of buffer inactivity that will trigger an auto-flush. If this threshold is reached, flushing will occur in the background, during which time the buffer is still available for writing.</comment>
        </param>
        <param name="bufferSafeCapacity" value="40000">
            <comment>The maximum size the buffer can reach before being forcibly flushed. If this threshold is reached, flushing will occur in the foreground and the buffer will be locked for writing until it is finished. This should be larger than autoFlushBufferSize.</comment>
        </param>
        <param name="bufferFlushBatchSize" value="20000">
            <comment>The number of updates to send to the triplestore at a time. This should be the same size as, or smaller than autoFlushBufferSize.</comment>
        </param>
        <param name="autoFlushBufferSize" value="20000">
            <comment>The size at which the buffer should be auto-flushed. If this threshold is reached, flushing will occur in the background, during which time the buffer is still available for writing.</comment>
        </param>
    </datastore>

Lastly, the `<module role="org.fcrepo.server.resourceIndex.ResourceIndex"/>` section must be altered so that the value of the `value` attribute in`<param name="datastore"/>` is the ID used above for the new `<datastore/>`, e.g.

    <module role="org.fcrepo.server.resourceIndex.ResourceIndex" class="org.fcrepo.server.resourceIndex.ResourceIndexModule">
        <comment>Supports the ResourceIndex.</comment>
        <param name="datastore" value="sparqlUpdateTriplestore">
            <comment>(required)  Name of the triplestore to use. WARNING: changing the  triplestore running the Resource Index Rebuilder.</comment>
        </param>
        …
    </module>