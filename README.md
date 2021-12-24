## Containerizing a Ruby on Rails Application
#
##### Prerequisites - 
Docker and Docker Compose installed on your local machine or server.
#
##### Dockerizing Your Rails Application - 
Postgres and Redis use Docker volumes to manage persistence
Postgres, Redis and Drkiq all expose a port
Drkiq and Sidekiq both have links to Postgres and Redis.
Drkiq and Sidekiq both read in environment variables from .env
Sidekiq overwrites the default CMD to run Sidekiq instead of Unicorn.
#
##### Commands - 
Prepare environment, 
```sh
$ cp env-example .env
```
In the docker-compose.yml file, create the volumes using, 
```sh
$ docker volume create --name drkiq-postgres
$ docker volume create --name drkiq-redis  
```
Run the following commands to initialize the database, 
```sh
$ docker­-compose run drkiq rake db:reset
$ docker­-compose run drkiq rake db:migrate
```
Start the stack by the following,
```sh
$ docker-compose up -d
```
Test the application using, 
```sh
curl --request GET --url http://localhost:8020
```
##### Logging, Monitoring and Alerting - 
Docker expects your application or process to log to STDOUT. ELK stack would be used for Logging and Alerting, in which we will send the log entries to Logstash to be processed, which will in turn store them in an ElasticSearch, and use Kibana for visualisations.
Set up the ELK stack using the following, 
```sh
cd elk
docker-compose up -d
```
Alerting: Kibana plugins sentinl or other plugins to raise intelligent alerts whenever certain condition matches the data eg:
- Average of CPU usage goes beyond 80 % in last 15 min (Metrics)
- If we see no of 500 error in logs beyond a given threshold (Logs)
- If the number of request for an application increases beyond a threshold (APM)

Reporting: Kibana provides plugins that can generate and deliver reports to your mail slack etc.

- Implementing intelligent health check
- Define business objectives eg: Analyze logs to check which city pages are being searched more and act accordingly.
- Using machine learning jobs to get outliers in network metrics to identify any network attack
- See patterns in a number of requests for your app to identify any scraping or DDOS attacks.
- See the usage metrics of your servers and upgrade /downgrade for cost optimization

> In addition to the elk-filebeat stack, APM can be also be included for enhanced monitoring. 