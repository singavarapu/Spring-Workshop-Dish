# Lab 2: Spring Boot Actuator

## part 1: Pick up where we left off
1. navigate to your project from lab 1
1. alternatively, you can go to start.spring.io and
    1. go through all the steps from lab 1
    1. when you specify dependencies, add `actuator` and `HATEOAS`

image::lab2_boot.png[]
1. assuming, you don't want to repeat yourself, open the pom.xml from lab 1
1. add the following 2 dependencies

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.hateoas</groupId>
    <artifactId>spring-hateoas</artifactId>
</dependency>

```
1. spring-boot-starter-actuator is the guts of all the actuator endpoints
1. spring-hateoas:
    1. HATEOAS = Hypermedia As The Engine Of Application State
    1. provides functionality for the /actuator endpoint to allow it to point to all the other endpoints
    1. if you remove the hateoas dependency, actuator still works, you just don't get the /actuator endpoint itself

## part 2: run locally and explore
1. stop your previous version if it is still running
1. `./mvnw spring-boot:run`
1. visit http://localhost:8080/actuator
1. follow the various links
1. if you don't have the _JSONView_ chrome extension or something similar, consider installing it
1. there is some documentation of the endpoints http://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-endpoints.html[here]

## part 3:  cf push

1. repackage `./mvnw clean package`
1. push your app:  `cf push`
    1. visit http://hello-your-random.yoursite.com/actuator
    1. note some of the differences from your local version
1. start thinking about lunch

## part 4: extra credit

1. Your company uses routers manufactured with recycled electronics from Russian submarines.  As a result, they you need to provide a custom health message that includes: 'положение_дел:вверх' in your `/health` endpoint to route requests properly.  Refer to http://www.baeldung.com/spring-boot-actuators
1. When certain requests come in, your app spins out of control  Your support team is asking for thread and heap dumps.  Can you provide them without logging into the server and running commands from a shell?
1. Support would also like to see a dump of the last N requests to come in.
1. They would also like self-service access to the app's overall health when it isn't spinning out of control,  What can you give them?
1. You think you might have left the "SPIN_OUT_OF_CONTROL_WHEN_CAT_VIDEOS_ARE_REQUSTED" environment variable set to true.  How can you validate the apps environment variables?
1. the /greeting endpoint doesn't seem to be mapped.  How can we determine all the endpoint mappings?




