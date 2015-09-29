# Deploy a Java Web Application that launches with Jetty Runner

Follow each step to build an app from scratch, or skip to the end get the source for this article. You can also use almost any existing Maven webapp project.

## Prerequisites

* Basic Java knowledge, including an installed version of the JVM and Maven.
* Basic Git knowledge, including an installed version of Git.

## What is Jetty and Jetty Runner?
Jetty is a lightweight Java application server that offers a flexible array of options for how it can be launched. One popular option is using embedded Jetty the way that the [java quickstart](http://devcenter.heroku.com/java) does. Another good option is the Jetty Runner jar file. Each version of Jetty that is released includes a Jetty Runner jar. This jar can be run directly from the java command and can be passed a war file to load right on the command line. An example of this would be:

```term
$ java -jar jetty-runner.jar application.war
```

Jetty Runner will then launch a Jetty instance with the given war deployed to it.

## Create an application if you don't already have one

```term
$ mvn archetype:generate -DarchetypeArtifactId=maven-archetype-webapp
...
[INFO] Generating project in Interactive mode
Define value for property 'groupId': : com.example
Define value for property 'artifactId': : helloworld
```

(you can pick any groupId or artifactId). You now have a complete Java web app in the `helloworld` directory.

## Configure Maven to download Jetty Runner

Although not necessary for using Jetty Runner it's a good idea to have your build tool download Jetty Runner for you since your application will need it to run. You could, of course, just download Jetty Runner and use it to launch your application without doing this. However having all of your dependencies defined in your build descriptor is important for application portability and repeatability of deployment. In this case we're using Maven so we'll use the dependency plugin to download the jar. Add the following plugin configuration to your pom.xml:

```xml
<build>
  ...
  <plugins>
    ...
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-dependency-plugin</artifactId>
      <version>2.3</version>
      <executions>
        <execution>
          <phase>package</phase>
          <goals><goal>copy</goal></goals>
          <configuration>
            <artifactItems>
              <artifactItem>
                <groupId>org.mortbay.jetty</groupId>
                <artifactId>jetty-runner</artifactId>
                <version>9.3.3.v20150827</version>
                <destFileName>jetty-runner.jar</destFileName>
              </artifactItem>
            </artifactItems>
          </configuration>
        </execution>
      </executions>
    </plugin>
  </plugins>
</build>
```

## Run your application

To build your application simply run:

```term
$ mvn package
```

And then run your app using the java command:

```term
$ java -jar target/dependency/jetty-runner.jar target/*.war
```

That's it. Your application should start up on port 8080.

## Deploy your application to Heroku

## Create a Procfile

You declare how you want your application executed in `Procfile` in the project root. Create this file with a single line:

```
web: java $JAVA_OPTS -jar target/dependency/jetty-runner.jar --port $PORT target/*.war
```

## Optionally choose a JDK

By default, OpenJDK 1.8 is installed with your app. However, you can choose to use a newer JDK by specifying `java.runtime.version=1.7` in the `system.properties` file.

Here's what a `system.properties` file looks like:

```
java.runtime.version=1.7
```

You can specify 1.6, 1.7, or 1.8 (1.8 is in beta) for Java 6, 7, or 8 (with lambdas), respectively.

## Deploy to Heroku

Commit your changes to Git:

```term
$ git init
$ git add .
$ git commit -m "Ready to deploy"
```

Create the app:

```
$ heroku create
Creating high-lightning-129... done, stack is cedar-14
http://high-lightning-129.herokuapp.com/ | git@heroku.com:high-lightning-129.git
Git remote heroku added
```

Deploy your code:

```term
$ git push heroku master
Counting objects: 227, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (117/117), done.
Writing objects: 100% (227/227), 101.06 KiB, done.
Total 227 (delta 99), reused 220 (delta 98)

-----> Heroku receiving push
-----> Java app detected
-----> Installing Maven 3.3.3..... done
-----> Executing: mvn -B -DskipTests=true clean install
       [INFO] Scanning for projects...
       [INFO]                                                                         
       [INFO] ------------------------------------------------------------------------
       [INFO] Building petclinic 0.1.0.BUILD-SNAPSHOT
       [INFO] ------------------------------------------------------------------------
       ...
       [INFO] ------------------------------------------------------------------------
       [INFO] BUILD SUCCESS
       [INFO] ------------------------------------------------------------------------
       [INFO] Total time: 36.612s
       [INFO] Finished at: Tue Aug 30 04:03:02 UTC 2011
       [INFO] Final Memory: 19M/287M
       [INFO] ------------------------------------------------------------------------
-----> Discovering process types
       Procfile declares types -> web

-----> Compiled slug size is 62.7MB
-----> Launching... done, v5
       http://pure-window-800.herokuapp.com deployed to Heroku
```

Congratulations! Your web app should now be up and running on Heroku. Open it in your browser with:

```term
$ heroku open
```

## Clone the source

If you want to skip the creation steps you can clone the finished sample:

```term
$ git clone git@github.com:heroku/devcenter-jetty-runner.git
```
