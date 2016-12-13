# Lab 3: Properties

## part 1: Pick up where we left off
1. navigate to your project from lab 2
1. alternatively, you can go to start.spring.io and
    1. go through all the steps from lab 1
    1. no additional dependencies are necessary for this lab


## part 2: create some properties files
1. in `src/main/resources`
1. add this to `application.properties`

```
greeter.details.name=DefaultDISH
```
1. create the file `application-p1.properties` and add:

```
greeter.details.name=P1_DISH
```
1. create the file `application-p2.properties` and add:

```
greeter.details.name=P2_DISH
```
1. create the file `application-cloud.properties` and add:

```
greeter.details.name=P1_IN_THE_CLOUD
```
1. create the file `application-p3.yml` and add:

```
greeter:
  details:
    name: P3_DISH_FROM_YAML
```
1. create the `GreeterSettings` class alongside your other classes.  Its contents would look something like this:

```
package bootworkshop.pivotal.io;

import org.springframework.boot.context.properties.ConfigurationProperties;

/**
 * Created by rhardt on 9/21/16.
 */
@ConfigurationProperties(prefix = "greeter.details")
public class GreeterSettings {

    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```
1. note that we're telling the class to look in all the Spring property sources for properties beginning with _greeter.details_ and then populate its bean values with anything after that prefix.
so _greeter.details.name_ will populate GreeterSettings' _name_ property.
1. autowire the GreeterSettings class into our HelloController

```
@RestController
@EnableConfigurationProperties(GreeterSettings.class)
public class HelloController {


    @Autowired
    private GreeterSettings greeterSettings;

    @RequestMapping("/")
    public String sayHello(){
        return String.format("Hello %s!", greeterSettings.getName());
    }
```
1. notice the 3 changes
    1. the `@EnableConfigurationProperties(GreeterSettings.class)` annotation
    1. the `@Autowired` GreeterSettings instance
    1. modified `sayHello()` method to use the property.

## part 3: running the app
1. stop any running versions of the app
1. `./gradlew bootrun`
1. visit http://localhost:8080/
1. what do you see?
1. stop the app and restart it with `./gradlew bootrun -Drun.profiles=p1`
1. refresh the web browser
1. stop the app and restart it with `./gradlew bootrun -Drun.profiles=p2`
1. refresh the web browser
1. stop the app and restart it with `./gradlew bootrun -Drun.profiles=p3`
1. refresh the web browser1.  Did it pick up the .yml?

## part 4:  cf push

1. repackage `./gradlew clean package`
1. push your app:  `cf push`
    1. visit http://hello-your-random.yoursite.com/
    1. which properties file did it pick up?
    1. do you remember specifying a profile anywhere to cloud foundry?
1. In production, we probably don't want our settings in properties files.  They should be runtime environment variables.
    1. Try this command `cf set-env hello GREETER_DETAILS_NAME Dish_From_The_Environment`
    1. Then `cf restage hello`
    1. refresh the web browser again.  What do you see?  And you should see an *opinionated runtime*!






