---
layout: default
title: Platform Architecture
parent: Hybris
nav_order: 100
has_children: false
---

# Platform Architecture

# Core Extentions

![Platform Core Extensions](/img/hybris/platform_core_extensions.png)

- located in the `<HYBRIS_BIN_DIR>/platform/ext`
- Extensions
	- advancedsavedquery
	- catalog
	- comments
	- commons
	- core
	- deliveryzone
	- europe1
	- hac
	- impex
	- maintenanceweb
	- mediaweb
	- oauth2
	- paymentstandard
	- platformservices
	- processing
	- scripting
	- testweb
	- validation
	- workflow

## advancedsavedquery extension

- saved query functionality

## catalog Extension

- catalog, synchronization, classification, Variant, Category functionality

## comments Extension

- comments functionality for workflow

## commons Extension

- provides core helper methods for templates
- `Formatter framework` - coverting item content to PDF
- `Translator framework` - converting HTML format to another (e.g. Adobe InDesign)
- `Rederer framework` - creating for email templates using `Apache Velocity` 

## core Extension

- most basic, technical foundation pieces for SAP Commerce 
- persistence handling, ordering process, contains service layer functionality

## deliveryzone Extension

- organizes countries and regions for relating shipping costs to them
- delivery zone may contain several countries 
- `ZoneDeliveryMode` - represent the logistic company, it holds delivery fees and payment modes
- `ZoneDeliveryModeValue` - represents delivery fee 
- `Zone` - represent the countries to which a given PaymentMode delivers to

## europe1 Extension

- default pricing system 
- uses price rows, product information and customer information to arrive at final price
- enables us to implement our own pricing strategy
- manages prices, discounts and taxes to products and customers
    - per line item
    - per order
    - multiple units
    - scale prices
    - date ranges
    - grouping per customer and / or products 
    - Net/ gross prices

## hac Extension

- default administration web application that offers administration, monitoring and configuration functionalities
- default url `http://localhost:9001` or `https://localhost:9002`
- For Administration console only mode, run `adminserver.bat`

## impex Extension

- allows us to create, update, remove and export platform items

## maintenanceweb Extension

- funtionality for web maintenance page if the web front end is not available

## mediaweb Extension

- two funcionalities 
    - to define where the media files are located 
    - to redirect the HTTP and HTTPS requests to them, done by `RedirectServlet` 
- default Media File Storage location
- for cluster prefer use of shared storages over `media.replication.dirs`

## oauth2 Extension

- exposes HTTP endpoint as Authorization server
- default url `http:/localhost:9001/authorizationserver`

## paymentstandard Extension

- manages payment methods / modes
- `StandardPaymentMode` - represent the means of payment 
    - specifies payment info and applicable delivery modes 
- `StandardPaymentModeValue` - cost of using (transactional cost)
- subtypes of `PaymentInfo` 
    - `CreditCardPaymentInfo` - creditcard
    - `InvoicePaymentInfo` - invoice
    - `AdvancePaymentInfo` - advance payment  
    - `DebitPaymentInfo` - debitentry (debit)
- instances of payment info represent individual payment settings for a user
- debit or credit card payment will create a copy of payment info in the Order
- advance and invoice payment will not create a copy of payment info as they don't have any credit card or banking informations

## platformservices Extension

- part of serviceLayer that has functional and business service

## processing Extension

- provides Cronjob service, Task service and Process engine 
- `Cronjob Service` executes long task called `cron jobs` at regular interval (periodic). it is easy to check and logs results for each run 
- `Task service` is a light weight scheduling framework, without the overhead of cronjob features, they can schedule `Spring based actions` at specific times or events
- `Process Engine` enables us to define business process through XML process definitions and run it asynchronously

## scripting Extension

- allows us to execute logic using Scripting languages without restarting the Server
- scripts can be stored in DB (`ScriptModel`), classpath, file system, remote repositories or on the fly

## testweb Extension

- provides a Web user interface to test one or more tests or test suites

## workflow Extension

- provides a concept for modeling and processing of batch card-like processes

## validation Extension

- enable us to define data validation constraints, validate before save and notify validation violations

# Optional Extensions

![Platform Optional Extensions](/img/hybris/platform_optional_extensions.png)

- located in the `<HYBRIS_BIN_DIR>/modules/platform`
- Extensions
	- adminapi
	- amazoncloud
	- auditreportservices
	- azurecloud
	- deltadetection
	- deprecated
	- embeddedserver
	- gridfsstorage
	- groovynature
	- mediaconversion
	- patches
	- patchesdemo
	- samlsinglesignon
	- springintegrationlibs
	- tomcatembeddedserver
	- ydocumentcart
	- yempty
	- yhacext
	- yvoid
