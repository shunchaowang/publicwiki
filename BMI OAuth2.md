# Environments
## bmi-oauth2
* build: run `./gradlew build` to build the whole project
* config mysql data source: in application-[env].properties of auth and service, put
  bmi.oauth2.persistence.[driver|url|username|password] properties to set up the mysql connection.
* load oauth2 schema into the database
* load test data into oauth2 table
### bmi-oauth2:auth
run `./gradlew :auth:bootRun` auth should be accessible on the host:port based on application-[env].properties of auth
project.
### bmi-oauth2:service
run `./gradlew :auth:bootRun` auth should be accessible on the host:port based on application-[env].properties of auth
project.
## bmi-oauth2-app
run `npm start` to start in dev mode, the default test user is admin/admin
# Tasks
- [X] Upgrade Material UI from V3 to V4
    - [X] Update NavBar
    - [X] Update ToolBar
    - [X] Updade Drawer
- [X] Add Username and Password requirements to registration page
    - [X] Use JSS to format the UI
    - [X] Add a list for requirement
- [X] Update database/service properties for prod
    - [X] Update db for auth project
    - [X] Update db for admin service
    - [X] Update auth service for admin service
    - [X] Update auth service for admin app
    - [X] Update admin service for admin app
- [X] Document auth request and response
    - [X] authorization_code authentication
        * 1st request - get
        1. url:`http(s)://<auth_host>/oauth/authorize?response_type=code&client_id=<client_id>&redirect_uri=<redirect_uri>&scope=all`
        2. header: `Content-Type: application/x-www-form-urlencoded`
        3. body: empty
        * 1st response
        1. url: `<redirect_uri>?code=<authorization_code>`
        2. body: empty
        * 2nd request - post
        1. url: `http(s)://<auth_host>/oauth/token`
        2. header: `Authorization: Basic <client_id:credential base64 encoded>`
        3. header: `Content-Type: application/x-www-form-urlencoded`
        4. body: 
            ```
            grant_type=authorization_code&client_id=<client_id>&code=<authorization_code from 1st
            request>&redirect_uri=<redirect_uri>
            ```
        * 2nd response
        1. body
            ```
            {
                "access_token": "<jwt token>",
                "token_type": "bearer",
                "refresh_token": "<jwt token>",
                "expires_in": "<seconds>"
            }
            ```
    - [X] password authentication
        * request - post
        1. url: `http(s)://<auth_host>/oauth/token`
        2. header: `Authorization: Basic <client_id:credential base64 encoded>`
        3. header: `Content-Type: application/x-www-form-urlencoded `
        4. body: 
            ```
            grant_type=password&username=<username>&password=<password>
            ```
        * response
        1. body
            ```
            {
                "access_token": "<jwt token>",
                "token_type": "bearer",
                "refresh_token": "<jwt token>",
                "expires_in": "<seconds>"
            }
            ```
    - [X] Retrieve uer's info
        * request
        1. url: `http(s)://<auth_host>/me`
        2. header: `Authorization: Bear <access_token obtained above>`
        3. header: `Content-Type: application/json`
        4. body: empty
        * response
        1. body
            ```
            {
                "name": "<username>"
            }
            ```
    - [X] Logout
    1. redirect user to `http(s)://<auth_host>/logout` to logout from OAuth session.
- [ ] Document admin service request and response
# Network Architecture
## database connection info
* Prod
Hostname: bmi-imiddb-vp01.bmi.osumc.edu
DB: OAuth_prd
Username: OAuth_prd_usr
Password: 5UqK80xZVw9BJ8tAt0a8#
* Test
Hostname: bmi-imiddb-vd01.bmi.osumc.edu
DB: OAuth_tst
Username: OAuth_tst_usr
Password: VUjJKrXlOoehGV26a21e! 
* Dev
Hostname: bmi-imiddb-vd01.bmi.osumc.edu
DB: OAuth_dev
Username: OAuth_dev_usr
Password: emCGYToaux!5X0JqfkY4w
## NetScaler Configuration
```
https://auth.bmi.osumc.edu -> http://bmi-oauth-vp01.bmi.osumc.edu:8080
https://auth-test.bmi.osumc.edu -> http://bmi-oauth-vt01.bmi.osumc.edu:8080
https://auth-dev.bmi.osumc.edu -> http://bmi-oauth-vd01.bmi.osumc.edu:8080

https://auth-service.bmi.osumc.edu -> http://bmi-oauthm-vp01.bmi.osumc.edu:8080/
https://auth-service-test.bmi.osumc.edu -> http://bmi-oauthm-vt01.bmi.osumc.edu:8080/
https://auth-service-dev.bmi.osumc.edu -> http://bmi-oauthm-vd01.bmi.osumc.edu:8080/

https://auth-portal.bmi.osumc.edu -> http://bmi-oauthm-vp01.bmi.osumc.edu/
https://auth-portal-test.bmi.osumc.edu -> http://bmi-oauthm-vt01.bmi.osumc.edu/
https://auth-portal-dev.bmi.osumc.edu -> http://bmi-oauthm-vd01.bmi.osumc.edu/
```
* Docker Container

