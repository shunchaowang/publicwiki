# Installation 
## Set up airflow development environment
- download `docker-compose.yaml` file from airflow
run the command in terminal `curl -LfO 'https://airflow.apache.org/docs/apache-airflow/3.0.2/docker-compose.yaml'`
- create a `.env` initial file
run `touch .env` to create the env file
- set up the user id for airflow instance
run `echo -e "AIRFLOW_UID=$(id -u)" > .env` to set the current user as the uid
- create the essential folders for airflow runtime
run command `mkdir -p ./dags ./logs ./plugins ./config`
- initialize the database
run `docker compose up airflow-init`
- running airflow
run command `docker compose up`
access airflow ui @ [local airflow instance](http://localhost:8080/)
the admin username and password is airflow/airflow 


