# Jenkins CI/CD Demo (AWS)

For an introduction on CI/CD, here's a great write up from Atlassian that you can read on:\
https://www.atlassian.com/continuous-delivery/principles/continuous-integration-vs-delivery-vs-deployment

## Overview

In this demo, we will be creating jobs in Jenkins that will handle application code builds and deploying it to a Kubernetes cluster in AWS EKS. 

For creating the Kubernetes cluster, please refer to the AWS EKS Demo guide here:\
https://github.com/halflogic/aws-eks-demo

The diagram below is high-level overview of how CI/CD pipelines may be applied to an organization and the teams involved within process.

<img src="images/cicd.png" width="700" height="">

Here's a few details on the process involved during application code build and deployment. Infrastructure as Code (IaC) is also integrated in the build process that contains deployment configurations for the k8s pods.

<img src="images/build-deploy.png" width="700" height="">

## Create the Jenkins server

References: \
https://docs.aws.amazon.com/AmazonECS/latest/developerguide/docker-basics.html \
https://gist.github.com/npearce/6f3c7826c7499587f00957fee62f8ee9 \
https://www.jenkins.io/doc/book/installing \
https://github.com/jenkinsci/docker/blob/master/README.md

1. Create an ec2 instance with Amazon Linux 2, t3.small would be fine for this demo. You may need a larger instance once your demand increases.

2. Install and configure docker. and run jenkins in a container.

   ```
   # docker installation
   sudo yum update -y
   sudo amazon-linux-extras install docker -y
   sudo service docker start
   sudo usermod -a -G docker ec2-user
   sudo chkconfig docker on
   sudo yum install -y git
   # logout/restart
   ```

3. Install docker-compose, then build and run the jenkins container.\
   To do: Setup docker-compose yaml.

   ```
   # docker-compose installation
   sudo curl -L https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
   sudo chmod +x /usr/local/bin/docker-compose
   docker-compose version  # verify
   
   # jenkins installation
   https://www.jenkins.io/doc/book/installing/
   docker network create jenkins
   docker volume create jenkins-docker-certs
   docker volume create jenkins-data
   
   docker container run --name jenkins-docker --detach \
     --restart unless-stopped \
     --privileged --network jenkins --network-alias docker \
     --env DOCKER_TLS_CERTDIR=/certs \
     --volume jenkins-docker-certs:/certs/client \
     --volume jenkins-data:/var/jenkins_home \
     --publish 2376:2376 docker:dind
     
   docker container run --name jenkins-lts --detach \
     --restart unless-stopped \
     --network jenkins --env DOCKER_HOST=tcp://docker:2376 \
     --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 \
     --volume jenkins-data:/var/jenkins_home \
     --volume jenkins-docker-certs:/certs/client:ro \
     --publish 8080:8080 --publish 50000:50000 jenkins/jenkins:lts

   # get the initialAdminPassword
   docker exec -it $(docker ps -aqf "name=jenkins-lts") cat /var/jenkins_home/secrets/initialAdminPassword
   ```

4. Grab the IP of the ec2 instance and open Jenkins in a browser: http://your-ec2-instance-ip:8080 \
   Go through the Jenkins setup wizard, install the recommended plugins and setup an admin user.


## Create Github Token

To do

## Setup Credential in Jenkins

To do

## Create Pipeline Job

To do

