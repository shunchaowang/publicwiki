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
## Huawei Clound

