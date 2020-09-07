# 01 - Elastic Beanstalk

---

## TL;DR

* **Elastic Beanstalk** handles the deployment, from capacity provisioning, load balancing, auto-scaling to application health monitoring
* Used when you want to run a web application but doesn't want to think about the infra
* Free to use (pay for provisioned services: EC2, ELB, RDS)
* Recommended for test and dev apps or small-size prod
* Multiple preconfigured platforms
* Can be run in a single container (using EC2) or multiple (using ECS)
* Can launch either a **Web environment** or a **Worker environment**:
  * Web: Single-Instance or Load-Balanced (difference is that Load-Balanced will have an Elastic Load Balancer)
  * Worker: Uses SQS queue and initialises SQS daemon on EC2 instances
* Deployment policies:
  * All at once: takes all out of service, applies changes, puts them back. Fast, has downtime
  * Rolling: updates services in batched, reduced capacity based on batch size. In case of failure: rollback
  * Rolling with additional batches: adds new servers in batches to replace old, never reduces capacity. In case of failure: rollback
  * Immutable: creates the same amount of servers, switches to new ones, removes old ones. Most secure, yet slowest
* Rollback deployment policies requires Elastic Load Balancer - cannot be used with Single-Instance web
* In-Place deployment is when deployment happens within the same environment, all EB policies are in-place
* Blue/Green deployment is when deployment swaps environments outside of the current one
* Folder with config files: `.ebextensions`
* EB can use custom images
* If EB creates RBS, then it will be destroyed when deleting EB environment

---

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

### Example: Linux

```yaml
# Packages: download and install pre-packaged applications and components
packages:
  yum:
    libmemcached: []
    ruby-devel: []
# Groups: create Linux/UNIX groups and assign group IDs
groups:
  groupAdmin: {}
  groupDev:
    gid: "12"
# Users: create Linux/UNIX users
users:
  andrew:
    groups:
      - groupAdmin
    uid: 87
    homeDir: "/andrew"
# Files: create files on the EC2 instance (inline or from url)
files:
  "/home/ec2-user/application.yml":
    mode: "000755"
    owner: root
    group: root
    content: |
      SECRET: 000destruct0
# Commands: execute commands on the EC2 instance before app setup
commands:
  1_project_root:
    command: mkdir /var/www/app
  2_link:
    command: ln -s /var/www/app /app
# Services: define which services should be started or stopped when the instance is launched
services:
  sysvinit:
    nginx:
      enabled: true
      ensureRunning: true
# Container Commands: execute commands that affects your application source code. Name is misleading, it doesn't necesarily mean container as in Docker.
container_commands:
  0_collectstatic:
    command: "django-admin.py collectstatic --noinput"
  1_syncdb:
    command: "django-admin.py syncdb --noinput"
    leader_only: true
  2_migrate:
    command: "django-admin.py migrate"
    leader_only: true
  3_customize:
    command: "scripst/customize.sh"
```

## Custom Image

If necessary, there's an ability to run a custom image on EB. That might improve provisioning times.

## Configuring RDS

A database can be added **inside** or **outside** EB environment.

* Inside EB - whenever the EB env is terminated, then the DB will be terminated as well. Mostly intended for dev.
* Outside EB - persistent DB. Mostly intended for prod.
