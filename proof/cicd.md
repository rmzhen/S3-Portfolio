# CI/CD

## Table of content
- [Intro](#intro)
- [What is CI/CD](#what-is-cicd?)
	- [Continuous integration](#continuous-integration)
	- [Continuous delivery](#continuous-delivery)
	- [Continuous deployment](#continuous-deployment)
- [IP impementation](#ip-implementation)
	- [CI](#ci)

## Intro
In this semester, we're tasked to design and implement a (semi)automated releace process that matches the needs of the project context. To tackle this, I'll have to implement a coninuous integration and delivery/deployment solution.

## What is CI/CD?
### Continuous integration
> Developers practicing continuous integration merge their changes back to the main branch as often as possible. The developer's changes are validated by creating a build and running automated tests against the build. By doing so, you avoid integration challenges that can happen when waiting for release day to merge changes into the release branch.
>
> Continuous integration puts a great emphasis on testing automation to check that the application is not broken whenever new commits are integrated into the main branch.

*Source: https://www.atlassian.com/continuous-delivery/principles/continuous-integration-vs-delivery-vs-deployment*

### Continuous delivery
> Continuous delivery is an extension of continuous integration since it automatically deploys all code changes to a testing and/or production environment after the build stage. 
>
> This means that on top of automated testing, you have an automated release process and you can deploy your application any time by clicking a button.
>
> In theory, with continuous delivery, you can decide to release daily, weekly, fortnightly, or whatever suits your business requirements. However, if you truly want to get the benefits of continuous delivery, you should deploy to production as early as possible to make sure that you release small batches that are easy to troubleshoot in case of a problem.

*Source: https://www.atlassian.com/continuous-delivery/principles/continuous-integration-vs-delivery-vs-deployment*

### Continuous deployment
> Continuous deployment goes one step further than continuous delivery. With this practice, every change that passes all stages of your production pipeline is released to your customers. There's no human intervention, and only a failed test will prevent a new change to be deployed to production.
> 
> Continuous deployment is an excellent way to accelerate the feedback loop with your customers and take pressure off the team as there isn't a "release day" anymore. Developers can focus on building software, and they see their work go live minutes after they've finished working on it.

*Source: https://www.atlassian.com/continuous-delivery/principles/continuous-integration-vs-delivery-vs-deployment*

![ci-cd](../images/ci-cd-asset-updates.png)
*Source: https://www.atlassian.com/continuous-delivery/principles/continuous-integration-vs-delivery-vs-deployment*

## IP implementation

### CI Angular
For my Angular application, I'm using Github actions for CI with Sonarcloud for my static code analysis. Down below is a snippet of the yml file for the Angular application.
```yml
name: Node.js CI  

on:
  push:
    branches: ["main", "dev"]
  pull_request:
    branches: ["main", "dev"] 

jobs:
  build_and_test:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [18.x]
        # See supported Node.js release schedule at https://nodejs.org/en/about/releases/  

    steps:
      - uses: actions/checkout@v3
        with:
          # Disabling shallow clone is recommended for improving relevancy of reporting
          fetch-depth: 0
      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node-version }}
          cache: "npm"
      - run: npm install
      - run: npm ci
      - run: npm run build --if-present
      # - run: npm test --passWithNoTests  

      - name: SonarCloud Scan
        uses: sonarsource/sonarcloud-github-action@master
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
```
*14 december 2022, subject to change.*

### CI Spring Boot
For my Spring Boot API, I'm also using GitHub actions for CI, but with Codiga for static code analysis. I initially wanted to use Sonarcloud for both my Angular application and Spring Boot API, but after running into issues with Sonarcloud not being able to run with a Spring Boot maven project, I've decided to use Codiga instead. Down below is a snippet of the yml file for the Spring Boot API.

```yml
name: Java CI with Maven  
  
on:  
  push:  
    branches: [ "main", "dev" ]  
  pull_request:  
    branches: [ "main", "dev" ]  
  
jobs:  
  build:  
  
    runs-on: ubuntu-latest  
  
    steps:  
    - uses: actions/checkout@v3  
    - name: Set up JDK 17  
      uses: actions/setup-java@v3  
      with:  
        java-version: '17'  
        distribution: 'temurin'  
        cache: maven  
    - name: Build with Maven  
      run: |   
        mvn -B verify --file pom.xml  
        mkdir staging && cp target/*.jar staging  
  
    - name: Check code meets quality standards  
      id: codiga  
      uses: codiga/github-action@master  
      with:  
        repo_token: ${{ secrets.GITHUB_TOKEN }}  
        codiga_api_token: ${{ secrets.CODIGA_TOKEN }}  
        min_quality_score: '75'  
        max_defects_rate: '0.05'  
        max_timeout_sec: '600'
```
*14 december 2022, subject to change.*

### CD (Continuous Delivery) Angular
For the delivery of the Angular application, I'm using Docker with docker containers. The containers are set up via a docker-compose file, which creates a container for the application with the defined settings. Down below is a snippet of the docker-compose file for my Angular application.
```yml
version: "3.9"
services:
  shazzboard-frontend:
    container_name: shazzboard_frontend
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - 4200:4200
```

### CD (Continuous Delivery) Spring Boot
Like with the Angular application, I'm also using Docker for the CD of the Spring Boot API. Because the API runs with a MySQL, I needed to host a MySQL server together with the API. To do that, I needed to pull a MySQL image from Docker hub first and then create a network container containing both the MySQL image and the API with the docker-compose file. Down below is a snippet of the docker-compose file for my Spring Boot API.
```yml
version: "3.9"  
services:  
  mysqldb:  
    container_name: mysqldb  
    image: mysql:5.7  
    restart: always  
    env_file:  
      - .env  
    networks:  
      - shazamservice-mysql-net  
    ports:  
      - $MYSQLDB_LOCAL_PORT:$MYSQLDB_DOCKER_PORT  
    environment:  
      - MYSQL_ROOT_PASSWORD=$MYSQLDB_ROOT_PASSWORD  
      - MYSQL_DATABASE=$MYSQLDB_DATABASE  
    volumes:  
      - mysql_vol:/var/lib/docker/volumes/mysql_vol/_data  
  
  service:  
    container_name: shazam_service  
    depends_on:  
      - mysqldb  
    command: ["./wait-for-it.sh"]  
    build:  
      context: .  
      dockerfile: Dockerfile  
    restart: on-failure  
    env_file:  
      - .env  
    ports:  
      - $SERVICE_LOCAL_PORT:$SERVICE_DOCKER_PORT  
    environment:  
      - spring.datasource.url=jdbc:mysql://mysqldb:$MYSQLDB_DOCKER_PORT/$MYSQLDB_DATABASE?enabledTLSProtocols=TLSv1.2&useSSL=false  
      - spring.datasource.username=$MYSQLDB_USER  
      - spring.datasource.password=$MYSQLDB_ROOT_PASSWORD  
      - spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect  
      - spring.jpa.hibernate.ddl-auto=update  
    networks:  
      - shazamservice-mysql-net  
  
networks:  
  shazamservice-mysql-net:  
  
volumes:  
  mysql_vol:
```

### CD (Continuous Deployment)
To actually deploy deploy my application, there are automated deployment systems like Kubernetes that I can use for that. Since deploying the services falls out of scope for now, I won't be covering this step.