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

> More info [HERE](http://blog.itaysk.com/2017/11/20/deployment-strategies-defined)

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
3. Attach the new batch and terminate the existing batch.

> Rolling with additional batch ensure our capacity is never reduced. **this is important for application where a reduction in capacity could cause availability issues!**

**IN CASE OF FAILURE** - additional rolling update is necessary to roll back the changes.

## Immutable -Deployment method

1. Create a new Auto Scaling Group (ASG) with EC2 instances;
2. Deploy the updated version of the app on the new EC2 instances;
3. Point the ELB to the new ASG and the delete the old ASG which will terminate the old EC2 instances.

It's the safest way to deploy critical applications

**IN CASE OF FAILURE** - terminate the new instances as the old still remains.

## Deployment methods comparison

| Method | Impact of failed deployment | Deploy time | No downtime | No DNS change | Rollback process | Code deployed to Instances |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| At at once | Downtime | Short | False | True | Manual | Existing |
| Rolling | Single batch out of service; any successful batches before failure running new application version | Medium (time may vary) | True | True | Manual | Existing |
| Rolling with additional batch | Minimal if first batch fails; otherwise, similar to **Rolling** | Medium (time may vary) | True | True | Manual | New and Existing |
| Immutable | Minimal | Long | True | True | Terminate New | New |
| Blue/green | Minimal | Long | True | False | Swap URL | New |

## In-place vs blue/green

> In-place deployments are all the deployment methods defined earlier.

In-place could mean within the *scope of Elastic Beanstalk env*. All the deployment policies provided by EB could be considered as In-place as they are all within the scope of a single EB environment. That'd be: *All at once, Rolling, Rolling with additional batch and Immutable*.

In-place could mean within *the scope of the same server*(not replacing the server). Deployment policies which do not involve the server being replaced. That'd be: *All and once and Rolling*.

In-place could mean within *the scope of an uninterrupted server*. Traffic is never routed away from the server (taken out of service). Implements **zero-downtime** deploys where Blue/Green occurs on the server. EB is unable to deploy like this. Capistrano + Ruby on Rails + Unicorn would be an example of such deployment.

### Blue/green in the context of Elastic Beanstalk

In case of EB, **Immutable** deployment method is considered as In-place because all the changes occurs within the scope of EB container. Elastic Beanstalk defines Blue/Green deployment as swapping EB environments and this occurs on the DNS level.

## Configuration files

EB environments can be customised using configuration files. This can be found in a hidden folder `.ebextensions` in the root of the project, which contains `.config` files.

`.config` files can: 

1. Set options
2. Configure Linux/Windows servers
3. Define/configure custom resources

## Environment manifest

It's a file called `env.yml` which is stored in the root of the project.
