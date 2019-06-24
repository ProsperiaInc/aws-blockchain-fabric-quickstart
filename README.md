AWS Amazon Managed Blockchain QuickStart  
===============================================

This solution shows how to create an AWS EKS Cluster and deploy a simple web application with an external Load Balancer. This readme updates an article "Getting Started with Amazon EKS" referenced below and provides a more basic step by step process.  Unfortunately this is a pretty manual effort right now.

Note:  This how-to assumes you are creating the eks cluster in us-east-1, you have access to your AWS Root Account, you know how to create an EC2 Instance, and can login to the instance from your laptop.

Steps:  

  Configure Your AWS EC2 Instance  
  Create AWS EKS Cluster using AWS CloudFormation  
  Configure kubectl on Your EC2 Instance   
  Deploy WebApp to Your Cluster  
  Configure the Kubernetes Dashboard (optional)
  Remove Your AWS EKS Cluster  


To make this first microservice easy to deploy we'll use a docker image located in DockerHub at kskalvar/web.  This image is nothing more than a simple webapp that returns the current ip address of the container it's running in.  We'll create an external AWS Load Balancer and you should see a unique ip address as it is load balanced across containers.

The project also includes the Dockerfile for those interested in the configuration of the actual application or to build your own and deploy using ECR.
