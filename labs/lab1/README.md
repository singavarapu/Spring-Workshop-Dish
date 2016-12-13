# Lab 1: Spring Boot Basics

## part 1: run locally
1. visit link:http://start.spring.io[start.spring.io]
+
 <image::lab1_boot.png[] create image and link>
1. configure your project
1. group name
1. artifact name (*_'hello'_* is recommended)
1. *_maven_* for your build system
1. spring boot version 1.4.1
1. add dependencies
1. Web
1. 'Generate Project'
1. unzip the downloaded file
1. open in IDE or cd to project directory for vim/emacs
1. open pom.xml - notice the spring starters
1. create class: `HelloController` in same directory as the Boot app class
1. annotate the class with `@RestController` (package: `org.springframework.web.bind.annotation`)
1. create method: `public String sayHello() { return "Hello World"; }`
1. annotate the method with: `@RequestMapping("/")` (package: `org.springframework.web.bind.annotation`)
1. Your source should look something like this:

```
 package bootworkshop.pivotal.io;

 import org.springframework.web.bind.annotation.RequestMapping;
 import org.springframework.web.bind.annotation.RestController;

 /**
  * Created by rhardt on 9/21/16.
  */
 @RestController
 public class HelloController {

     @RequestMapping("/")
     public String sayHello(){
         return "Hello DISH!";
     }

 }
 ```

1. from project root directory, run `mvn sprint-boot:run`
1. point a web browser to http://localhost:8080
1. bask in the glory that is Spring Boot

## part 2: push to Pivotal Cloud Foundry
1. make sure you have all of your link:/Spring-Workshop-Dish/pre-requisites/Spring_Boot_Labs_Setup.html[prerequisites] in place.
1. build a runnable jar `./mvnw package`
1. log in to cloud foundry `cf login -a api.yourpcfsystem.com`
1. when prompted, provide your email and password
1. if prompted, choose an appropriate org and space
1. push your app:  `cf push hello -p ./target/hello-0.0.1-SNAPSHOT.jar -n "hello-rob" -b java_buildpack`
    1. instead of 'hello-rob' give your app a unique route (the `-n` option)
    1. depending on what you called your app, the runnable jar (the `-p` option) may be different
    1. watch the output, it will tell you the url of your app

1. Creating app hello in org Vertical / space rhardt-sandbox as rhardt@pivotal.io...
    OK

    Creating route hello-rob.cfapps.io...
    OK

    Binding hello-rob.cfapps.io to hello...
    OK
    
1. open a web browser to the route of your app - from the output of the `cf push`
1. celebrate
1. silently complain about the so-un-2016-uber-long command you must issue to push to Cloud Foundry
1. stop complaining and put it in a manifest.yml at the top level of your project
```---
applications:
- name: hello
  buildpack: java_buildpack
  host: hello-${random-word}
  path: target/hello-0.0.1-SNAPSHOT.jar
```
1.  `cf push`
1. `cf apps` will show you all the deployed apps in your space along with their vital stats and mapped routes.  Notice the random words from the manifest file.

$ ```cf apps```
Getting apps in org Vertical / space rhardt-sandbox as rhardt@pivotal.io...
OK

name                  requested state   instances   memory   disk   urls
ep-shell              started           1/1         128M     1G     ep-shell.cfapps.io
hello                 started           1/1         1G       1G     hello-rob.cfapps.io, hello-unscaled-multimillion.cfapps.io
vto-server-loadtest   stopped           0/1         4G       1G     vto-server-rob.cfapps.io

1. verify your app at its new randomly-generated route

## Part 3: Extra Credit

1.  Why `@RestController`?
1. Allows you to return strings, but also enables some interesting features
1. Add this class to your HelloController.java file:

```
  class Greeting {
    private String message;
    private String greeting;
    private boolean happyToSeeYou;

    //constructor, getters, setters omitted for brevity

}
```
1.  and add this method:

```
   @RequestMapping("/greeting")
    public Greeting greet() {
        return new Greeting("Hello", "DISH", true);
    }
```
1. now `cf push` and visit http://hello-random-words.yourhost.com/greeting
1. marvel at your newfound speed and productivity







