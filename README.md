# Payara Showcase

[![GitHub last commit](https://img.shields.io/github/last-commit/stephan-mueller/payara-showcase)](https://github.com/stephan-mueller/payara-showcase/commits) 
[![GitHub](https://img.shields.io/github/license/stephan-mueller/payara-showcase)](https://github.com/stephan-mueller/payara-showcase/blob/master/LICENSE)
[![CircleCI](https://circleci.com/gh/stephan-mueller/payara-showcase.svg?style=shield)](https://app.circleci.com/pipelines/github/stephan-mueller/payara-showcase)
[![Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=stephan-mueller_payara-showcase&metric=alert_status)](https://sonarcloud.io/dashboard?id=stephan-mueller_payara-showcase)
[![Coverage](https://sonarcloud.io/api/project_badges/measure?project=stephan-mueller_payara-showcase&metric=coverage)](https://sonarcloud.io/dashboard?id=stephan-mueller_payara-showcase)

This is a project based on the microservice framework [Payara Micro](https://www.payara.fish/products/payara-micro/). 
It contains a hello world application, which demonstrates features of Payara Micro and Eclipse Microprofile.

Software requirements to run the samples are `maven`, `openjdk-1.8` (or any other 1.8 JDK) and `docker`.
When running the Maven lifecycle it will create the war package. The war will be copied into a
Docker image using Spotify's `dockerfile-maven-plugin` during the package phase.

**Notable Features:**
* Dockerfiles for runnable JAR & Server
* Integration of MP Health, MP Metrics and MP OpenAPI
* Testcontainer-Tests with Rest-Assured, Cucumber and Postman/newman
* [CircleCI](https://circleci.com) Integration
* [Sonarcloud](https://sonarcloud.io) Integration


## How to run

Before running the application it needs to be compiled and packaged using `Maven`. It creates the runnable jar and Docker image and can be 
run via `docker`:

```shell script
$ mvn clean package
$ docker run --rm -p 8080:8080 payara-showcase
```

For the Payara Server a multi-stage Docker image is provided. It can be created and run via `docker`:    
```shell script
$ docker build -f Dockerfile.server -t payara-server-showcase .
$ docker run --rm -p 8080:8080 payara-server-showcase
```

Wait for a message log similar to this:

> [2020-07-14T17:39:34.789+0200] [] [INFO] [] [PayaraMicro] [tid: _ThreadID=1 _ThreadName=main] [timeMillis: 1594741174789] [levelValue: 800] Payara Micro  5 #badassmicrofish (build 547) ready in 7.127 (ms)

If everything worked you can access the OpenAPI UI via [http://localhost:8080/swagger-ui](http://localhost:8080/swagger-ui).

### Resolving issues

Sometimes it may happen that the containers did not stop as expected when trying to stop the pipeline early. This may
result in running containers although they should have been stopped and removed. To detect them you need to check
Docker:

```shell script
$ docker ps -a | grep payara-showcase
```

If there are containers remaining although the application has been stopped you can remove them:

```shell script
$ docker rm <ids of the containers>
```


## Features

### Application 

The application is a very simple "Hello World" greeting service. It supports GET requests for generating a greeting message, and a PUT 
request for changing the greeting itself. The response is encoded using JSON.

Try the application
```shell script
curl -X GET http://localhost:8080/api/greet
{"message":"Hello World!"}

curl -X GET http://localhost:8080/api/greet/Stephan
{"message":"Hello Stephan!"}

curl -X PUT -H "Content-Type: application/json" -d '{"greeting" : "Hola"}' http://localhost:8080/api/greet/greeting

curl -X GET http://localhost:8080/api/greet/greeting
{"greeting":"Hola"}

curl -X GET http://localhost:8080/api/greet/Max
{"message":"Hola Max!"}
```

### Health, Metrics and OpenAPI

The application server provides built-in support for health, metrics and openapi endpoints.

Health liveness and readiness
```shell script
curl -s -X GET http://localhost:8080/health

curl -s -X GET http://localhost:8080/health/live

curl -s -X GET http://localhost:8080/health/ready
```

Metrics in Prometheus / JSON Format
```shell script
curl -s -X GET http://localhost:8080/metrics

curl -H 'Accept: application/json' -X GET http://localhost:8080/metrics
```

OpenAPI in YAML / JSON Format
```shell script
curl -s -X GET http://localhost:8080/openapi

curl -H 'Accept: application/json' -X GET http://localhost:8080/openapi
```