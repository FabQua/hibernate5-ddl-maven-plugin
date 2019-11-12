# DDL Generator for Hibernate 5 Usage

## Usage

### General usage

#### Include the plugin into Maven

To use the plugin add it the the build plugins in your POM:

```
<project>
    [...]
    <build>
        [...]
        <plugin>
            <groupId>de.jpdigital</groupId>
            <artifactId>hibernate5-ddl-maven-plugin-hibernate52</artifactId> <!-- Change to match your Hibernate version -->
            <version>2.0.0</version> <!-- Change to latest version available -->
            <configuration>
                <dialects>
                    <param>postgresql9</param>
                </dialects>
                <customDialects> <!-- Optional -->
                    <param>org.example.dialects.FooDBDialect</param>
                </customDialects>
                <packages>
                    <param>org.example.entities</param>
                </packages>
            </configuration>
            <executions>
                <execution>
                    <goals>
                        <goal>gen-ddl</goal>
                    </goals>
                    <phase>process-classes</phase>
                </execution>
            </executions>
        </plugin>
        [...]
    </build>
    [...]
</project>
```

### Configuration parameters

The plugin has two mandatory configuration parameters. The first on are the 
dialects for which DDL scripts will be generated. There are two options for 
configured the dialects. The first option is to use the `dialects` 
parameter. This parameter allows to specify the names of the 
SQL dialects for which a schema is generated by their name.
For a list of supported dialects refer to the JavaDoc of the 
Dialect:hibernate5-ddl-maven-plugin-core/apidocs/index.html?de/jpdigital/maven/plugins/hibernate5ddl/Dialect.html}} 
enumeration. The second option is to use the `customDialects` parameter.
The `customDialects` parameter allows you to specify the dialects to use
with the fully qualified name of the class which provides the implementation
of the dialect. The class has must be available on the class path at build 
time. Custom dialects are supported since version 2.1.

The second parameter are the packages which contain the entity classes. 

If you are using Envers in some of your entities no additional configuration
is necessary. The usage of Envers is automatically detected by Hibernate.

### Other parameters

Starting with version 2.1 it is possible to customize the name of the output 
file(s) using several parameters:

* `outputDirectory` allows you to change the name of directory where the 
  generated files are put. The default is 
  `${project.build.directory}/generated-resources/sql/ddl/auto>`. The 
    placeholder `${project.build.directory}` is the path of the `target`
    directory. 

* `outputFileNamePrefix` If this parameter is set the name of each generated
  file will be prefixed with the string set in this parameter.

* `outputFileNameSuffix` If this parameter is set the value will be appended 
  the name of each generated to the name of each generated file.

* `omitDialectFromFileName` If set to `true` *and* only one dialect
  *and* either `outputFileNamePrefix` or `outputFileNameSuffix` are
  set the name of the dialect will be omitted from the name of the generated 
  file. If both `outputFileNamePrefix` and `outputFileNameSuffix` are set their
  values are concatenated.

* `includeTestClasses` If set to true entities classes in `src/test` are
  are included into the generated schema.

* Starting with version 2.2 only specific properties from the 
  `persistence.xml` file are passed to hibernate. This properties can be
  configured using `persistencePropertiesToUse` parameter. Only properties
  starting with the strings listed in this parameter are passed to Hibernate.
  If the 
  parameter is not a default configuration equivalent to the following
  configuration will be used:

```
<project>
    [...]
    <build>
        [...]
        <plugin>
            <groupId>de.jpdigital</groupId>
            <artifactId>hibernate5-ddl-maven-plugin-hibernate52</artifactId> <!-- Change to match your Hibernate version -->
            <version>2.2.0</version> <!-- Change to latest version available -->
            <configuration>
                [...]
                <persistencePropertiesToUse>
                    <param>format_sql</param>
                    <param>use_sql_comments</param>
                    <param>hibernate.id.new_generator_mappings</param>
                    <param>org.hibernate.envers.audit_strategy</param>
                </persistencePropertiesToUse>
            </configuration>
            <executions>
                [...]
            </executions>
        </plugin>
        [...]
    </build>
    [...]
</project>
```

## Maven repository

The plugin is available from the Maven Central repository, therefore no
special configuration is needed.

## Include generated SQL scripts into project archive

The default output directory for the SQL DDL scripts is 
`${project.build.directory}/generated-resources/sql/ddl/auto`. 
`${project-build.directory}` is usually the `target` directory of your
project. To include the SQL scripts into the project archive you must add the
directory in the `resources` element inside the your build configuration, 
for example like this:

```
<project>
    [...]
    <build>
        [...]
        <resources>
            <resource>
                <directory>${project.build.directory}/generated-resources</directory>
            </resource>
        </resources>
        [...]
    </build>
    [...]
</project>
```

## Using generated SQL scripts in the tests

If you want to use the generated SQL DDL scripts in your tests you must also
add the `generated-resources` directory to the test classpath:

```
<project>
    [...]
    <build>
        [...]
        <plugins>
            [...]
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.18.1</version>
                <configuration>
                    [...]
                    <additionalClasspathElements>
                        <additionalClasspathElement>${project.build.directory}/generated-resources</additionalClasspathElement>
                    </additionalClasspathElements>
                    [...]
                 </configuration>
            </plugin>
            [...]
        </plugins>
        [...]
    </build>
    [...]
</project>
```
