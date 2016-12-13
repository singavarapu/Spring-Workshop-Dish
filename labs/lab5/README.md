# Lab 5: Spring Data REST

## part 1: we need Spring Boot's starter for Data REST
1. navigate to your project from lab 4
1. open the `pom.xml` from lab 4
1. add the following dependency

[source, xml, numbered]
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-rest</artifactId>
</dependency>
```



## part 2: expose our MovieRepository
1. add `@RepositoryRestResource` annotation to *_MovieRepository_*
    1. add attribute `collectionResourceRel = "movies"` to @RepositoryRestResource annotation
    1. add attribute `path = "movies"` to @RepositoryRestResource annotation
1. stop your previous version if it is still running
1. `./mvnw spring-boot:run`
1. visit http://localhost:8080/movies
1. follow the various links

## part 3: let their be data 
1. from part 2 we can see there's no data
    1. POST data to the MovieRepository RESTful service +
`curl -i -X POST -H "Content-Type:application/json" -d '{"title" : "Descpicalble Me", "year" : "2010", "rated" : "PG", "released" : "09 Jul 2010", "runtime" : "95 min" }'  http://localhost:8080/movies/` +
[%hardbreaks]
1. visit http://localhost:8080/movies

## part 4: open the flood gates +
1. let's preload some data +
    1. copy labs/lab5/import.sql to src/main/resources +
    1. stop your previous version if it is still running +
1. `./mvnw spring-boot:run` +
1. visit http://localhost:8080/movies +
1. follow the various links

## part 5: i failed to search for you +
1. let's fix search, in case you noticed there were errors +
    1. visit http://localhost:8080/movies/search/findByTitle?title=Minions +
    1. let's modify *_MovieRepository_* +
        1. add @Param("title") as in `List<Movie> findByTitle(@Param("title") String title);` +
        1. add @Param ("rated") as in `List<Movie> findByRated(@Param("rated") String rated);` +
        1. add @Param ("genre") as in `List<Movie> findByGenreStartsWith(@Param("genre") String genre);` + 
1. `./mvnw spring-boot:run` +
1. visit http://localhost:8080/movies/search/findByTitle?title=Minions

## part 6: pagination +
1. let's enable pagination
    1. change CrudRepository in *_MovieRepository_* to use PagingAndSortingRepository
    1. change return type from List<Movie> to Page<Movie>
    1. add extra argument to each findBy method as in `@Param("title") String title, Pageable pageable`
    1. should now look like
+
[source, java, numbered]
```
import io.pivotal.domain.Movie;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.repository.PagingAndSortingRepository;
import org.springframework.data.repository.query.Param;
import org.springframework.data.rest.core.annotation.RepositoryRestResource;

@RepositoryRestResource(collectionResourceRel = "movies", path = "movies")
public interface MovieRepository extends PagingAndSortingRepository<Movie, Long> {

    Page<Movie> findByTitle(@Param("title") String title, Pageable pageable);

    Page<Movie> findByRated(@Param("rated") String rated, Pageable pageable);

    Page<Movie> findByGenreStartsWith(@Param("genre") String genre, Pageable pageable);

}
```
+ 
1. `./mvnw spring-boot:run`
1. visit http://localhost:8080/movies/
1. scroll to bottom of page to see pagination enabled
1. now visit http://localhost:8080/movies/search/findByGenreStartsWith?genre=Animation&sort=year
1. verify sort order by year movie was released

## part 7:  cf push

1. repackage `./mvnw clean package`
1. create manifest.yml - be sure to change *_<your cf login>_* to that of your PCF login name
+
```
---
applications:
- name: watching-${random-word}
  buildpack: java_buildpack
  host: hello-$<your cf login>
  path: target/watching-0.0.1-SNAPSHOT.jar
```

1. push your app:  `cf push`
1. visit http://hello-your-random.yoursite.com/movies
1. visit http://hello-your-random.yoursite.com/movies/search/findByTitle?title=Minions
1. visit http://hello-your-random.yoursite.com/movies/search/findByGenreStartsWith?genre=Animation&sort=year