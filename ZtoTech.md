## Development
## Deployment
Ad platform is depending lots of open source projects, including kafka (which requires zookeeper to run), Mongodb,
MySQL, Redis, Nginx and JDK. 
1. Kafka is the event driven streaming service used by ad service to achieve service subscription. 
2. Mongodb is used by kafka to store the stream content and file.
3. MySQL is to store all transactional data including enterprise info, user info and product info.
4. Redis is the in memory cache for session.
5. Nginx is the reverse proxy for all ad applications, AKA the front end of all apps.
6. JDK is needed by ad-service to run.
* Install prerequisites
1. Kafka
2. Zookeeper
3. Mongodb
4. MySQL
5. Redis
6. Nginx
7. JDK
* Configure prerequisites
* Install apps
* Configure apps
## Enviroment
### Production
Ubuntu Server 20.04 will be used as the server. All prerequisites have to be installed before the deployment of web
apps.
* Prerequisites
1. Install OpenJDK 11
JDK 11 can be used by ad-service, run `sudo apt install openjdk-11-jdk` to install openJDK. Verify the installation by
checking `java -version` to make sure Java 11 has been installed successfully.
2. Install MySQL
Run `sudo apt install mysql-server` to install mysql 8 on the server, after the installation, refer to the configuration
section to set up the instance and initialize ztotech schema.
Once the installation is done, verify the service is up and running `sudo systemctl status mysql`.
4. Install Mongodb
5. Install Redis
6. Install Nginx
7. Install Kafka
* Configuration
1. Set up MySQL
* Set up the security to disable remote root logins and sample users/schemas.
run `sudo mysql_secure_installation` to answer a series of prompts:
- VALIDATE PASSWORD COMPONENT: No
- Set root password: <password>
- Remove anonymous users: Y
- Disallow root login remotely: Y
- Remove test database and access to it: Y
- Reload privilege tables now: Y
After all these steps root cannot log in yet.
3. Set up Mongodb
4. Set up Redis
5. Set up Nginx
6. Set up Kafka
* Deployment
1. Create environment properties file `src/main/resources/application-prod.properties`
2. Update `src/main/resources/PaasConfigurationFile.properties` based on the previous configuration
3. Build project
4. Deploy the artifacts
### Development
* ad-service
1. code repo: `https://github.com/shunchaowang/ztotech-ad-service.git`
2. build
under project root, run `./mvnw clean package`, make it's successful
3. run
create an adservice database in mysql, source the sql file in the code to initialize tables and data, update
`ad-service/src/main/resources/application-local.properties` to the username and password used by local mysql, then
update `spring.profiles.active=local` in `ad-service/src/main/resources/application.properties`, then build again. If
the build passes, under root path run `./mvnw spring-boot:run -pl ad-service`
* admin-web
1. code repo: `https://github.com/shunchaowang/ztotech_ad_admin_web`
2. build
* Install node v8.17.0
* install `bower globally npm install -g bower`
* Install gulp-cli if needed
* Install build tools `npm install`
* Install dependencies `bower install`
* Build project `gulp build`
3. run `gulp serve-dist`
* enterprise-manager
1. code repo: `https://github.com/shunchaowang/ztotech_ad_enterprise-manager`
2. build 
* Install node v10.23.2
* build `npm install`
3. run
* run `npm run serve`
## Huawei Clound

