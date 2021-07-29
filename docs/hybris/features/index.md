---
layout: default
title: Platform Features
parent: Hybris
nav_order: 300
has_children: false
---

# Platform Features

## Bulit framework 

- uses ant build scripts to run tasks that extension compilation, code generation, copying and compilation


## Business Process Management 

- a sequence of steps or activities that is followed repeatedly
- usually modeled as flowcharts of action
- some action requires human interaction, other invoke an automated process
- each action specifies what action should follow
- manual task can also be added

## Cache

- it is a part of the persistent layer 
- reduces the DB calls
- caches the Item instances, all Item attributes, results of Flexible search queries

## Cluster

- a number of individual installations of Commerce application using one common database

## Containerization

- use docker images and run them as a software instances

## Data Retention 

- retain specific data until it is cleaned up by our strategy
- provides item locking service as well

## Data validation

- adopts JSR 303 (Bean validation) based on Hibernate validator
- provides run time configurations though `admincockpit`
- `ValidationService` intercepts all `modelService.save` calls and returns list of violations 
- can be called explicitly

## Digital Asset Management

- media conversion and media management tools
- manage in a centralized and consistent manner for various delivery channel
- `media` item is a container that holds a reference to any file, actual file will be local or in remote server 
- one File per Media, but different media can refer same file
- after Synchronization, source and target medias will be refering to the same file
- target CV medias holds reference to original file by `dataPK` and maintains the correct reference to the files  
- `media.getURL()` to get the actual location of the file
- Media Folders enable storage and access controls
- Media Formats is a logical label that holds the Format of the Media that we decide
- Media Containers is a logical grouping having medias with different formats (used primarily for images)
- Media Contexts is a mapping of formats that holds media format to replace and replacement
- Media managed by `MediaWeb` will have the following format for Not Secured URL pattern `${server_address} : ${port} / localMediaWebRoot / realFileName ?context= encodedMediaContext`. 
- Other Medias that don't use `MediaWeb` should use `media.getURL()` for reference
- URLs can be formed as below
    - with prettyURL disabled `/medias/someFileName.jpg?context=NAYDCL3IGAZC6ZTPN4XGU4DHHI5DU4LXMVZHI6JRGIZTINI`
    - with prettyURL enabled `/medias/sys_master/folder/h78/hd0/8796125986846/someFileName.jpg`
- ETag provided for web cache validation
- Response headers can be added via `local.properties` using the following formats 
    - `media.set.header.<header_name>=<value>`, 
        - e.g. `media.set.header.Content-Security-Policy=disable`
    - `media.set.mime.header.<mime>.<headername>=<headervalue>`, 
        - e.g. `media.set.mime.header.text/plain.content-security-policy=disable`

## Generic Data Report and Audit

- to collect Raw data from DB and present it as a Report
- to create historical report use Audit

## ImpEx

- text based import and export funtionality
- used to create, update, remove and export data
- use cases
    - update the data at runtime
    - create initial data/ migrate the data during upgrade
    - facilitate data transfer task such as cronjob, synchronization with third party systems such as LDAP or SAP R/3 

## Internationalization and Localization

- adapt the system to multiple languages 

## Java Message Services

- it offers a asynchronous approach to possibly remote method invocation
- client doesn't wait until the service completes the task
- `Message` is sent to `Message Broker` who forwards the message to one or more `Message Listeners` 
- `Message Broker` can contain many buckets called  
    - `Queues` - one service to act upon a message
    - `Topics` - broadcast message to one or more services
- supports Spring based JMS framework

## Logging

- A tool to capture and persits the data. 
- comes with Apache Log4J 2 logging framework
- can be used along with SLJ4J.
- can produces JDBC logs in JSON format

## Multitenancy 

- multitenant mode support - several distinct data sets maintained on one single installation
- same code base for several fully individual and isolated set of data
- different from Clustering
- Customizing Options
    - Time Zones
    - DB url, driver, username and password 
    - currency, currency format, date format
- each tenant has it own configured extensions 
- in `local.properties` tenants can be configured using `installed.tenants=junit,foo,t1,t2`
- property files defined as `tenant_{tenantID}.properties`
- local properties are defined as `local_tenant_{tenantID}.properties` in `config` folder
- `master` is the primary tenant, having no db table prefix. it is created automatically and cant be removed 
- DB table prefix (max 5 characters) is configured by `db.tableprefix=myjunit_` in `local_tenant_junit.properties`
- if DB table prefix and URL is missing, then it uses master tenant configuration 
- when initializing a new tenant, all other tenants always stay online  
- In a single instance, when adding a new tenant, we need to shutdown, rebuild and initialize platform. If in cluster, then one of the hybris instance can be used and other instance can be online.
- register a backgroud thread using `createAndRegisterBackgroundThread` method in Tenant interface, this helps in completing the task before platform shutdown 
    - `shutdown.background.processes.wait.timeout=30`
    - `shutdown.background.processes.forced.wait.timeout=30` 

## OAuth 2.0

