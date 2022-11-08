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
### CI
Within my individual project, I've chosen to use GitHub Actions for my CI. Down below is a snippet of the yml file for one of my microservices in Java.

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

run: mvn -B package --file pom.xml

- name: SonarCloud Scan

uses: sonarsource/sonarcloud-github-action@master

env:

GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
```
*12 october 2022, subject to change.*
