## Structure
* ad-service
Entry web project with restful service
* plat-paas
Module to handle all backend communication to kafka, redis and mongo
## Environment
1. local
All dependencies are deployed on a Ubuntu Server 20.04 with ip `192.168.137.8`
3. dev
4. live
## Dependencies
* MySql
db: adservice
username: admin
password: admin
* Redis
* MongoDb
db: admin
username: admin
password: admin
* MongoDb in Virtual Box
username: mongoAdmin
password: changeMe
login: `mongo -u mongoAdmin -p --authenticationDatabase admin`
fileserver:
mongodb: fileDB
username: fileservice
password: 123456
filedomain:
* Kafka
under `~/Downaload/kafka-***`
start zookeeper: `bin/zookeeper-server-start.sh config/zookeeper.properties`
start kafka: `bin/kafka-server-start.sh config/server.properties`
## Services
## Code
run ad-service with maven from `ztotech-ad-service`: `mvn spring-boot:run -pl ad-service`
