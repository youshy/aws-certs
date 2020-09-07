# 01 - Elastic Beanstalk

PaaS - Platform as a Service - a platform allowing customers to develop, run, and manage applications without the complexity of building and maintaining the infra typically associated with developing and launching an app. EB is an equivalent of Heroku, but on AWS.

EB is not recommended for "Production" applications (in this case, talking about enterprise-size companies).

EB is powered by a CloudFormation template that sets up:

* Elastic Load Balancer
* Autoscaling Groups
* RDS Database
* EC2 Instance preconfigured/custom platforms (language/framework setup)
* Monitoring (CloudWatch, SNS)
* In-Place and Blue/Green deployment methodologies
* Security (rotates password)
* Can run **Dockerized** environments

## Supported languages (preconfigured platforms)

* Go
* NodeJS
* Java
* Python
* Ruby
* PHP
* ASP.NET
* Docker

## Web environment types

* Load Balanced env - designed to scale, uses load balancer
* Single-Instance env - doesn't use the load balancer

## Deployment policies

Available options in Elastic Beanstalk:

| Deployment Policy | Load Balanced Env | Single-Instance Env |
| :-- | :-- | :-- |
| All at once | True | True |
| Rolling | True | False |
| Rolling with additional batch | True | False |
| Immutable | True | True |



