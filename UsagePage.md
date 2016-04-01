### Prerequisites ###

The plugin requires Maven version 3.0.3 or higher. Unfortunately, it doesn't work with earlier maven versions.

### Simple usage ###

Suppose your project's classpath needs to include all jar files located in the `lib` directory of your project (including subdirectories). To achieve this you should put the following declaration to the list of plugins in your `pom.xml`:

```
<plugin>
    <groupId>com.googlecode.addjars-maven-plugin</groupId>
    <artifactId>addjars-maven-plugin</artifactId>
    <version>1.0.5</version>
    <executions>
        <execution>
            <goals>
                <goal>add-jars</goal>
            </goals>
            <configuration>
                <resources>
                    <resource>
                        <directory>${basedir}/lib</directory>
                    </resource>
                </resources>
            </configuration>
        </execution>
    </executions>
</plugin>
```

### Advanced usage ###

Apart from specifying the directory of the jar resources to be added, you can also specify the dependency scope and lists of includes and excludes:

```
<plugin>
    <groupId>com.googlecode.addjars-maven-plugin</groupId>
    <artifactId>addjars-maven-plugin</artifactId>
    <version>1.0.5</version>
    <executions>
        <execution>
            <goals>
                <goal>add-jars</goal>
            </goals>
            <configuration>
                <resources>
                    <resource>
                        <directory>${basedir}/lib</directory>
                        <includes>
                            <include>**/*.jar</include>
                        </includes>
                        <excludes>
                            <exclude>${basedir}/lib/runtime/**/*.jar</exclude>
                        </excludes>
                    </resource>
                    <resource>
                        <directory>${basedir}/lib/runtime</directory>
                        <scope>runtime</scope>
                    </resource>
                </resources>
            </configuration>
        </execution>
    </executions>
</plugin>
```

In the given example, all jar files located in the `lib` directory, except for the ones located in `lib/runtime`, will be added to your project with scope `compile`. The other files (located in `lib/runtime`) will be added with scope `runtime`.

The includes and excludes have the same meaning as in the main resources section (see http://maven.apache.org/pom.html#Resources).

### IDE, `addjars-maven-plugin`, and autocompletion ###

If you use NetBeans (not sure about other IDEs) and the `addjars-maven-plugin` configuration is used in a module of packaging `jar` or `war`, you will probably have problems with autocompletion. That's because NetBeans doesn't consider the plugin configuration.

Fortunately, there is a solution.
The project should have multi-module setup and consist of two modules at least.

First of the modules should be of packaging `pom` and include the `addjars-maven-plugin` configuration:

```
<project xmlns="http://maven.apache.org/POM/4.0.0" 
		 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
                      http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
	
  <parent>
    <groupId>my.group.id</groupId>
    <artifactId>parent</artifactId>
    <version>my-project-version</version>
  </parent>
	
  <artifactId>lib</artifactId>
  <packaging>pom</packaging>
	
  <build>
    <plugins>
      <plugin>
        <groupId>com.googlecode.addjars-maven-plugin</groupId>
        <artifactId>addjars-maven-plugin</artifactId>
        <version>1.0.5</version>
        <executions>
          <execution>
            <goals>
                <goal>add-jars</goal>
            </goals>
            <configuration>
                <resources>
                    <resource>
                        <directory>${basedir}/lib</directory>
                    </resource>
                </resources>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
	
</project>
```

Other (probably `jar` or `war`) module(s), which depend on the third party jars, should refer to the first module in their `dependencies` sections:

```
<project xmlns="http://maven.apache.org/POM/4.0.0" 
		 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
                      http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
	
  <parent>
    <groupId>my.group.id</groupId>
    <artifactId>parent</artifactId>
    <version>my-project-version</version>
  </parent>
	
  <artifactId>webapp</artifactId>
  <packaging>war</packaging>
	
  ...

  <dependencies>
    <dependency>
      <groupId>${project.groupId}</groupId>
      <version>[${project.version}]</version>
      <artifactId>lib</artifactId>
      <type>pom</type>
    </dependency>
    ...
  </dependencies>
	
</project>
```