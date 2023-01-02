# openshift-workload-monitoring

This project is maded for test `OpenShift Workloads Monitoring` using **Thanos**, **Prometheus** and **Grafana**.

## How to re-create this project?

1. To create this project from the beginning, make a new file called: [mvn-settings.xml](./mvn-settings.xml)

2. Then, run the next command:
    ```bash
    $ mvn -s mvn-settings.xml org.apache.maven.plugins:maven-archetype-plugin:2.4:generate \ 
        -DarchetypeCatalog=https://maven.repository.redhat.com/ga/io/fabric8/archetypes/archetypes-catalog/2.2.0.fuse-sb2-780040-redhat-00002/archetypes-catalog-2.2.0.fuse-sb2-780040-redhat-00002-archetype-catalog.xml \
        -DarchetypeGroupId=org.jboss.fuse.fis.archetypes \     
        -DarchetypeArtifactId=spring-boot-camel-xml-archetype \
        -DarchetypeVersion=2.2.0.fuse-sb2-780040-redhat-00002
    ```

    > **NOTE:** the previous command will ask you for data input for the project like: groupId, artifactId, version and package. Complete it with your values.

3. Add next dependency in `pom.xml` file:
    ```xml
    <!-- Camel Servlet -->
    <dependency>
        <groupId>org.apache.camel</groupId>
        <artifactId>camel-servlet-starter</artifactId>
    </dependency>

    <!-- Metrics -->
    <dependency>
        <groupId>org.apache.camel</groupId>
        <artifactId>camel-micrometer-starter</artifactId>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-registry-prometheus</artifactId>
    </dependency>
    ```

4. Change [src/main/resources/spring/camel-context.xml](./workloads/src/main/resources/spring/camel-context.xml) to fit with this:
    ```xml
    <!-- Define a traditional camel context here -->
    <camelContext id="camel" xmlns="http://camel.apache.org/schema/spring">
      <route id="test">
        <from uri="timer:myTimer?period=60s"/>
        <to uri="direct:main"/>
      </route>
      <route id="main">
        <from uri="direct:main"/>
        <log message="got a request from client"/>
      </route>
    </camelContext>
    ```

5. Add next properties to [src/main/resources/application.properties](./workloads/src/main/resources/application.properties) file:
    ```ini
    server.port=8080

    # Enable actuator
    management.endpoints.web.exposure.include = info,health,prometheus

    # Metrics
    metrics = */1 * * * *
    ```

6. Build & Run:
    ```bash
    $ cd workloads
    $ mvn -s ../mvn-settings.xml clean package
    ```