# Introduction
> MyExperience aims to provide a ticketing system for the hospitals and medical center community to submit concerns and
express kudos based on their experience provided by medical center. It is a multi module project including a backend rest service and frontend hybrid web app. 
## MyEx Service 
> Service is responsible for the crud data operations, it also serves as a middleware to bridge jira ticketing system
with MyExperince. Service is developed using grails 3.1.10, using spring security rest as the
authentication/authorization provider for the stateless token based restful apis.
## MyEx App
> App is the UI web application, it's developed using grails 3.1.10 and JQuery Mobile 1.4.5 to provide a cross platform
hybrid mobile application. App consumes the apis provided by MyEx Service to submit concerns/kudos to the jira server.
***
# Features
***
# Releases
---
# Tasks
- [X] Convert service to be a rest
    - [X] use spring security with jwt as the stateless authentication
    - [X] update apis to return standard rest response
    - [X] update apis to response a 401/403 if jwt token not found
    - [X] update controller secured annotation
    - [X] test rest api using postman
- [X] Convert app to consume service rest api
    - [X] update login to request stateless token
    - [X] update logout to deactivate token
    - [X] update controllers to refresh token during the service call
- [X] trace if user has valid session 
    - [X] listMyTickets requires session, otherwise redirect to login
    - [X] createIssue has to check session if username is in the request, a user param needs to be added if user logs in
    - [X] createKudos has to check as well
    - [X] updateIssue has to check, user param is not need
    - [X] updateKudos has to check, user param is not need
    - [X] updatePassword has to check, user param is not need
    - [X] logout has to check, just log out, no check needed
- [X] Set up dev and test environment for MyExperience Service
    - [X] Set up dev profile in application.yml
        ```
        url: jdbc:mysql://bmi-rdmzdb-vd01.bmi.osumc.edu/MY_EXPERIENCE_Dev
        username: experienceUser
        password: 2I0ujsCDvvFM7D9XSoDy2018!
        ```
    - [X] Set up test profile in application.yml
        ```
        url: jdbc:mysql://bmi-rdmzdb-vd01.bmi.osumc.edu/MY_EXPERIENCE_Test
        username: experienceUser
        password: 2I0ujsCDvvFM7D9XSoDy2018!
        ```
- [X] Switch existing MyEx App and Service to be dev
- [X] Set up new prod
- [X] Add delivered and read to status table on both dev and prod
    - [X] Add delivered and read on dev
    - [X] Add delivered and read on prod
- [X] Add bravo to type table on both dev and prod
    - [X] Add bravo on dev
    - [X] Add bravo on prod
---
# Notes
- Spring Security Rest is a stateless authentication/authorization based on jwt (json web token), unauthorized request
  will result in a 403 `Forbidden` response, unlike Spring Security Web responses a 302 `Redirection` to the login form.
- Spring Security intercept url pattern uses ant paths
    - inherit from regular expression, `*` matchs zero or more characters, `?` matches one character
    - if a pattern ends with `/` or `\`, then `**` is appended, `/test/` is interpreted the same with `/test/**`
    - `**` means recursive, and `*` means 1 dpeth