- default authorization framework for Omni Commerce Connect (OCC) Web services
- Client request access token (instead of user credentials) from an Authorization Server (different to Resource Server - can de a different server)
- These access tokens are valid for some time, if expired then tokens can be refreshed

```
Protocol Flow:

     +--------+                               +---------------+
     |        |--(A)- Authorization Request ->|   Resource    |
     |        |                               |     Owner     |
     |        |<-(B)-- Authorization Grant ---|               |
     |        |                               +---------------+
     |        |
     |        |                               +---------------+
     |        |--(C)-- Authorization Grant -->| Authorization |
     | Client |                               |     Server    |
     |        |<-(D)----- Access Token -------|               |
     |        |                               +---------------+
     |        |
     |        |                               +---------------+
     |        |--(E)----- Access Token ------>|    Resource   |
     |        |                               |     Server    |
     |        |<-(F)--- Protected Resource ---|               |
     +--------+                               +---------------+
```     

## Ordering, Payment and Pricing Standards

- default implementation of stragies are for general business cases
- replace these with own implementation
- Ordering, Delivery, Payment etc

## Performance and Monitoring

- implements Java Management Extensions (JMX)
- to allow the remote observation and management of your installation, allowing you to call methods within a running application for direct application monitoring.

## Polyglot Persistence

- provides a way to store specified data types in an alternative storage
- polyglot persistence querying language is designed for document-based storage that cannot support the SQL query language
- supports the create, read, update, and delete operations as well as searching for objects
- Supported DBs - MySQL and HSQL
- default implementation available for `Cart`

## Primary Keys

- use to filter DB databy primary key
- encoded informations
    - Creation time (old items use UUID)
    - Counter 
    - Typecode
- Get primary keys by using the following methods:
    - item.getPK()
    - jalosession.getItem(PK)
- Convert primary keys to a different format:
    - From Strings to PK and vice versa
    - From hex to PK and vice versa
- Extract data from a primary key:
    - getTypeCode()
    - getCreationTime()
```
8796093087748 (decimal format)

0x80000010004 (hexadecimal format)

0000 0000 0000 0000 0000 1 000 0000 0000 0000 0000 0000 0001 0 000 0000 0000 0100

63-------------------44 43 42--------------------------------15 14--------------0

```
| Bit Digit | Value                                 | Information           |
|:---------:|:--------------------------------------|:----------------------|
| 44 to 63  | 0000 0000 0000 0000 0000              | Counter (High bits)   |
| 43        | 1                                     | Marker Bit            |
| 15 to 42  | 000 0000 0000 0000 0000 0000 0001 0   | Counter (Low bits)    | 
| 0 to 14   | 000 0000 0000 0100                    | Type code             | 

## Product and Data Modeling

- Two ways to model a Product - Type system & Classification Functionality
- Type system using `items.xml`
- Classification using Backoffice > Classification System or CSV based 
- Usage of both is preffered. If an attribute is used in Bussiness logic then use Typing. If an attribute is used for displaying, then modelling as classification is preferred.

## Product Content and Catalog

- catalog funtionality for holding, structuring and managing Products and Product information
- synchronization functionality allows synchronizing a catalog version in one directional way for updating the catalog according to another
- classification functionality allows to define product attributes based on classification methods
- variant functionality and data model for Product allows to create products that are different in some aspect but has same basic model
- category functionality allows to build a hierarchical product structure, where products are kept in categories

## Search

- Two search mechanism available - FlexibleSearch and GenericSearch
- Flexible Search is SQL-based syntax and used in Administrative cockpit and Commerce API (Code)
- Generic Search encapsulates the FlexibleSearch syntax and used in Backoffice Search Area and Commerce API
- ViewType is representation of DB view used in Backoffice Report Definitions

## Secure HTTP Transactions

- uses `Charon` library that allows applications to interact with external (micro)services
- it uses OAuth + HTTP for secure transactions

## Security and User Management

- tools for access control and data encryption
- appropriate access rights, user management and user groups ensure items are accessible only to authorized users
- Security Policy should include data encryption, authenticated protocols like LDAP or Spring Security Framework
- `Permission Services` used to define access rights policy for user and user group

## ServiceLayer

- services are business code separated into java classess that implement specific, well-defined requirement
- based on Spring Framework using interface-oriented design and dependency injection
- provides hooks for model life-cycle events, System event life-cycle (update/init)
- provide framework for publishing and receiving events 

## ServiceLayer Direct

- allows you to read and persist data in the database and completely skip the Jalo layer
- still use `modelService` to persist
- to enable it globally `persistence.legacy.mode=false` 
- enable ServiceLayer Direct just for a current session context

```java
PersistenceUtils.doWithSLDPersistence(() -> {
    final TitleModel title = modelService.create(TitleModel.class);
    title.setCode("foo");
    modelService.save(title);
 
    return title;
});
``` 

## [Workflow and Collaboration](../workflow)

- a concept for modeling and processing of batch card-like processes.
- processes which are separated into several tasks and are completed when these tasks are completed
- provides the comments functionality within the workflow & collaboration features 