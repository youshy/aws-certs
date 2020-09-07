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

## All At Once -Deployment method

1. Deploy the new app version to all instances at the same time;
2. Takes all instances **out of service** while the deployment progresses;
3. Servers become available again.

It's the fastest but also the most dangerous deployment method

**IN CASE OF FAILURE** - rollback is performed by re-deploying old/original version to all instances.

## Rolling -Deployment method

1. Deploy the new app version to a batch of instances at a time;
2. Takes batch's instances **out of service** while the deployment progresses;
3. Reattaches updates instances;
4. Goes onto next batch, taking them out of service;
5. Reattaches those instances (rinse and repeat)

**IN CASE OF FAILURE** - additional rolling update is necessary to roll back the changes.

## Rolling with additional batch -Deployment method

1. Launch new instance that will be used to replace a batch;
2. Deploy update app version to new batch;
3. Attach the new batch and terminate the existing batch

> Rolling with additional batch ensure our capacity is never reduced. **this is important for application where a reduction in capacity could cause availability issues!**

**IN CASE OF FAILURE** - additional rolling update is necessary to roll back the changes.
