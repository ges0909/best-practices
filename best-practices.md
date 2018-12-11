---
title:  Best Practices with JHipster, Spring Boot and Gradle
# separator: <!--s-->
# verticalSeparator: <!--v-->
revealOptions:
  slideNumber: true
  transition: slide
  # transitionSpeed: slow
  embedded: true
  width: 95%
  height: 95%
  minScale: 1
  maxScale: 1
  mouseWheel: true
---

# Best Practices

## infinIT Service Platform

### 09/12/2018

---

# JHipster

---

<!--v-->
<!-- .slide: style="font-size:smaller;text-align:left" -->

## What is JHipster

[JHipster](https://www.jhipster.tech/) is a scaffolding tool for project generation with [Angular](https://angular.io/) or [React](https://reactjs.org/) (front-end) and [Spring Boot](https://spring.io/projects/spring-boot) (back-end).

After installation you run it from command line with `jhipster`.

_JHipster_ supports two application types: _Monolith_ and _Microservice_. It offers many options to configure an application with authentication, database, caching, etc. Build tools for back-end are _Maven_ and [Gradle](https://gradle.org/).

The _infinIT Service Platform_ uses the _Microservice_ application type and the _Gradle_ build tool. Front-end generation is skipped recently.

A microservice runs "out of the box" with `gradlew bootRun`.

---

<!--v-->
<!-- .slide: style="font-size:smaller;text-align:left" -->

## Sub Generators

| `jhipster`          | Description                                     |
|---------------------|-------------------------------------------------|
| `upgrade`           | upgrade app. with new _JHipster_ version        |
| `entity`            | domain classes with `@Entity`                   |
| `spring-controller` | REST controller classses with `@RestController` |
| `spring-service`    | business logic classes with `@Service`          |
| `import-jdl *.jh`   | generate app. or entities from JDL              |

Example: `jhipster entity`

Others: `docker-compose`, `heroku`, `cloudfoundry`, `aws`, `kubernetes`

---

<!--v-->
<!-- .slide: style="font-size:smaller;text-align:left" -->

## Best Practice: Sub Generators

If you can generate something, generate it!

_JHipster_ generates _Java_ source code for you. If you change sources, **avoid the change of generated sources**. You changes will be lost, while re-generated the next time.

The same applies for IDE (IntelliJ IDEA) refactorings. **Don't refactor generated sources.**

But sometimes you can't avoid the change of generated stuff like for `ApplicationProperties.java`, `build.gradle`, `application*.yml`, etc. Merge your lost changes with the help of _Git_!

---

<!--v-->
<!-- .slide: style="font-size:smaller;text-align:left" -->

## Application Generation

Beside running "`jhipster`" with Q&A, you can use _JHipster Domain Language_ (JDL) to define an application in a "`*.jh`" file and run "`jhipster import-jdl *.jh`".

```js
application {
  config {
    applicationType microservice,
    baseName predictor,
    packageName de.infinitservices.forge.serviceplatform.predictor,
    databaseType sql,
    devDatabaseType h2Disk,
    prodDatabaseType mariadb,
    enableTranslation false,
    serviceDiscoveryType no,
    serverPort 9961,
    buildTool gradle,
    skipClient true
  }
}
```

JDL-Syntax: see [JHipster Domain Language (JDL)](https://www.jhipster.tech/jdl/)

---

<!--v-->
<!-- .slide: style="font-size:smaller;;text-align:left" -->

```sh
λ cloc --exclude-dir=node_modules .
...
-------------------------------------------------------------------------------
Language                     files          blank        comment           code
-------------------------------------------------------------------------------
JSON                             3              0              0           4471
Java                            63            587            363           2445
YAML                             9             36            183            381
Gradle                           7             61             22            342
HTML                             2             38              0            228
XML                              4             22             27            149
Bourne Shell                     3             23             22            135
DOS Batch                        1             23              2             59
Markdown                         1             42              0             51
Dockerfile                       1              7              1             12
-------------------------------------------------------------------------------
SUM:                            94            839            620           8273
-------------------------------------------------------------------------------
```

[Wikipedia](https://de.wikipedia.org/wiki/Lines_of_Code): "_Üblicherweise rechnet man mit einer Produktivität – inklusive aller Projekttätigkeiten – von 10 bis 50 Codezeilen je Mitarbeiter und Tag._"

---

<!--v-->
<!-- .slide: style="font-size:smaller;text-align:left" -->

## Best Practice: Application Generation

Select a unique _Java_ package name for your microservice.

_Java_ packages define a namespace shielding your symbol names from symbols named the same in other packages.

A name "`de.infinitservices.forge.serviceplatform`" is not sufficient. All microservices would belong to the same package resulting sooner or later in name clashes.

Add "`*.jh`" file to version control!

---

<!--v-->
<!-- .slide: style="font-size:smaller;text-align:left" -->

## Entity Generation

Beside running "`jhipster entity`" with Q&A you can use _JHipster Domain Language_ (JDL) to define entities in a "`*.jh`" file and run "`jhipster import-jdl *.jh`".

```js
entity AnomalySequence {
  name String required pattern(/[a-z]+[a-z0-0-]*/) unique
}

entity Anomaly {
  epochTimestamp Instant required
  directionUp Boolean required
  positive Boolean required
  sigma Integer required min(1) max(4)
}

relationship OneToMany {
  AnomalySequence{anomalies} to Anomaly{name}
}

skipClient * // skip generation of front-end code
```

---

<!--v-->
<!-- .slide: style="font-size:smaller" -->

```sh
λ cloc --exclude-dir=node_modules .
...
github.com/AlDanial/cloc v 1.78  T=0.50 s (213.2 files/s, 22058.4 lines/s)
-------------------------------------------------------------------------------
Language                     files          blank        comment           code
-------------------------------------------------------------------------------
JSON                             5              0              0           4561
Java                            71            785            523           3227
YAML                             9             36            183            381
Gradle                           7             61             22            342
HTML                             2             38              0            228
XML                              7             40             40            224
Bourne Shell                     3             23             22            135
DOS Batch                        1             23              2             59
Markdown                         1             42              0             51
Dockerfile                       1              7              1             12
-------------------------------------------------------------------------------
SUM:                           107           1055            793           9220
-------------------------------------------------------------------------------
```

---

<!--v-->
<!-- .slide: style="font-size:smaller;text-align:left" -->

## Best Practice: Entity Generation

Choose most approriate JDL types for your problem domain, e.g.  `Instant` instead of `Long` if you want to store time stamps.

Use validation constraints like `required`, `unique`, `pattern`, `min`, `max`, ... as far as possible.

If you want to access a database owned by another sub-project, add a project dependency to your `build.gradle` file to get access to entity and repository classes.

```gradle
dependencies {
 ...
 compile project(":otherSubProject")
}
```

---

<!--v-->
<!-- .slide: style="font-size:smaller" -->

## Java package organization

_JHipster_ organizes _Java_ packages "_by layer_" (vs. "_by feature_").

| Package               | Contains                     |
| :-------------------- | ---------------------------- |
| `*/java/*/*App.java`  | `main()`                     |
| `*/java/*/config`     | `ApplicationProperties.java` |
| `*/java/*/domain`     | `@Entity` classes            |
| `*/java/*/repository` | `@Repository` classes        |
| `*/java/*/service`    | `@Service` classes           |
| `*/java/*/web.rest`   | `@RestController` classes    |
| `*/test/*/web.rest`   | `@Test` of `@RestController` |

---

# Spring Boot

---

<!--v-->
<!-- .slide: style="font-size:smaller;text-align:left" -->

## What is Spring

The [Spring Framework](https://spring.io/) is for writing _Java_ applications. We us it to write _Java_ server applications.

It differs from others by implementing the principles _Inversion of Control_ (IoC) and _Dependency Injection_ (DI). For this it introduces the concepts of _Bean_ and _Component_.

Classes annotated with _@Component_, _@Service_, _@Controller_ or _@Repository_ are components. Mainly components are **singletons**, created under the hood by the Spring run-time.

With _@Autowired_ (or _@Inject_) you inject other components into your component. Spring resolves the link at run-time.

Don't use _Java_'s `new` operator to create or autowire components!

A Spring application consists of loosely coupled components.

---

<!--v-->
<!-- .slide: style="font-size:smaller;text-align:left" -->

## What is Spring Boot

[Spring Boot](https://spring.io/projects/spring-boot) is based on the _Spring Framework_. It is your friend because it eases the usage of Spring radically.

Spring Boot ensures that you get library versions that are guaranteed to work together without problems.

Further it auto-configures you application depending on what is on your _classpath_. It prefers _Java_ configuration against the stupid XML configuration of the Spring Framework.

And it brings an embedded server freeing you from the burden  to deploy your application to any server. Just run "`java -jar <application>.jar`" and your server application starts magi

---

<!--v-->
<!-- .slide: style="font-size:smaller;text-align:left" -->

## Best Practice: Dependency Injection

There is _Field_-, _Setter_- and _Constructor Injection_. But the recommended way is _Constructor Injection_.

```java
@Service
public class FilesystemWatchService {
  ...
}
```

```java
@RestController
@RequestMapping("/api/start")
public class StartController {
    // @Autowired // Field injection is BAD!
    private final FilesystemWatchService watchService;

    @Autowired // Constructor injection is GOOD!
    StartController(FilesystemWatchService watchService) {
        this.watchService = watchService;
    }
    ...
}
```

---

<!--v-->
<!-- .slide: style="font-size:smaller;text-align:left" -->

## Data Transfer Objects

_JHipster_ uses its entities directly in its REST endpoints. That's bad!

```java
@Entity // will be stored in database
class User {
  public String visibleForAll;
  public String forYourEyesOnly;
}
```

```java
@GetMapping(value = "/users/{id}", produces = MediaType.APPLICATION_JSON)
public User getUser(@PathVariable Long id) {
  return userRepository.findById(id).get(); // 'findBy*' return Optional
}
```

---

<!--v-->
<!-- .slide: style="font-size:smaller;text-align:left" -->

## Best Practice: Data Transfer Objects

Instead of entities use _Data Transfer Objects_ (Dto's) for method and return parameters at REST endpoints.

Dto's are simple Plain Old Java Objects (POJO's) without business logic. They will be constructed from or converted to JSON.

```java
class UserDto {
  public String visibleForAll;
}
```

```java
@GetMapping(value = "/users/{id}", produces = MediaType.APPLICATION_JSON)
public UserDto getUser(@PathVariable Long id) {
  User user = userRepository.findById(id).get();
  UserDto = userDto = new UserDto();
  userDto.visibleForAll = user.visibleForAll;
  return userDto;
}
```

---

<!--v-->
<!-- .slide: style="font-size:smaller;text-align:left" -->

## Why should I use Dto's

Dto's separate the aspects of persistence (entity) from the aspects of transfer (Dto). They follow the principle of _Separation of Concerns_. As developer you get full control over what will be visible outside your REST service. That's good!

Don't add methods with business logic to Dto's.

## Data Transfer Objects in JHipster

_JHipster_ supports the generation of Dto's. But the feature is new and still in **BETA**. In will be presented later in this series, if we have some expierence how to use it.

---

<!--v-->
<!-- .slide: style="font-size:smaller;text-align:left" -->

## Bean Validation for Dto's

Bean Validation means "_Constrain once, validate everywhere_".

```java
class AccountDto {
    @NotNull @Size(min=8, max=16) // constrain once
    String name;
    @Email                        // constrain once
    String email;
    @NotNull @Min(0) @Max(99)     // constrain once
    Integer age;
}
```

```java
@PostMapping(value = "/accounts", consumes = MediaType.APPLICATION_JSON)
public void createAccount(@Valid /*validate everywhere*/ AccountDto account) {
  ...
}
```

Never again checks with stupid _if_'s. The _**@Valid**_ annotation triggers validation. A constrain violation results in throwing an exception.

Syntax: see [Bean Validation](https://beanvalidation.org/)

---

<!--v-->
<!-- .slide: style="font-size:smaller;text-align:left" -->

## REST API Tests

All routes (`/api/...`) to _JHipster_ generated microservices are secured by a _JSON Web Token_ ([JWT](https://www.jhipster.tech/security/)). You must be authenticated to access the REST API. Otherwise you will receive `401 Unauthorized` .

The `*Int` tests generated by _JHipster_ using a technique called _Slicing_. Only those server parts required to run the REST call are started. Slicing bypasses security.

**Tip**: Use `*Int` tests generated by _JHipster_ as blueprint for your own tests to minimize the risk of non-working tests.

Write negative tests also. Check values on boundaries and immeditately before and after boundaries.

---

<!--v-->
<!-- .slide: style="font-size:smaller;text-align:left" -->

## Drop Security for `dev` Profile

Modify file `config/SecurityConfiguration.java` .

```java
@Profile("!dev") // add this annotation
public class SecurityConfiguration extends WebSecurityConfigurerAdapter {
  @Override
  public void configure(HttpSecurity http) throws Exception {
    ...
    .authorizeRequests()
    .antMatchers("/api/**").authenticated()
```

Add new file `config/SecurityConfigurationDevProfile.java` .

```java
@Configuration
@EnableWebSecurity
@Profile("dev")
public class SecurityConfigurationDevProfile extends WebSecurityConfigurerAdapter {
  @Override
  public void configure(WebSecurity web) {
    web.ignoring().antMatchers("/api/**");
  }
}
```

---

# Miscellanea

---

<!--v-->
<!-- .slide: style="font-size:smaller;text-align:left" -->

## Java and Gitflow

_Java_: Use `final` where ever you can. Though adds verbosity to the code, nevertheless it is good style.

_Gitflow_: The first action after cloning a repository should be `git flow init`. Use _Gitflow_ with _Start New Feature_ and _Finish Feature_!

---

<!--v-->
<!-- .slide: style="font-size:smaller;text-align:left" -->

## Open Topics

Externalized configurations: Files `application*.yaml` and `ApplicationProperties.java` and "_relaxed binding_"?

Custom Queries: `@Query`, `Find-By-Example`, `find` methods.

Test: How to mock components with _Mockito_? Fluent assertions with _AssertJ_.

`@FeignClient` for REST calls to other microservice.

Upgrade _JHipster_ and your application.

---

<!--v-->
<!-- .slide: style="font-size:smaller;text-align:left" -->

## Future Topics

Reactive _Java_ and _Spring Boot_.

---

# Questions

---

# Thank you for your time

---

# Fragments

<div class="fragment" data-fragment-index="3">Appears last</div>
<div class="fragment" data-fragment-index="1">Appears first</div>
<div class="fragment" data-fragment-index="2">Appears second</div>

---

# SVG

<!-- ![circles](svg/circles.svg) -->

<svg height="300" width="300">
  <circle cx="150" cy="150" r="150" stroke="black" stroke-width="3" fill="red" />
  <circle cx="150" cy="150" r="100" stroke="black" stroke-width="3" fill="blue" />
  <circle cx="150" cy="150" r="50" stroke="black" stroke-width="3" fill="yellow" />
</svg>
