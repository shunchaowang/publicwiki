# Groot
> *GR* oovy with Spring B *OOT* 
# Introduction
> An awesome project based on Spring Boot, Spring Security and Thymeleaf. Groovy is used as the language on this
project, it sounds so exciting to use Groovy on Spring Framework project, can abandon Grails now.
---
# Features
- Spring Boot 1.5.8
> Pure java config and CoC without any xml configuration, application switches to use yml file as the configuration
instead of properties file, code looks more organized and straightforward.
- Spring Security 4.x
> Pure java config, no xml either. Implements custom UserDetails and MethodEvaluator to provide both role based and
permission based access control. Navbar is dynamic based on the permissions login user has. The menubar composes of 2
levels, menu item is mapped to permission and groupd by menu categories.
- Thymeleaf 2.x
> Pure html, the seperation of server side processing and web design. Along with extra package for Spring Security to
use native Spring Security Tags in the page.
- Bootstrap 3.3.7
> Responsive and beautiful
- JQuery 3.x
> UI, Dialog and JQuery Datatables with custom create/edit/delete button extended, perfect single page for one business
unit.
- Internationalization and Localization
> Both web pages and validation messages are i18n, no more static strings in html!
- Groovy
> Best scripting language runs on JVM, save more typings than Java.
---
# Releases
- 0.0.1-SNAPSHOT
> kick off the idea
---
# Tasks
- [X] Modulize project
    - [X] Separate backend service and front web app
        > Project is divided into 2 modules: core and app. Core contains domain, data repository and service; app
        contains controller and UI. App consumes core to manipulate business logic.
    - [X] Create build structure for multi-module project
        > Gradle is the build tool, the artifact and classes are built to build folder, but intellij idea uses out by
        default. Config idea plugin to use build as the output dir to make runtime resource reload and debug work as
        expected.
    - [X] Build core service
        > Core is a library, no tomcat container is needed, but mysql database and spring data jpa are dependencies.
    - [X] Build web app
        > App depends on core and tomcat is the the container, packaged as a war. tomcat should be a providedRuntime
        dependency on the production, but intellij idea automatically shuts down the boot application when running in
        the IDE if tomcat is providedRuntime. To solve this marking tomcat as a runtme dependency is the trick.
- [ ] Config spring webmvc 
- [ ] Config spring security 
- [ ] Config Thymeleaf
- [ ] Design model
- [ ] Define spring data repository interface
- [ ] Implement service layer
- [ ] Design UI
- [ ] Customize datatables
- [ ] Validate java bean
- [ ] Localize page language
---
# Notes
