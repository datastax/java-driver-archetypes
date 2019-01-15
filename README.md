# Maven archetypes for the DataStax Cassandra(R) Java Driver

This is a Maven multi-Archetype project that can be used to bootstrap a simple Maven project
featuring the Java Drivers for Cassandra. Currently, only a basic Command Line Interface (CLI)
project exists, but more archetypes can be added as sub-modules. To build _this_ project, simply
run a Maven install:

```
mvn clean install
```

This will install all archetypes locally so you can then use it to create bootstrap projects.

## Working with this Maven Archetype project
If you are looking to create a bootstrapped project, you can skip down to
[Creating a Bootstrap project](#creating-a-bootstrap-project)

The following sections describe the general layout fo each Archetype template within this project.

### Archetype Metadata
The [archetype-metadata.xml][1] in the CLI archetype conforms to the
[Maven Archetype Descriptor Model][2]. It defines properties that must be set to generate a
bootstrapped project as well as file set rules that control how source files are generated. The file
must live in `src/main/resources/META-INF/maven` of **each** project.

### Archetype sources
All of the source and resource files to be generated into a bootstrapped project need to be
located in `src/main/resources/archetype-resources` directory of the project. A template of the
generated `pom.xml` is located in this directory, as well as a basic README.md. Any other files that
should be copied into the root of the generated project should be placed here. Adding files here
will require updates to `archetype-metadata.xml` to ensure they are explicitly listed in the
`<fileSet>` that is copied during project generation.

Each project will have sub-directories under `src/main/resources/archetype-resources` that will have
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
package structure, based on the value provided for the `package` property.

Also of note, the archetype-metadata descriptor in the CLI project only supports Java source files.
This is currently just an arbitrary limitation in the `fileSets` section of the descriptor. Adding
other JVM languages (Scala, Kotlin, etc) should be simple in the future by altering the filtering
in the descriptor as necessary.

## Creating a Bootstrap project

Each archetype will have its own set of properties that will need to be set in order to generate the
project. Most properties should have sensible defaults, but all can be specified or overridden on
the command line or in the interactive mode of `archetype:generate`. Please see the README for the
specific archetype for more details.

[1]: cli/src/main/resources/META-INF/maven/archetype-metadata.xml
[2]: http://maven.apache.org/archetype/archetype-models/archetype-descriptor/archetype-descriptor.html