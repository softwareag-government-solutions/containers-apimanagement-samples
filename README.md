# webmethods Containers for API Management - Samples by Software AG Government Solutions
A repository containing instructions and examples of how to deploy webMethods API Management containers into an environment using [docker-compose](https://docs.docker.com/compose/).

## Prerequisites
If you are using Docker Desktop for Mac, resources should be set for 12 CPU and 12 GB RAM.

If you will be running these containers on a server (as opposed to a workstation), less resources are typically required for operation.  This has been tested successfully on an AWS m5a.2xlarge (8 CPU, 32GB Memory).

The API Portal and the API Gateway from Software AG require license key files to function.  Please have these files handy, the instructions below will guide you through where you will need to place these files during setup.

Software AG Government Solutions hosts the API Management containers on the GitHub Container Registry.  You not only will need to have a GitHub account to pull these artifacts, you will also have to create a personal access token.  Follow these steps:
1.	In GitHub, navigate to Settings -> Developer settings -> Personal access tokens.
2.	Click the “Generate Access Token” button (you may need to provide your login credentials as this time).
3.	Add a note to your token, something like “Docker Pulls from Software AG”.
4.	Click the checkbox next to the “read:packages” scope, then click the “Generate Token” button at the bottom of the screen.  Save the token somewhere safe.
5.	Issue the following Docker command to login to the registry.  Be sure to use your GitHub username and the token that was generated in Step 4 as the password.
`docker login ghcr.io`

For more details on the GitHub Container registry, please refer to the documentation here:
[Working with the GitHub Container registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)

__Important!__

The API Portal uses Elasticsearch.  If you are going to deploy to a Linux system, the maximum map count checks that the kernel allows a process to have at least 262,144 memory-mapped areas.  Please ensure that the host that docker is running on sets the vm.max_map_count to 262144. 

## Getting Started
For your convenience, Software AG Government Solutions has created Docker Compose scripts to accelerate deployment of the containers within your environment.  These scripts are available in the [webmethods Container Deployments](https://github.com/saggs-cloudops/webmethods-container-deployments) GitHub respository.


__Start by cloning the following repository to retrieve the docker scripts and configuration:__

`git clone git@github.com:saggs-cloudops/webmethods-container-deployments.git`

This project contains several docker-compose scripts to pull & deploy the API Management components in a wide variety of configurations: from a single node “standalone” environment to a multi-node arrangement suitable for exploring high-availability scenarios.

__Set the environment variable for the release you’d like to work with.  The current Generally Available release is version 10.7__

`export SAG_RELEASE=107`

`export REG=ghcr.io/softwareag-government-solutions/`

## Configuration Option 1 - A Standalone Gateway and a Standalone Portal

__Navigate to the Docker “home” directory of the API Gateway project:__

webmethods-container-deployments/docker/apigateway

__Copy your license files to the following directory.  The docker compose script will map these into the container into the appropriate spot using volumes.__

webmethods-container-deployments/docker/apigateway/licensing/apigateway-licenseKey.xml

webmethods-container-deployments/docker/apigateway/licensing/apiportal-licenseKey.xml

__Starting the containers:__

`docker-compose --env-file ./configs/docker.env${SAG_RELEASE} -f apigateway-with-apiportal/docker-compose.yml up -d`

__Stopping the containers:__

`docker-compose --env-file ./configs/docker.env${SAG_RELEASE} -f apigateway-with-apiportal/docker-compose.yml down`

__Stopping & removing volumes for the containers:__

`docker-compose --env-file ./configs/docker.env${SAG_RELEASE} -f apigateway-with-apiportal/docker-compose.yml down -v`

__Refreshing the containers:__

`docker-compose --env-file ./configs/docker.env${SAG_RELEASE} -f apigateway-with-apiportal/docker-compose.yml pull`


__These are the containers that will be started:__

_apigateway-standalone-apigateway-1_
This container is the API Gateway runtime.

_apiportal-standalone-apiportal-1_
This container is the API Portal runtime.

_apigateway-standalone-apigateway_configurator-1_
This container will run at startup and eventually complete and exit.  This is normal.  Its role is to configure the gateway with an initial set of values.

_apigateway-standalone-webmethods-sample-apis-bookstore-1_
This container is just a sample REST API endpoint with some sample data.  This is provided as an example native API that can be governed by the API Management Platform.

_apigateway-standalone-webmethods-sample-apis-uszip-1_
This container is just a sample REST API endpoint with some sample data.  This is provided as an example native API that can be governed by the API Management Platform.

---

__To access the API Gateway’s Administrator UI__

http://localhost:9072/

username: Administrator

password: somethingnew

__Sample API Gateway Users__

sampleuser1.admin

sampleuser2.apiprovider

sampleuser3.apiuser

__To access the API Portal__

http://localhost:18101/

username: system

password: manager
