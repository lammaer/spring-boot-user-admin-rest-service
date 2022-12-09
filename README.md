# Spring Boot User Admin Rest Service

### How to build and start the application?
```
./mvnw clean install
./mvnw spring-boot:run -pl webservice
```
Launch as compiled jar
```
mvn clean install
java -jar webservice/target/userwebservice-0.0.1-SNAPSHOT.jar
```

Launch as docker image
```
mvn clean install
docker run -p 8080:8080 userwebservice:0.0.1-SNAPSHOT
```

### Api Documentation
```
http://localhost:8080/api/swagger-ui/
```

### Testing
standalone-e2e-test module contains E2E(or IT) tests what can be executed against a fully built and running service instance
present either on local machine or a deployed one (to prod)
These tests are also executed during the build of the service, the webservice docker image started and stopped automatically by 
the standalone-e2e-test module. 



## Run E2E (or so called IT) tests

Start the previously built docker image locally and run the tests against it
```
mvn clean verify -pl standalone-e2e-test
```
With -DskipStartServiceLocally=true option, test just executed without starting the service (so need to start it separately)


## Test execution on different environments

With -P<env> setting (maven profile) you can control the env under test
```
mvn clean verify -pl standalone-e2e-test -Pprod
```
options:
-Pdev -> http://localhost:8080/  (default)

-Pprod -> http://prod:8080/

Run a single test class: -Dtest=SwaggerEndpointTestIT

### Todo: 
bump spring-boot-starter-parent to latest - that means need to bump to swagger 3 (openapi) 
https://stackoverflow.com/questions/72357737/i-am-getting-this-error-failed-to-start-bean-documentationpluginsbootstrapper

### Additional notes
PR link
```
https://github.com/kovaku/spring-boot-user-admin-rest-service/pull/1
```

Dockerize the webservice & build image during mvn build :
https://medium.com/swlh/build-a-docker-image-using-maven-and-spring-boot-58147045a400
https://spring.io/guides/gs/spring-boot-docker/
docker-maven-plugin - http://dmp.fabric8.io/#introduction

Other useful docs:
how to build jar: https://docs.spring.io/spring-boot/docs/1.1.x/reference/html/howto-build.html
Dockerfile: https://www.baeldung.com/java-dockerize-app#writing-the-dockerfile
https://dzone.com/articles/build-docker-image-from-maven - instead of it I used dockerfile-maven-plugin

Command for manual build and execution
docker build -t userwebservice/latest ./webservice
docker run -p 8080:8080 userwebservice/latest

--------

How standalone tests launch docker image:

fabric8 docker-maven-plugin "start" and "stop" goals controls the docker image
Both goals are bound to the pre-integration-test and post-integration-test phase
respectively. It is recommended to use the maven-failsafe-plugin for integration testing in 
order to stop the docker container even when the tests fail.

This is why maven-failsafe-plugin needed (instead of the surefire-plugin) to introduce 
pre-integration-test and post-integration-test phases
To make sure test are picked up only by the failsafe plugin and not by the surefire plugin, tests renamed to 
*IT.java format.

Docker image created in the webservice submodule, using  also the fabric8 docker-maven-plugin