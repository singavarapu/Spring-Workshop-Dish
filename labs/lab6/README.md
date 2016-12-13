# Lab 6: Spring Security

## part 1: simple authentication
1. navigate to your project from lab 4
1. open the `pom.xml` from lab 4
1. add the following dependency

[source, xml, numbered]
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```


1. `./mvnw spring-boot:run` 
1. visit http://localhost:8080/movies 
1. notice that you are promoted for a username and password
1. take a look at the output and look for the _password_1. You should see a print out: “Using default security password: …”1. 
1. the username is: user and the password is the one in the output.
1. once username/password have been entered you will be able to see the web page

## part 2: can we externalize the username / password used for authentication
1. create file *_application.yml_* in src/main/resources/
1. add the following YAML properties (note YAML is formatted using spaces, convert tabs to spaces in your IDE)

[source, YAML, numbered]
```
## Security
security:
  user:
    name: springboot
    password: workshop
```

1. `./mvnw spring-boot:run` 
1. visit http://localhost:8080/movies 
1. notice that you are promoted for a username and password
1. use the username (*_security.user.name_*) and password (*_security.user.password_*) from *_application.yml_* file

## part 3: let's introduce oauth2
1. open the `pom.xml` from lab 4
1. add the following dependency

[source, xml, numbered]
```
<dependency>
    <groupId>org.springframework.security.oauth</groupId>
    <artifactId>spring-security-oauth2</artifactId>
    <version>2.0.7.RELEASE</version>
</dependency>
```

1. add annotations to WatchingApplication class
@EnableAuthorizationServer
@EnableResourceServer

[source, xml, numbered]
```
@SpringBootApplication
@RestController
@EnableAuthorizationServer    // <----- add
@EnableResourceServer         // <----- add
public class WatchingApplication {
```

1. `./mvnw spring-boot:run` 
1. take a look at the output and look for the *_security.oauth2.client.clientId_* & *_security.oauth2.client.secret_*. you should see them printed out.
1. lets obtain a bearer token
1. replace *_<clientId>_* and *_<secret>_* for the values found in the output

[source, numbered]
```
curl <clientId>:<secret>@localhost:8080/oauth/token -d grant_type=password -d scope=read -d username=springboot -d password=workshop

{"access_token":"482af593-1f00-4451-8bda-2d54440bfe37","token_type":"bearer","refresh_token":"9f6384a7-b2b2-4907-b0d5-d487000dd002","expires_in":43199,"scope":"read"}
```

1. take note in the results, we received an access_token of type "bearer" with a refresh scope and expires in 43199. scope is set to read (handle on GET)
1. lets access the movie service

[source, numbered]
```
curl localhost:8080 -H "Authorization: Bearer <access_token>"
```

1. replace token with value from "access_token" received in response from oauth server, see example below

[source, json, numbered]
```
$ curl localhost:8080 -H "Authorization: Bearer 482af593-1f00-4451-8bda-2d54440bfe37"
{
  "_links" : {
    "movies" : {
      "href" : "http://localhost:8080/movies{?page,size,sort}",
      "templated" : true
    },
    "profile" : {
      "href" : "http://localhost:8080/profile"
    }
  }
}
```

[%hardbreaks]
.

## part 4: use supplied client-id and client-secret
1. add the following YAML stanza to *_application.yml_* file in src/main/resources/

[source, YAML, numbered]
```
## Security
security:
  user:
    name: springboot
    password: workshop
# add content ----
  oauth2:
    client:
      client-id: myid
      client-secret: mysecret
# add content ^^^^      
```

1. `./mvnw spring-boot:run` 
1. lets obtain a bearer token

[source, json, numbered]
```
curl myid:mysecret@localhost:8080/oauth/token -d grant_type=password -d scope=read -d username=springboot -d password=workshop
{"access_token":"482af593-1f00-4451-8bda-2d54440bfe37","token_type":"bearer","refresh_token":"9f6384a7-b2b2-4907-b0d5-d487000dd002","expires_in":43199,"scope":"read"}
```


1. take note in the results, we received an access_token of type "bearer" with a refresh scope and expires in 431991. scope is set to read (handle on GET)
1. lets access the movie service
`curl localhost:8080 -H "Authorization: Bearer <access_token>"`
1. replace token with value from "access_token" received in response from oauth server

[source, json, numbered]
```
$ curl localhost:8080 -H "Authorization: Bearer 482af593-1f00-4451-8bda-2d54440bfe37"
{
  "_links" : {
    "movies" : {
      "href" : "http://localhost:8080/movies{?page,size,sort}",
      "templated" : true
    },
    "profile" : {
      "href" : "http://localhost:8080/profile"
    }
  }
} 
```

.

## part 5: okay so how does one enable SSL
1. we've created a p12 in advance, copy labs/lab4/watching/keystore.p12 to your base directory for the project
1. add to *_application.yml_* these properties

[source, yml, numbered]
```
server:
  port: 8443
  ssl:
    enabled: true
    key-alias: spring-security
    key-store: keystore.p12
    key-store-type: PKCS12
    key-store-password: spring-security
    key-password: spring-security
```

1. lets obtain a bearer token

[source, numbered]
```
$ curl https://localhost:8443/oauth/token --insecure -u myid:mysecret -d grant_type=password -d scope=read -d username=springboot -d password=workshop -v
```

1. note with verbose turned on we can see TLS is enabled

[source, numbered]
```
TLS 1.2 connection using TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
```

1. replace token with value from "access_token" received in response from oauth server

[source, json, numbered]
```
$ curl -X GET -H "Authorization: Bearer 482af593-1f00-4451-8bda-2d54440bfe37" https://localhost:8443/movies --insecure
{
  "_embedded" : {
    "movies" : [ {
      "title" : "Despicable Me",
      "year" : "2010",
      "rated" : "PG",
      "released" : "09 Jul 2010",
      "runtime" : "95 min",
      "genre" : "Animation, Comedy, Family",
      "_links" : {
        "self" : {
          "href" : "https://localhost:8443/movies/1"
        },
        "movie" : {
          "href" : "https://localhost:8443/movies/1"
        }
      }
    }, {
      "title" : "Frozen",
      "year" : "2013",
      "rated" : "PG",
      "released" : "27 Nov 2013",
      "runtime" : "102 min",
      "genre" : "Animation, Adventure, Comedy",
      "_links" : {
        "self" : {
          "href" : "https://localhost:8443/movies/2"
        },
        "movie" : {
          "href" : "https://localhost:8443/movies/2"
        }
      }
    }, {
      "title" : "Toy Story",
```
