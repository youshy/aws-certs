# Cloud Practitioner

## Regions

* US East
  * N.Virginia
  * Ohio
* US West
  * N.California
  * Oregon
* Asia Pacific
  * Mumbai
  * Seoul
  * Singapore
  * Sydney
  * Tokyo
  * Osaka-Local
* Europe
  * Frankfurt
  * Ireland
  * London
  * Paris
* China
  * Bejing
  * Ningxia
* Canada
  * Central
* South America
  * Sao Paulo
* AWS GovCloud
  * US-West
  * Secret Region (FBI/CIA)

Each region is physically connected to multiple Availability zones.

There are over 100 edge locations that prevents DDOS attacts and improves performance.

## Cloud Compuiting Models

* IaaS - Infractructure as a Service - basic blocks for building cloud IT (VPC, EC2, EBS)
* PaaS - Platform as a Service - underlying infrastructure managed by AWS, but the frontend has to be managed by the user (RDS, EMR, ElasticSearch)
* SaaS - Software as a Service - complete product run and managed by the service provider (Email, Office 365, Salesforce)
* FaaS - Function as a Service (Abstract Services) - build and run applications without thinking about servers (S3, Lambda, DynamoDB, SNS)

## AWS Storage Services

* S3 - Simple Storage Service - easily accessible
* Glacier - more for archivisation
* EBS - Elastic Block Store
* EFS - Elastic File System - network attached storage
* Storage Gateway - enabled hybrid storage
* Snowball - helpful for migrating data

## Databases

* RDS - Relational Database Service
* DynamoDB - noSQL
* Redshift - based on postgres, big-data solution
* ElastiCache - in-memory cache
* DMS - Database Migration Services - orchestrator
* Neptune - graph database service

## Compute Services

* EC2 - Elastic Compute Cloud
* EC2 Autoscaling - scales automatically up or down
* Lightsail - easiest way to run the service
* ECS - Elastic Container Service - container management service for Docker
* AWS Lambda - FaaS

## Networking & Content Delivery

* Cloudfront - CDN with low latency
* VPC - Virtual Private Cloud
* Direct Connect - high speed connector to AWS cloud
* ELB - Elastic Load Balancing - can distibute across multiple zones, can bypass unhealthy instances
* Route 53 - Domain name system (DNS)
* API Gateway - managed service for creating APIs
