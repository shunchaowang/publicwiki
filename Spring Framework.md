## Initialize a **spring boot** project
1. using [spring initilizr](https://start.spring.io/) to generate the quickstart project
- pick a gradle java project based on latest version 
- select the dependencies below
> `Spring Boot DevTools`, `Lombok`, `Spring Web`, `Spring Data JDBC`, `H2 Database`, `Spring Boot Actuator`, 
    `Spring REST Docs`
* generate and download the project
2. set up git version control
> `git init`, `.gitignore`, `git remote add origin ...` 
3. adjust the properties
- create  a yaml file `application.yml` to act as the default properties file
> I like to keep both yaml and properties, but use `application.yml` as the default, from the loading order standpoint,
default properties files `application.properties/yml` is loaded first, then the environment specific properties will be
loaded, not that env file will take precedence over the default files. There must be only one `application.yml` file
since yaml supports multi documents in a single file with three dashes as the document separator. The approach I am
preferring
    - `application.yml` as the default properties and specify the environment here, vault variables should be referenced
      here
    - `application-<env>.properties` as the env specific properties
        - `application-dev.preperties` for local dev setup (it will not be added to version control)
        - `application-ci.properties` for review, and external apis for review
        - `application-staging.properties` for staging, and external apis for staging
        - `application-prod.properties` for production, and external apis for production
4. adjust build file to use kotlin
- rename `build.gradle` to be `build.gradle.kts` and adjust to kotlin syntax
