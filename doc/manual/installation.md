## Installation

This plugin is available from Maven central and can be connected to
pre- and post-integration phase as seen below. The configuration and
available goals are described below. 

````xml
<plugin>
  <groupId>io.fabric8</groupId>
  <artifactId>docker-maven-plugin</artifactId>
  <version>0.15.7</version>

  <configuration>
     ....
     <images>
        <!-- A single's image configuration -->
        <image>
           ....
        </image>
        ....
     </images>
  </configuration>

  <!-- Connect start/stop to pre- and
       post-integration-test phase, respectively if you want to start
       your docker containers during integration tests -->
  <executions>
    <execution>
       <id>start</id>
       <phase>pre-integration-test</phase>
       <goals>
         <!-- "build" should be used to create the images with the
              artifact --> 
         <goal>build</goal>
         <goal>start</goal>
       </goals>
    </execution>
    <execution>
       <id>stop</id>
       <phase>post-integration-test</phase>
       <goals>
         <goal>stop</goal>
      </goals>
    </execution>
  </executions>
</plugin>
````

When working with this plugin you can use an own packaging with a specialized lifecycle in order to keep your pom files small. Three packaging variants are available:
  
* **docker** : This binds `docker:build` to the package phase and `docker:start` / `docker:stop` to the pre- and post-integration phase respectively. Also `docker:push` is bound to the deploy phase.
* **docker-build** : Much like the *docker* packaging, except that there are no integration tests configured by default.
* **docker-tar** : Create a so called *Docker tar* archive which is used as the artifact and which later can be use for building an image. It contains essentially a `Dockerfile` with supporting files. See [docker:source](docker-source) for more details. 

These packaging definitions include the *jar* lifecycle methods so they are well suited simple Microservice style projects.
For example:

```
<pom>
  <artifactId>demo</artifactId>
  <version>0.0.1</version>
  <packaging>docker</packaging>
  ...
  <build>
    <plugins>
      <plugin>
        <groupId>io.fabric8</groupId>
        <artifactId>docker-maven-plugin</artifactId>
        <extensions>true</extensions>
        <configuration>
          <images>
            <image>
            ...
            </image>
          </images>
        </configuration>
      </plugin>
    </plugins>
    ....
  <build>
</pom>
```

This will create the jar (if any), build the Docker images, start the configured Docker containers, runs the integration tests, stops the configured Docker container when you enter `mvn install`. With `mvn deploy` you can additionally push the images to a Docker configuration. Please note the `<extensions>true</extensions>` which is mandatory when you use a custom lifecycle.

The rest of this manual is now about how to configure the plugin for your images.
