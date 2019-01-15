# Cassandra Java Driver CLI Archetype

This is a Maven Archetype project that can be used to bootstrap a simple Maven project featuring the
Java Drivers for Cassandra, with a basic command line interface implementation that is similar to
`cqlsh`. To build _this_ project, simply run a Maven install (if you haven't already built the
parent):

```
mvn clean install
```

This will install the archetype locally so you can then use it to create a bootstrap project.

## Working with this Maven Archetype project
If you are looking to create a bootstrapped project, you can skip down to
[Creating a Bootstrap project](#creating-a-bootstrap-project)

### Archetype Metadata
The [archetype-metadata.xml][1] conforms to the [Maven Archetype Descriptor Model][2]. It defines
properties that must be set to generate a bootstrapped project as well as file set rules that control
how source files are generated. The file must live in `src/main/resources/META-INF/maven` of _this_
project.

### Archetype sources
All of the source and resource files to be generated into the bootstrapped project need to be
located in `src/main/resources/archetype-resources` directory of _this_ project. A template of the
generated `pom.xml` is located in this directory, as well as a basic README.md. Any other files that
should be copied into the root of the generated project should be placed here. Adding files here
will require updates to [archetype-metadata.xml][1] to ensure they are explicitly listed in the
`<fileSet>` that is copied during project generation.

The project will have sub-directories under `src/main/resources/archetype-resources` that will have
the typical module directory structure:

```
archetype-resources/src/main/java
archetype-resources/src/main/resources
archetype-resources/src/test/java
archetype-resources/src/test/resources
```

The only difference is that the Java source and test class files are not in packaged subdirectories,
they are flattened into the `java` directories. This is because the `archetype:generate` goal will
copy those sources, substituting any Velocity properties with values provided, into the desired
package structure, based on the value provided for the `package` property. See
[Archetype Properties](#archetype-properties) for more on the `package` property.

## Creating a Bootstrap project

Generating the bootstrap project requires some properties to be set. Some properties have default
values and do not need to be specified. All properties can be provided on the command-line used to
generate the project, and all can be modified interactively after executing the generate command.

### Archetype Properties
The following properties are used during the bootstrap project generation. They can be specified
when executing the generate command, or in interactive mode during bootstrap project generation:

| Property            | Default               | Example             | Description |
| ------------------- | --------------------- | ------------------- | ------------|
| groupId             | &lt;no default&gt;    | com.mycompany.group | Sets the Maven `groupId` value in the pom.xml of the bootstrapped project |
| artifactId          | &lt;no default&gt;    | myArtifact          | Sets the Maven 'artifactId' value in the pom.xml of the bootstrapped project |
| version             | 1.0-SNAPSHOT          | 1.0.0-SNAPSHOT      | Sets the Maven `version` value in the pom.xml of the bootstrapped project |
| package             | &lt;groupId value&gt; | com.mycompany.poc   | Controls the base package for all generated source code |
| java-driver-version | [4.0.0-beta1,)        | 4.0.0               | The Java Driver version to use (NOTE: only 4.x and newer versions are supported) |
| cassandra-host      | 127.0.0.1             | 10.10.1.16          | The host or IP address of the Cassandra instance to which to connect |
| cassandra-port      | 9042                  | 9042                | The port to use for the Cassandra connection |
| datacenter          | datacenter1           | datacenter1         | The name of the local datacenter |
| logging             | logback-classic       | log4j               | The SLF4J logging implementation to use (NOTE: only `logback-classic` or `log4j` are supported)

### Example generate commands

#### Bare-bones command
```
mvn archetype:generate -DarchetypeGroupId=com.datastax.oss -DarchetypeArtifactId=java-driver-archetype
```
The above will generate the bootstrap project, but you will have to provide values for any required
properties that have no default (at least`groupId` and `artifactId`). These values are used to setup
your bootstrap project `pom.xml` and generate source code in the desired `package`.

#### Specify everything command
```
mvn archetype:generate -DarchetypeGroupId=com.datastax.oss -DarchetypeArtifactId=java-driver-archetype -Dversion=1.0.0-SNAPSHOT -DgroupId=com.mycompany.group -DartifactId=myArtifact -Dpackage=com.mycompany.poc -Dcassandra-host=127.0.0.1 -Dcassandra-port=9042 -Ddatacenter=datacenter1 -Djava-driver-version="[4.0.0-beta1,)" -Dlogging=logback-classic
```
The above will generate a project with all properties set, though you can still override them
interactively.

#### Interactive confirmation
Regardless of how you execute the command, you will get a confirmation dialog with all the property
values shown. If you are happy with what you see, simply hit `enter` to proceed. If you wish to
change any of the values, type in `N` and hit `enter` to start the interactive edit of the values.

```
[INFO] Using property: groupId = com.mycompany.group
[INFO] Using property: artifactId = myArtifact
[INFO] Using property: version = 1.0.0-SNAPSHOT
[INFO] Using property: package = com.mycompany.poc
[INFO] Using property: cassandra-host = 127.0.0.1
[INFO] Using property: cassandra-port = 9042
[INFO] Using property: datacenter = datacenter1
[INFO] Using property: java-driver-version = [4.0.0-beta1,)
[INFO] Using property: logging = logback-classic
Confirm properties configuration:
groupId: com.mycompany.group
artifactId: myArtifact
version: 1.0.0-SNAPSHOT
package: com.mycompany.poc
cassandra-host: 127.0.0.1
cassandra-port: 9042
datacenter: datacenter1
java-driver-version: [4.0.0-beta1,)
logging: logback-classic
 Y: :
```

### Test the Bootstrap project

Once you complete the archetype generation, you should have a simple Maven project using the latest
4.x Java Driver in a sub-directory named the same as the `artifactId` you provided, in the current
working directory. Change into that directory and execute the following:

```
mvn compile exec:java -Dexec.mainClass=com.mycompany.poc.Main
```

This should produce output similar to:

```
15:57:32.944 [com.mycompany.poc.Main.main()] INFO  c.d.o.d.i.c.DefaultMavenCoordinates - DataStax Java driver for Apache Cassandra(R) (com.datastax.oss:java-driver-core) version 4.0.0-beta1-SNAPSHOT
15:57:33.200 [myArtifact-admin-0] INFO  c.d.o.d.internal.core.time.Clock - Using native clock for microsecond precision
15:57:33.431 [com.mycompany.poc.Main.main()] INFO  com.mycompany.poc.Main - Cassandra release version: 3.11.3
```

#### Troubleshooting

If you see an error like this when trying to execute the example:

> [WARNING]
> com.datastax.oss.driver.api.core.AllNodesFailedException: Could not reach any contact point, make sure you've provided valid addresses (showing first 1, use getErrors() for more: /127.0.0.1:9042: com.datastax.oss.driver.api.core.connection.ConnectionInitException: [myArtifact|control|connecting...] init query STARTUP: error writing )

It means the example client can't connect to Cassandra. Check that Cassandra is running on the host
and port you specified, and that the client is not being blocked by any sort of firewall. When
running the example above, it usually indicates Cassandra isn't running or did not start up
correctly.

[1]: src/main/resources/META-INF/maven/archetype-metadata.xml
[2]: http://maven.apache.org/archetype/archetype-models/archetype-descriptor/archetype-descriptor.html