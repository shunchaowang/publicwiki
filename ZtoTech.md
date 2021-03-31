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
2. Install MySQL
3. Install Mongodb
4. Install Redis
5. Install Nginx
6. Install Kafka
* Configuration
1. Set up MySQL
2. Set up Mongodb
3. Set up Redis
4. Set up Nginx
5. Set up Kafka
* Deployment
1. Create environment properties file `src/main/resources/application-prod.properties`
2. Update `src/main/resources/PaasConfigurationFile.properties` based on the previous configuration
3. Build project
4. Deploy the artifacts
## Huawei Clound

