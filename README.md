# Push Docker Image to Docker Registry using Azure Pipeline

## Azure Pipeline
Azure Pipelines combines the Continuous Integration and Continuous Delivery to constantly and constistently test and build your code and deploy it to any target. With the help of this we are going to push docker image to docker public registry. For this to work we need :
- Docker Hub Account
- Azure Devops Account
- And code to create an image with Dockerfile

### Docker Hub
Docekr hub is place for storing, testing th container images of our aplications. With the free account we can setup public registries as much as we want. 
### Azure Devops
This account to use pipeline feature. To build and deploy our desired container image to Docker Hub. 
### Code to Deploy
The code we are going to create image with, is stored on github repository. Azure Devops provides the feature to use different repositories. In this case, we are going to use GitHub Repository.

## How to Do it?

Before going further, ensure that we have all the prerequisites with us.
Dockerfile is used to build the images. As it containes all the instructions to create the image like dependencies, commands, entrypoint etc. This Dockerfile stored on github repo with the same instructions ( the relative paths to directories ) as we are going to build image on our machine.

First, log in to Azure Devops account. We are not going to look into the project and other things. After successful login, we need to create Service Connection.
To use docker hub login credentials in pipelines we need the service creation. To create follw the steps:
- In the organization setting, search for service Connection and click New Service Connection and opt for Docker Registry option.
- Next to that, defining the DockerID and password, give the Service Connection desired name and Click Save Configure

Now we are ready to Create pipeline. Go to pipelines, and click Create pipeline. Then it will ask for which repository hub ( where our code is saved) to use. And select the desired repo.
Now Select Docker (Build An Image) option and click validate and configure. Use following script to build and push the image.
```
# Docker
# Build a Docker image 
# https://docs.microsoft.com/azure/devops/pipelines/languages/docker

trigger:
- master

resources:
- repo: self

variables:
  tag: '$(Build.BuildId)'

stages:
- stage: Build
  displayName: Build image
  jobs:  
  - job: Build
    displayName: Build
    pool:
      vmImage: 'ubuntu-latest'
    steps:
    
    - task: Docker@2
      displayName: Login to Docker Hub
      inputs:
        command: login
        containerRegistry: test
    - task: Docker@2
      displayName: Build and Push
      inputs:
        command: buildAndPush
        repository: username/repository
        tags: |
          v1
```
In the containerRegistry of steps block we are defining the service connection name for login comand. And also edit the username/repository for your use.

Click save and run. Wait till process is complected.
After building and pushing is done we are ready to use the docker images.

Hope You enjoy this Blog. Thanks for reading. Good Day!!!
