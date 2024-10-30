# MMi Repository Control

This page serves as a quick access point to different types of repositories. A brief description of each type of repository and how they are organized is provided. Use the links to access the corresponding projects directly.

## General Documentation
- [Development Branch Arquitecture](doc-branch.md)
- [Telemetry Platform Documentation](telemetry_platform_documentation.md)

## Repository Index
- [AWS Lambdas](#aws-lambdas)
- [Microservices](#microservices)
- [Application Backends](#application-backends)
- [Application Frontends](#application-frontends)
- [Templates](#templates)


### **AWS Lambdas**

Repositories containing Lambda functions implemented in AWS. These projects are usually designed to handle specific tasks or efficiently process events.
- #### Documentation
  - [Creating/Migrating new repositories](lambda-new.md)
  - [AWS Local Test](lambda-local-test.md)

- #### Repository List
  - [Permanent SWD Ingestion](https://github.com/MMi-Software/lambda-swd-perm): Permanent SWD data ingestion Lambda.

### **Microservices**

Repositories containing microservices designed to run independent services within a larger system. Each microservice may be responsible for a specific functionality and technology.
- #### Documentation
  - [Creating/Migrating New Repositories](ms-new.md)

- #### Repository List
  - [mSafe3 Ingestion](https://github.com/MMi-Software/mmi-micro-service-msafe3): mSafe3 Ingestion
  - [Reports](https://github.com/MMi-Software/microservice-reports): Creates, returns, and sends metric reports.
  - [Alarms](https://github.com/MMi-Software/ms-alarms): Handle, open, close and notify device alarms.
  - [Assignation](https://github.com/MMi-Software/ms-assignation): Manage Contact groups and device assignation.
  - [Notification](https://github.com/MMi-Software/ms-notification): Send notifications and manage status.
  - [Cameras](https://github.com/MMi-Software/ms-cameras): Events, photos and cameras live.

### **Application Backends**

Repositories dedicated to application backends, which may include APIs, data services, and business logic to support web applications.
    
- #### Repository List
  - [mSuite](https://github.com/MMi-Software/mmi-msuite-backend)
  - [Virtual Assistant](https://github.com/MMi-Software/virtual-assistant-backend)
  - [Measures](https://github.com/MMi-Software/measures_api)
  - [mLevel](https://github.com/MMi-Software/mlevel-back-end)
  - [Drillout Cameras](https://github.com/MMi-Software/be-drillout-cameras)
  - [ms-alarms](https://github.com/MMi-Software/ms-alarms)
 

### **Application Frontends**

Repositories containing the source code for application frontends, including user interfaces and web components.
    
- #### Repository List
  - [mSuite](https://github.com/MMi-Software/mmi-msuite-webapp)
  - [Virtual Assistant](https://github.com/MMi-Software/virtual-assistant-webapp)
  - [mLevel](https://github.com/MMi-Software/mlevel-front)
  - [Drillout Cameras](https://github.com/MMi-Software/drillout-cameras)
    

### **Templates**

## General Documentation
- [Creating/Migrating New Repositories](tp-new.md)
  
Generic repositories for creating standardized elements.
- #### Repository List
  - [Microservices](https://github.com/MMi-Software/mmi-template)
  - [Desktop Apps](https://github.com/MMi-Software/tp-desktop-app)
  - [AWS lambdas](https://github.com/MMi-Software/mmi-lambda-template)
