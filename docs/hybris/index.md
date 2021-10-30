---
layout: default
title: Hybris
nav_order: 30
has_children: true
---

# Hybris

## Architecture

### What is the SAP Hybris Architecture?

SAP Hybris has the following 

![Hybris Architecture](/img/hybris/hybris_architecture.png)

- Core Platform 

    It provides the core features like persistence, logging, caching, cron jobs, multi-tenancy, security, search, clustering, and task queuing, i18n among others

- Tomcat Application Server
- Data base and other connections to external DBs
- Business layer / API having commerce , CMS, PIM, OMS services
- Storefronts, HAC, Backoffice and other Cockpits

### What is an Extension and Addon  in Hybris?

Extension generally represents larger functionality and each extension is provided to address specific functionality. Extension generally contains business logic,item type definitions,jsp pages(if its web extension) etc.

Addon is also kind of extension but it is generally used to enhance the existing functionality. Addon will never alter any existing code rather it just adds additional code to the existing code without impacting anything.
In addition to this, we can also achieve reusability of code using addon.

Technically addon is a regular extension but whenever we build its codebase is copied inside target extension. 

A normal extension doesn't need to install but addon needs to. It means if you are creating an addon, you need to install it in your existing storefront template.

### When to Choose extension ?

- Whenever we are developing completely new functionality
- Whenever we are developing complex functionality like code for integration with third party system, etc

### When to chose Addon?

- Whenever we are required to enhance storefront by adding some functionalities like Captcha,asm,etc
- Whenever we are not allowed to alter the existing codebase but need to enhance its functionality
- Whenever we need to add plugin functionality which can be added and removed as and when required (using addon install and uninstall)

## How to create an Addon and install?

- Create addon by using below cmd.
- Configure localextension.xml by adding the addon
- Do clean build `ant clean all`
- install addon 
- Do clean build `ant clean all`
- also to remove addon 
- Do clean build `ant clean all`

```
# create addon
ant extgen -Dinput.template=yaddon -Dinput.name=myaddon -Dinput.package=com.myapp

# install addon
ant addoninstall -Daddonnames="addonname" -DaddonStorefront.<storefrontTemplateName>="StoreFront"

# Uninstall addon
ant addonuninstall -Daddonnames="addonname" -DaddonStorefront.<storefrontTemplateName>="StoreFront"
```

## Data Modelling

### How are localized properties for a type stored in DB?

The localized properties of a type is stored in a separate table if atleast one property is localized. Hybris adds 'lp' to the actual table

e.g productslp 

### How are generic audit tables for a type stored in DB?

Their names are constructed using the item table name, code and a suffix “sn”. 

e.g products1sn = products + 1 (typecode) + sn

truncation is also possible e.g. products4restri1081sn

### What is the use of props / property table in Hybris ?

Table defined by propertytable attribute of the deployment is used to store the data for a type when the value of dontOptimize is true. 

Basically if you set the dontOptimize modifier the object will be stored in this props table, making it more expensive to search by, but releasing the original object of what is expected to be a heavy column in the database. 

### What are ACL Entries?

ACL means Access Control Lists.  Used by JALO AccessManager. The User/Object permissions are stored here.

Defined by core-advanced-deployment.xml

### How to change the max length of table name in Hybris? 

The maximum length for a deployment table name in Hybris is 24 characters. It is defined in the `hybris/bin/platform/resources/advanced.properties` as below `deployment.tablename.maxlength=24`


### What is PK ?

Hybris primary key (PK) is a 13-digit number that is unique across the entire database. The number has data encoded inside it.

Platform -> PK Analyzer

### How PK is generated?

Information encoded in each PK includes:

Creation time (for old UUID based PKs)
Counter (for 5.0.0 PKs)
Typecode

The counter based PK is used as default, and the UUID PK is deprecated.

e.g. 

8796093087748 (decimal format)

0x80000010004 (hexadecimal format)

### Can a Hybris PK be generated outside of Hybris?

No.  It is not possible to generate a PK outside of Hybris.  The PK of an item in one database could be invalid in another database.  Hybris has internal counters for PKs which aren't accessible to developers. 

### Explain Hybris Type System ?

Hybris Type system is used to design data modeling in Hybris via items.xml file in each extension.

At build time and database-initialization time, the platform combines all the XML declarations from the extensions being used, and generates Java classes and a database schema.

It has the following types supported for data modeling :

- Item types − used to create tables.
- Relation types − used to create relation between tables.
- Atomic types − used to create various Atomic types.
- Collection types − used to create Collections.
- Map Types − used to define maps.
- Enum types − used to define Enums.

```xml
<items>	
    <atomictypes>atomictypesType</atomictypes> [0..1]	
    <collectiontypes>collectiontypesType</collectiontypes> [0..1]
    <enumtypes>enumtypesType</enumtypes> [0..1]	
    <maptypes>maptypesType</maptypes> [0..1]	
    <relations>relationsType</relations> [0..1]	
    <itemtypes>itemtypesType</itemtypes> [0..1]	
</items>

```

- [items.xml Element Reference](https://help.sap.com/viewer/d0224eca81e249cb821f2cdf45a82ace/1905/en-US/8bff7a568669101488a5e40cb7bbd0b9.html)
- [Hybris DB Structure](https://hybrismart.com/2018/05/06/explaining-hybris-database-structure-2/)

### What is the hjmpts column in hybris for?

SAP Commerce Cloud platform provides optimistic locking through a version property (hjmpTS) on your persistent items. This property is automatically managed by the platform. It is incremented each time you update the item and there are no conflicts.

The conflicts happen if some other process or thread tries to update the item as well. The first attempting submit an update wins, all the rest are rejected.

Default is `hjmp.throw.concurrent.modification.exceptions=false`. 

If false, only the dirty attributes will be written and the final result will be a merge. If true, then HJMPException is thrown, which will force a transaction rollback. 

For a single-node use, there is a synchronized keyword in Java. However, for a multi-server setup, it is not enough.

### Why the order is important in items.xml?

Each items.xml is parsed in single pass. This means, more specific types are dependent on general types. 

### What is deployment table?

SAP Commerce allows you to explicitly define the database tables where the values of instances of a given type will be written. This process is referred to as deployment.

- table - specifies the table where the instances of the item type are stored.
- typecode - is used internally to create item PKs

### What is typecode ?

The typecode is an internal ID for the Hybris type. 

Using typcode which already exists will cause failure. Typecode have values between 0 & 32767

- 0 and 10000 are reserved for hybris internal use
- greater than 10000 can be used for custom types.

### What does dontOptimize=true mean?

Using dontOptimize="true" stores the data for this type in a secondary table defined by the "propertytable" attribute of the deployment or the `props` table if no propertytable is defined.

Generally this should only be used if the attribute is intended to store huge amounts of data and wouldn't fit inside the item table.

Hence, it is not recommended to store attribute values in this way because it is more expensive to store and retrieve the values because joins are required.

### What is autocreate=true and generate=true?

autocreate is true, which lets the hybris Commerce Suite to create a new database entry for this type at initialization/update process.

generate=true at the item type level - It hints hybris to generate a new jalo class for this type during build time. 


case 1: In First definition of the type

- Setting the autocreate modifier to false causes a build failure. The first definition of a type has to enable this flag.

- If we set generate to false,then jalo class will not be generated however model class will always be generated. 

case 2: In second definition or extending types

- autocreate should be false.
- generate modifier is ignored

### What are the `<getter>` and `<setter>` in items.xml?

- `<getter>` - Allows to configure alternative getter methods at generated model.
- `<setter>` -Allows to configure alternative setter methods at generated model.

### What does redeclare=true means in items.xml?

Lets you re-define the attribute definition from an inherited type. In essence, you can use a different type of attribute as well as different modifier combinations than on the supertype. Default is 'false'.

e.g. entry in Cart and entry in Order are of different types

```xml

<attribute autocreate="true" redeclare="true" qualifier="entries" type="OrderEntryCollection"/>
                
<attribute autocreate="true" redeclare="true" qualifier="entries" type="CartEntryCollection"/>
```

### What is isSelectionOf in items.xml?

References an attribute of the same type. Only values of the referenced attribute can be selected as values for this attribute. Typical example: the default delivery address of a customer must be one of the addresses set for the customer. Default is 'false'.


```xml
<attribute autocreate="true" qualifier="defaultShipmentAddress" type="Address" isSelectionOf="addresses">
                    <persistence type="property"/>
                    <modifiers read="true" write="true" search="false" optional="true"/>
</attribute>
```
### what is the difference between persistent type and dynamic type in hybris in items.xml?

If the persistence type is set to persistant, Corresponding column will be created in the database and hence the values will be stored in the DB. So it’s called persistent attribute.

If the persistence type is set to dynamic, so the attribute value will not be stored in the database.

The attributeHandler points to a bean that must handle the DynamicAttributeHandler interface.

The write attribute is set to false, and therefore the attribute is read-only.

```xml
<persistence type="dynamic" attributeHandler="principalDisplayNameLocalizedAttributeHandler"/>


```

### How Dynamic Attributes are related to the hybris Model?

- The AbstractItemModel type holds a reference to the `DynamicAttributesProvider` object, which in turn, holds a collection of `DynamicAttributeHandler` objects.

- By using the methods get and set on the DynamicAttributesProvider object, the call is delegated to the valid DynamicAttributeHandler instance.

- From user point of view, this process is transparent so only the interaction with the `DynamicAttributeHandler` instance is done by calling getter on the hybris Model

e.g. Use cases  

- Calculate age based on date of birth. Here Age attribute is not present in DB, but we can calculate using Dynamic attribute.
- Giving promotion to the customer who registered before 2018.

### How to create Dynamic attribute? 

- Create an attribute in one of the tables in `<extensionName>-items.xml`.

```xml
<attribute type="java.lang.Integer" qualifier="customerSiteAge">
                           <description>Number of years for the customer associated with the site</description>
 <persistence type="dynamic" attributeHandler="siteAgeForCustomer"/>
                  </attribute>
```
- Provide a custom attributeHandler by assigning a bean id of the class that implements the DynamicAttributeHandler interface and holds the logic:

- Implementing Dynamic attribute : Create a new class named siteAgeForCustomer that implements `DynamicAttributeHandler` interface or `AbstractDynamicAttributeHandler` abstract class. Override the getter and setter methods

```java
    public String get() {
        //logic to get value
    }
    public String set() {
    }
```

- Register the spring bean in <extensionName>-spring.xml

```xml
<bean id="siteAgeForCustomer" class="de.hybris.servicelayer.testing.siteAgeForCustomer"   />
```

- Update the system : Update running system using the hybris Administration Console

### When we should go for Dynamic attribute?

We should choose dynamic attribute whenever we want to get some derived data based on existing values.
So instead of saving one more column, we can make it as dynamic and compute its value based on the current values.

### What are the advantages of Dynamic attribute?

- Data will not be saved in DB as its dynamic
- The custom logic is written once and used all the time wherever that attribute is required.

### What are custom-properties?

Custom attributes are certain defined attributes which are used in the type system definition to defined certain properties of a type.

In simple terms, they are marker properties or metadata. It is important to note that they are set to the itemtype itself and not to a specific instance i.e. whether they are at the itemtype level or at the attribute level, they can not vary from instance to instance; they are same of all instances of the itemtype.

- itemtype-level custom-properties:
- attribute-level custom-properties:

### How do you create a catalog aware item?

To explicitly make an item type to be catalog aware, follow below steps:

- Mark your item as catalog item type, using custom property catalog ItemType.
- Choose attribute from item, which will qualify the  catalog associated using custom property catalog Version Attribute Qualifier.
- Since each item in a catalog must be unique, we need to choose an attribute from item which is unique. This can be done using custom property unique Key Attribute Qualifier.

```xml

<custom-properties>
     <property name="catalogItemType"><value>java.lang.Boolean.TRUE</value></property>
     <property name="catalogVersionAttributeQualifier"><value>"catalogVersion"</value></property>
     <property name="uniqueKeyAttributeQualifier"><value>"code"</value></property>
 </custom-properties>

```

### Difference between Collection and relation?

Collections and Relations are used to store : one to many or many to many related data.

Collection Type: Collection Types are comma separated Pks that gets stored in the database. For example, when we need to store the keywords applicable for the product we can use collection type.

Relation: Relation is used when we need to model the association between objects. For example, if we are modelling an association between a product and its variant, we should use a relation.

### When do you chose Collection over Relation?

The advantage with collections is, 

- traversing and fetching many side, is fast if the list is small, because we do not need to JOIN different tables, and we know that JOINing different tables slows the database transaction.
- Collections are better option if you have small list of data to be stored.

### When do you chose Relation over Collection?

The disadvantage of using Collections is: It tends to truncate the list of comma separated PK’s of many side, resulting in data loss.

Advantage of using Relations is, you do not loose data due to truncation and database limitations.

Relations are used when we have a long list at many side.

Also, Many-to-many relations are only possible in relations.

### How to create a One-to-One relation in Hybris?

Hybris doesn't allow a one-one Relation. If you need this case you can simply model just an attribute to your object. Than you have a one-one relation.

### How are relations created in Hybris?

There are two possible relation we can created using `relationtype`.

- One-to-many (1:n) or Many-to-one (n:1) e.g. User address
- many-to-many (m:n) e.g. product and categories

Collection type can be List, Set or Collection.

Hybris does not create a separate deployment table for a one-to-many relation. For 1:n relationships, the relationship saved into the target table and it contains the reference of source.

However, it is mandatory to specify a deployment table for m:n (many-to-many) relation otherwise Platform will not build.

### What does order=true means in Hybris?

1. one to many

When we use ordered=true, an attribute *POS is added automatically. 

```xml
<relation code="house2rooms" localized="false" generate="true" autocreate="true">
    <sourceElement type="House" qualifier="house" cardinality="one">
       <modifiers read="true" write="true" optional="true" search="true"/>
    </sourceElement>
    <targetElement type="Room" qualifier="rooms" cardinality="many" ordered="true" collectiontype="set">
       <modifiers read="true" write="true" optional="true" search="true" partof="true" />
    </targetElement>
 </relation>
```

Based on the definition given above, we can import the value of housePOS as follows.

If we don't specify the value of housePOS, it will be inserted as 0 

```
INSERT_UPDATE house;code[unique=true]
 ;house_arvind
 
 INSERT_UPDATE Room;code[unique=true];house(code);housePOS
 ;room1;house_arvind;0
 ;room2;house_arvind;1
 ;room3;house_arvind;2
```
FS Query 

`SELECT {code},{housePOS} FROM {Room}`

2. many to many

In the db table there are columns for `SequenceNumber` and `RSequenceNumber`.

These columns are used as the order by for a query that gets the children getCategories() or parents getSupercategories()

You can update the values directly via impex or dragging and dropping from within the admin tooling (backoffice, hmc, productcockpit etc.)


### What is a Condition Query in items.xml?

The condition query is written in FlexibleSearch and must be valid in the context of a given relation query.

The property holds a string that is later added to the 'where' part of the select query generated for a one-to-many or many-to-one relation.

```xml
<relation code="User2Addresses" generate="true" localized="false" autocreate="true">
    <sourceElement type="User" cardinality="one" qualifier="owner">
        <modifiers read="true" write="true" search="true" optional="true" initial="false"/>
    </sourceElement>
    <targetElement type="Address" cardinality="many" qualifier="addresses">
        <modifiers read="true" write="true" search="true" optional="true" partof="true"/>
        <custom-properties>
            <property name="condition.query">
                <value>"{original} is null"</value>
            </property>
        </custom-properties>
    </targetElement>
</relation>
```

### What does Ordering Attribute do in items.xml?

By defining ordering.attribute, it is possible to specify which attribute will be used to order the many-side items when retrieving from the database

```xml
<relation code="AbstractOrder2AbstractOrderEntry" localized="false" generate="true" autocreate="true">
	<sourceElement type="AbstractOrder" qualifier="order" cardinality="one">
		<modifiers read="true" write="true" search="true" optional="true" />
				<custom-properties>
					<property name="ordering.attribute">
						<value>"entryNumber"</value>
					</property>
				</custom-properties>
			</sourceElement>
<targetElement type="AbstractOrderEntry" qualifier="entries" cardinality="many" collectiontype="list" ordered="false" >
		<modifiers read="true" write="true" search="true" optional="true" partof="true" />
</targetElement>
		</relation>
```

### what is the use of readOnlyForUI/hiddenForUI in items.xml?

Backoffice would allow to display all attributes of any type. 

There are some special attributes that definitely should not be visible in the UI or should at least be readonly in the UI (no matter what the access rights they have)

```xml
<property name="readOnlyForUI">
    <value>Boolean.TRUE</value>
</property>
<property name="hiddenForUI">
    <value>Boolean.TRUE</value>
</property>
```

### What are static and dynamic enums?

static enum - no enumeration values can be created at runtime; they can only be created at initialization or update via items.xml

dynamic enum - new enumeration values can be created at runtime

All enums are stored in `enumerationvalues` table. 

`select * from {enumerationvalue}`

e.g. 

```xml
 <enumtype code="MyEnum" dynamic="true" generate="true" autocreate="false" >
        <value code="new1"/>
        <value code="new2"/>
 </enumtype>
```

Through impex you can modify dynamic enums as below.

```
INSERT MyEnum;code[unique=true]
 ;new3

SELECT * FROM {enumerationvalue} WHERE {code}='new3'
```

### What is PartOf?

A PartOf relationship between two classes extends an aggregation relationship by ensuring that the lifecycle of the dependent object (part) is bound to the lifecycle of the parent object. When you delete the parent object, all instances of its attribute types that are marked as PartOf will be cascade-deleted. It could be said that the child item cannot live without its parent item. 

For example, an OrderEntry record(AbstractOrderEntry) is part of its parent Order(AbstractOrder), and older entries will never be shared between multiple orders. 


```xml
<relation code="AbstractOrder2AbstractOrderEntry" localized="false" generate="true" autocreate="true">
    <sourceElement type="AbstractOrder" qualifier="order" cardinality="one">
        <modifiers read="true" write="true" search="true" optional="true"/>
        <custom-properties>
            <property name="ordering.attribute">
                <value>"entryNumber"</value>
            </property>
        </custom-properties>
    </sourceElement>
    <targetElement type="AbstractOrderEntry" qualifier="entries" cardinality="many" collectiontype="list"
                    ordered="false">
        <modifiers read="true" write="true" search="true" optional="true" partof="true"/>
    </targetElement>
</relation>
```

### How to create a localized Custom attribute in items.xml?

Localize Item properties :

You need to create own maptype and then use it in attribute definition.

```xml
<maptype code="localized:MyType"
                      argumenttype="Language"
                      returntype="MyType"
                      autocreate="true"
                      generate="false"/>

<attribute qualifier="myAttr" type="localized:MyType">
                         ....
 </attribute>
```

Localize item and attribute names:

- stored in `<extension-name>-locales_XY.properties`
    - `XY` - ISO code   

```properties
type.{typename}.name=value
type.{typename}.description=value
type.{typename}.{attributename}.name=value
type.{typename}.{attributename}.description=value
type.{enumcode}.{valuecode}.name=value
```

### What are some of the Best Practice Guidelines in items.xml?

- Define a deployment table for all Item Types extending GenericItem. Except Abstract item type where you want concrete sub-types stored in separate tables
- Do not define a deployment table for any Item Types that already have a deployment inherited from a super type.
- Deployment type codes must be greater than 10000.
- Do not define a Jalo class if you are adding to an existing type.
- Define a deployment table for all many-to-many relations.
- Do not define attributes with a persistence type of cmp.
- Ensure mandatory fields (where optional='false') have either initial set to true or a default value defined.
- Ensure immutable fields (where write='false') have initial set to true.
- Define Boolean attributes as mandatory.
- Use an attribute persistence type of dynamic, instead of jalo.
- Start Item type Names (including EnumTypes and Relations) with an uppercase letter.
- Do not start Item type names with Generated
- Start item type attribute names with a lowercase letter
- Start qualifier names in relations with a lowercase letter.
- Avoid setting ordered='true' on any side of a relation that has cardinality='many' unless absolutely necessary.
- Use collectiontype='set' on any side of a relation that has cardinality='many' where items must not appear in that relation multiple times.
- Mark CatalogVersion attributes as unique for Catalog aware types.
- Ensure unique attributes match the catalog unique attribute key, uniqueKeyAttributeQualifier.
- Define database indexes for the unique attributes of type.
- Ensure catalog aware types are not part of a one-to-many relation when the many side is not a catalog aware type.
- Ensure all types have unique attributes.
- Do not use unoptimized attributes.
- Choose whether to add to an existing item type or create a new sub-type carefully.
    - If: Adding an attribute for all instances, Then: Add to existing type 
    - If: Adding an attribute for specific instances, Then: Create a new sub-type
    - If: Re-declaring an existing attribute e.g. change mandatory to optional, Then: Create a new sub-type
- Describe the purpose and intended use of an attribute in a description tag.
- Remove unnecessary indexes.
- Localize any attributes that need to be displayed in a different language.
- Avoid using collections and use relations instead.
- Define types in order of inheritance.
- Type names must be unique.
- Do not change attribute definitions unless absolutely necessary.
- Do not change deployment code or table names unless absolutely necessary.
- Do not change a type declaration unless absolutely necessary.
- Define a database index on the lookup value of reference types.

### How to create indexes?

We know that indexing a database column brings up a very good performance while fetching the records from Database especially when data set is huge.

Whenever we create an index on a column in a table, it creates another data structure which holds the column value , and pointer to the record it relates to. So, using indexing speeds up the searching for a matching column within the records.

```xml
<indexes>
    <index name="Region_Country">
        <key attribute="country" lower="true"/>
    </index>
</indexes>

...

<indexes>
    <index name="codeVersionActiveIDX" unique="true">
        <key attribute="code"/>
        <key attribute="version"/>
        <key attribute="active"/>
    </index>
</indexes>

...

<indexes>
    <index name="codeIDX" unique="false">
        <key attribute="code"/>
    </index>
    <index name="versionIDX" unique="false">
        <key attribute="catalogVersion"/>
    </index>
    <index name="codeVersionIDX" unique="true">
        <key attribute="code"/>
        <key attribute="catalogVersion"/>
    </index>
</indexes>

```

Index management is cost intensive. As a rule of thumb, indexes should only be added for attributes used in flexible searches.

## Flexible Search 

### What is Flexible Search Query?

FlexibleSearch is a powerful retrieval language built into SAP Commerce Cloud. It enables searching for SAP Commerce Cloud types and items using an SQL-based syntax. 

The execution of a FlexibleSearch statement takes place in two phases: 

- pre-parsing into an SQL-compliant statement and 
- running that statement on the database. 

### What are mandatory clause in FSQ?

The basic syntax of a FlexibleSearch query looks like this:

`SELECT <selects> FROM <types> ( WHERE <conditions> )? ( ORDER BY <order> )?`

selects and types are mandatory clauses. conditions and order by are optional clauses.

### How to fetch data through paging in FSQ?

FlexibleSearchQuery#setCount - total results to be returned
FlexibleSearchQuery#setNeedTotal - total  
FlexibleSearchQuery#setStart - sets the start for the result

Internally the results are fetched and then the results are paged according to count. Each paged queries are real time, so if there is table changes, it would reflect on the current query result.

### How to disable cache in FSQ?

FlexibleSearchQuery#setDisableCaching

The FlexibleSearchService allows you to easily skip cache on demand by setting it as true. 

### What is Personalization/ Search Restriction in Hybris?

A restriction in Hybris is a set of rules applied to flexible searches in order to limit the search results based on certain specific conditions.

### How to create a SearchRestriction ?

To create a `SearchRestriction`, 

- code: Unique id code of the restriction.
- name: Name of the restriction.
- principal: User/user group to whom the restriction will be applied.
- restrictedType: Item type to which the restriction will be applied on.
- active: true means the restriction is enabled, false is disabled.
- query: conditions to be added to the WHERE clause of the flexible search.


```
INSERT_UPDATE SearchRestriction;code[unique=true];name[lang=de];name[lang=en];query;principal(UID);restrictedType(code);active;generate
;Frontend_Navigationelement;Navigation;Navigation;{active} IS TRUE;test_user;Language;true;true
```

### How Search restriction is disabled for a user?

- Restrictions do not apply to admin users (user group = adminusergroup). This executed in local view by using sessionService.executeInLocalView

```java

sessionService.executeInLocalView(new SessionExecutionBody()  
{                                                                                         
	@Override                                                                              
	public List<ProductModel> execute()                                                    
	{                                                                                      
		userService.setCurrentUser(userService.getAdminUser());                             
		return productService.getProductsForCategory(getSomeCategory());                        
	}                                                                                      
	});                                                                                       
}         

...

sessionService.executeInLocalView(new SessionExecutionBody() {
    @Override
    public void executeWithoutResult() {
        // do stuff
    }
}, userService.getAdminUser());
```

- it can also be disabled by searchRestrictionService

```java
searchRestrictionService.disableSearchRestrictions();
```

## ImpEx

### What is ImpEx?

ImpEx is an out-of-the-box import-export framework. It is integrated text-based import/export extension . You can create, update, export, and remove items in Hybris Platform.

### What are the types of Header Mode ?

- INSERT - throws exception if the item already exist
- UPDATE - throws exception if item does not exist
- INSERT_UPDATE - update if not then insert
- REMOVE - if not logs only warning

### How to import using parent type?

Optionally, each line can specify a subtype of the type specified in the header at the first column.

```
INSERT User;uid[unique=true]
Customer;SampleCustomer
Employee;SampleEmployee
```

### Where colon is used in ImpEx?

If you specify an item expression using several attributes at a header line, you have to separate them with a comma. The values for such an expression at a value line are separated by a colon.

```
INSERT Product;code;unit(code);catalogVersion(catalog(id),version)
;testCode;pieces;clothescatalog:Staged
```

### How will you import a subtype of Item for a column ?

you cannot directly reference an attribute specified for a subtype of Item, we need to use the `subtype.subtype_attribute` as reference

```
INSERT Address;firstname;owner(Principal.uid)
;Hans;admin
```

### How do you localize an attribute?

Localized attributes are described by multiple columns using the lang modifier, each one containing the ISOcode or PK for a single language.

```
INSERT Product;code[unique=true];name[lang=en];name[lang=de]
;myProduct1;myProduct1's localized name;lokalisierter Name von myProduct1
;myProduct2;myProduct2's localized name;lokalisierter Name von myProduct2
```

### What is document id in Impex?

ImpEx provides a special header attribute marked with the prefix `&` to specify a virtual ID called Document ID.

Each imported item line can optionally define such a Document ID, which allows other lines to reference it later using the same attribute qualifier (with the & prefix).

Especially when importing partOf item values, you must reference these items by means other than the usual unique column technique because partOf items do not always provide a unique key but only hold their enclosing parent as a foreign key.

e.g  Customer.addresses 



When using ImpEx, both customer and address have to be imported in separate lines.

```
INSERT Customer;uid[unique=true];...;defaultPaymentAddress(&addrID);...
;andi;...;addr1;...
						
INSERT Address;&addrID;owner(Customer.uid);...
;addr1;andi;...
```

### What are special attributes in ImpEx?

Sometimes you need to add data to an item that is not covered by an attribute, for example the media data. In these cases, the syntax of ImpEx provides a special kind of attribute definition called special attributes that do not have a mapping to a real attribute of the configured type.

They are marked with the @ symbol and always need the translator modifier, because there is no default implementation. Furthermore the specified translator has to be a realization of the SpecialValueTranslator interface.

```
INSERT_UPDATE Media;code[unique=true];@media[translator=de.hybris.platform.impex.jalo.media.MediaDataTranslator]
;myMedia;file://c:/myMedia.txt
```

### What are Alternative Pattern in Impex?

An Alternative Pattern specifies more than one attribute and makes the ImpEx extension determine the matching type instance from all the specified attributes. Alternative Patterns are processed from left to right.

```
INSERT Address;firstname;owner(Principal.uid|AbstractOrder.code)
;Hans;admin
;Klaus;O-K2006-C0000-001
```

### What does [batchmode=true] mean?

In UPDATE or REMOVE mode, the batch mode allows modifying more than one item that matches for a combination of all unique attributes of a value line.

### What does [cacheUnique=true] mean?

If this option is enabled, the CachingExistingItemResolver is used for existing item resolving (in case of update or remove mode) which caches by the combination of unique keys already resolved items. So when processing a value line first, it is tried to find the related item by searching the cache using the unique keys. The usage is only meaningful if an item has to be processed more than one time within a header scope. The maximum size of the cache is not restricted at the moment.

### What does [processor=de.hybris.platform.impex.jalo.imp.DefaultImportProcessor] mean?

A Processor is passed a value line as input, and it creates or updates an item in SAP Commerce as its output.

Unlike a Translator, which handles a certain column of a value line, a Processor handles an entire value line. It contains all business logic to transform a value line into values for attributes of an item.

### What are header related modifier?

- batchmode
- cacheUnique
- processor
- impex.legacy.mode 

### What are attribute related modifiers?

- alias 
    - for changing export header name
- allownull 
    - allows null even for mandatory column e.g catalogversion of Media
- cellDecorator 
- collection-delimiter=comma(,)
- dateformat
    - e.g. dateformat=dd-MM-yyyy, dateformat='yyyy-MM-dd''T''HH:mm:ss.SSSZ'
- default
- forceWrite=false
    - If set, it tries to write into read-only columns. If not set, writing of read-only attributes is not to be tried.
- ignoreKeyCase=false
    - If set, the case of the text that specifies attributes of items for resolving is ignored
- ignorenull=false
    - skips or adds null value in collection type
- key2value-delimiter=->
- lang
- map-delimiter=;
- mode
    - mode=append:Collection value are added to the existing resolved Collection
    - mode=remove:  Collection value are removed from the resolved Collection
    - mode=replace: Replaces the existing Collection with the specified values
- numberformat=NumberFormat.getNumberInstance( impexReader.getLocale() )
    - German : numberformat=#.###,##
    - English : numberformat=#,###.##
- path-delimiter=:
    - Defines the delimiter to separate values for attributes using an item expression, such as catalog versions,
- pos
    - Specifies the column position where the values for this attribute can be found within the following value lines. 
- translator
    - A Translator class resolves a column of a value line into an attribute value for the item
    - AbstractValueTranslator or SpecialValueTranslator
    - each Type has a predefined default Translator.
- unique=false
- virtual
    - If set, a related column within the following value lines is expected. This has to be used in conjunction with default modifier.

### What is `<ignore>` in impex?

The value <ignore> makes the ImpEx extension skip the entry and leave the item value as it is. 

### What lines are ignored in impex?

- commented lines (starting with #)
- Blank lines (;;;;;;;;;)

### What are macros in impex?

The ImpEx extension allows you to define macros so that you do not have to type repeating strings into your CSV files, and you can keep your CSV files more manageable.

Macro definitions start with the dollar symbol ($), and they are referenced by $macroname

```
$catalog=catalog(id)
$catalogVersion=catalogVersion($catalog,version)
						
INSERT Product;code;$catalogVersion

...

INSERT Product;code;catalogVersion(catalog(id),version)
```

### What are different Validation Modes?

- Import Strict - Mode for import where all checks relevant for import are enabled. This is the preferred one for an import.
- Import Relaxed - Mode for import where several checks are disabled. Use this mode for modifying data not allowed by the data model like writing non-writable attributes
- Export Strict (Re)Import - Mode for export where all checks relevant for a re-import of the exported data are enabled. This is the preferred mode for export if you want to re-import the data as in migration case.
- Export Relaxed (Re)Import - Mode for export where several checks relevant for a re-import of the exported data are disabled.
- Export Only - Mode for export where the exported data are not designated for a re-import.

### How to write scripts in ImpEx?

You can use Beanshell, Groovy, or Javascript as scripting languages within ImpEx. In addition, ImpEx has special control markers that determine simple actions such as beforeEach, afterEach, if.

```
oldway

INSERT_UPDATE Title;code[unique=true]
					#% beforeEach: line.clear();
					;foo

new way
INSERT_UPDATE Title;code[unique=true]
					#%groovy% beforeEach: line.clear();
					;foo
                    
```

### What class are imported by default?

```java
import de.hybris.platform.core.*
import de.hybris.platform.core.model.user.* 
import de.hybris.platform.core.HybrisEnumValue
import de.hybris.platform.util.* 
import de.hybris.platform.impex.jalo.* 
import de.hybris.platform.jalo.*
import de.hybris.platform.jalo.c2l.Currency
import de.hybris.platform.jalo.c2l.* 
import de.hybris.platform.jalo.user.*
import de.hybris.platform.jalo.flexiblesearch.* 
import de.hybris.platform.jalo.product.ProductManager
```

### How user access are given by Impex?

```
$START_USERRIGHTS

Type;UID;MemberOfGroups;Password;Target;read;change;create;delete;change_perm
UserGroup;impexgroup;employeegroup;
;;;;Product;+;+;+;+;-
Customer;impex-demo;impexgroup;1234;

$END_USERRIGHTS
```

## Product Content and Catalog Management

### What is a catalog in Hybris?

The catalog is not limited to just list of items, but how they are arranged also.

hybris has two catalogs 
- Product Catalog responsible for arrangement of the product hierarchy and 
- the other as Content Catalog responsible for the layout (or the e-ambience) of the website.

### What is Synchronization ?

Hybris Catalog has two versions - Offline (Staged) and Online. The business users work on the Staged version and push it (to) Online once the item under work (product or content) is suitable to go live. They do this via a process called Synchronization.

This process picks the items from Staged version, checks for some rules (which indicate that the item is ready to go live), and creates (or updates if already created) a copy of the item with Online as the version. Hybris system understands that only Online needs to go live, picks the item, and displays it on the website (storefront).

### What are Catalog Aware / Catalog unaware Items?

Now, there are some entities which should have two copies in the system, so that Business users (like content managers or product managers) can make appropriate changes to make it look suitable on the website before it actually appears there. E.g. - Product, Images etc.

But there are certain items which need not to have two copies of them, E.g. - Price, Stock etc. because they would be same for both copies of the item (Product in this case) and creating another copy would be an overhead on the system.

Therefore, the items that (should) take part in the synchronization process like Product, CMS Pages, Components etc. are called Catalog Aware.

The items which do (or should) not take part in synchronization process are called Catalog Unaware

### What is Staged / Online Catalog?

Staged Catalog — this catalog version is a test catalog version. Modifications are made first in the staged catalog. When changes/modifications are tested/approved and you are satisfied then you publish them to be available online to your users by synchronizing staged catalog to the online catalog.

Online Catalog — this catalog version is the one which will be used to display on the storefront(the active one).

### How to implement a Multicountry solution for a Site?

Product: 

Usually the product data are the same , in some rare cases customer has had requirements that some properties are not visible in some countries but this can be solved in classification

Stock and Price:

The prices and availability are usually the things that differ. For this i recommend the multicountry package where you can specify different availabilities for products. Availability groups and userprice groups are configured into basestore. 

Content:

The different locations might want to have different cms content and this is supported by multicountry package.

Summary,

- One tenant
- One product catalog with each product having different price per country and availability group to correct basestore
- Each country a basestore with usergroups and availability group
- Each country a site with own cms catalog (local catalogs)
- A global catalog with the content that is shown for all sites.

Reference : 

- [Multi Country](https://answers.sap.com/questions/12768878/multi-country-hybris-architecture.html)
- [Multi Country Package](https://help.sap.com/viewer/9d346683b0084da2938be8a285c0c27a/1905/en-US/9b6a43313c3e4587b664370cb94391a5.html)
- [Multi Country Support](https://help.sap.com/viewer/9d346683b0084da2938be8a285c0c27a/2005/en-US/cb34c49145d9478b861f7bbec236a476.html?q=multicountry)

### What is Classification attributes/ Category features?

Through classification you can flexibly allocate category features that may change frequently. Defining and modifying them is easy because they are managed independently of the product type.

### What is Classification System?

Available category features can be organized independently from product catalog structures in separate classification systems, where they can be structured into classifying categories. 

Using classifying categories, you can assign a category feature to one or multiple product categories used in catalogs. All category features of the classifying categories are available within all products included in the assigned product categories. 

### How do you determine if a product attribute should use Classification System instead of the Type System ?

- The product attribute is not used in the business logic
- The product attribute is used only in a limited or select number of products
- The product attribute’s longevity is unknown, and it may become unnecessary in a matter of a few months or less
- The product attribute will be added dynamically at run-time
- The product attribute is a display property and does not have any reporting requirements

### Explain how to create a classification System?

- Create a classification system (stored in catalogs table)
- Create a classification system version (stored in catalogversions table)
- Create a ClassificationClass ( stored in categories table)
- Create a classification attribute (stored in classificationattributes table)
- Create a ClassAttributeAssignment which is the metadata for the attribute (stored in classattributeassignments table)

When you assign the classification class to the product via its super categories hierarchy or directly, the attributes are available to the product as product features. When you provide a value to the feature it internally creates an instance of ProductFeature (stored in productfeatures table).

So a classification attribute it completely different than the core attribute defined in items.xml.

On display of classification attribute in product cockpit there is a separate renderer for it which renders the classification attributes in the attributes tab of editor area. You can render the attributes in other tabs as well using the fully qualified classification attribute qualifier.

### how to add the classification attributes through impex?

- You need a ClassificationSystem (equivalent for Catalog for categories and product)
- You need a ClassificationSystemversion (equivalent for CatalogVersion for category and product)
- Your product has to be in a category.
- This category needs a super category of type ClassificationClass.
- You need a ClassificationAttribute.
- You need a ClassAttributeAssignment which assigns your ClassificationAttribute to a ClassificationClass.
- (Optional) You can create ClassificationAttributeUnits to define the unit of an attribute.
- (Optional) You can create ClassificationAttributeValues to define possible values for a ClassificationAttribute.
- When these preconditions are met, you can assign values to a product using your impex script.

```
INSERT_UPDATE ClassificationClass; code[unique = true]; name[lang = $lang]; $classCatalogVersion; allowedPrincipals(uid)[default = 'customergroup']
 ; toyAttributes         ; Toy details
 
 INSERT_UPDATE ClassificationAttribute; code[unique = true]; name[lang = $lang]; $classSystemVersion;
 ; toyType           ; Toy Type
 
 INSERT_UPDATE CategoryCategoryRelation; $categories; $supercategories
 ; Toys                        ; toyAttributes
 
 INSERT_UPDATE ClassAttributeAssignment; $class; $attribute; position; specialSymbols[default = false]; frontendVisible[default = true]; attributeType(code[default = string]); multiValued[default = false]; attributeValues(code, $classSystemVersion); range[default = false]; $unit; listable[default = true]; localized[default = true]
 ; toyAttributes         ; toyType           ; 1  ;

 $productCatalog = MyProductCatalog
 $catalogVersion = catalogversion(catalog(id[default = $productCatalog]), version[default = 'Staged'])[unique = true, default = $productCatalog:Staged]
 $clAttrModifiers = system = 'MyClassification', version = '1.0', translator = de.hybris.platform.catalog.jalo.classification.impex.ClassificationAttributeTranslator, lang = en
 
 $toyType = @toyType[$clAttrModifiers];
 INSERT_UPDATE Product; code[unique = true]; $toyType
 ; ABC123 ; Remote control
```

### How to use flexible search for classification Attribute?

```SQL
 SELECT 
     {p.code}, {cat.id}, {cv.version}, {pf.qualifier}, {pf.stringValue}
 FROM 
     {ProductFeature as pf 
     JOIN Product as p ON {p.pk}={pf.product} 
     JOIN CatalogVersion as cv ON {cv.pk}={p.catalogVersion}
     JOIN Catalog as cat ON {cat.pk}={cv.catalog}} 
 WHERE 
     {cat.id} LIKE 'Default' 
     AND {cv.version} LIKE 'Staged' 
     AND {p.code} LIKE 'PRODUCT_CODE' 
```

Reference : 

- [Classification Feature Value API](https://help.sap.com/viewer/0165f478fae948e6a0f95344929e7db9/6.7.0.0/en-US/8b7a777486691014823afa6e0ed7bb61.html)
- [Classification System API](https://help.sap.com/viewer/0165f478fae948e6a0f95344929e7db9/6.7.0.0/en-US/8b7ad17c86691014aa0ee2d228c56dd1.html)

### 

The single catalog that is visible to your customers is called online, and the catalogs that are not visible to customers are staged. A standard synchronizing operation is the act of updating the online catalog with at least one of the staged catalogs. If you synchronize a staged catalog, the online catalog will then contain all values from that staged catalog.


## CronJobs

### How to create a CronJob?

- Create CronJob that hold all the inputs to be passed to the Job (it’s optional).
- Create Our JobPerformable (extends AbstractJobPerformable), then implement the logic to be done inside it.
- Register the JobPerformable as a Spring bean with id.
- Create a new instance of ServicelayerJob, then inject the bean id in it.
- Create an instance CronJob model with this signle run configuration (inputs).
- Then Create a Trigger if it’s needed to schedule the CronJob in time.

### How to create an abortable job?

- Override `JobPerfomable#isAbortable()` method.
- In the `perform()` method, regularly check for abortion requests using `clearAbortRequestedIfNeeded()`

### What is a CompositeCronJob ?

In Hybris, a composite cronJob is a composition of multiple cronJobs used when we need to run a bundle of cronJobs successively.  It is often referred as an ordered all or nothing execution, e.g. MediaImportComposite composed of MediaImportCronJob and MediaConversionCronJob.

```
INSERT_UPDATE CompositeEntry  ;code[unique=true]  ;executableCronJob(code)
                              ;totoCronJobEntry   ;totoCronJob
                              ;fooCronJobEntry    ;fooCronJob
                            
INSERT_UPDATE CompositeCronJob  ;code[unique=true]    ;job(code)              ;compositeEntries(code)           ;sessionUser(uid)[default=admin]  ;sessionLanguage(isocode)[default=en]
                                ;helloCompositeCronJob;compositeJobPerformable;totoCronJobEntry, fooCronJobEntry
```

### What are the events triggered in CronJob?

- BeforeCronJobStartEvent 
- AfterCronJobFinishedEvent 

Note that both events are published to the local Platform instance only and aren't sent to other cluster members.

### What is the default polling time for Trigger?

It is 30 seconds.

`cronjob.trigger.interval=30`

The regular check for triggers is executed for the first time when the hybris Commerce Suite is started or the cron job is created. If it is overdue the job is triggered.

In some situations you may not want to run cron jobs right after the hybris Commerce Suite int his case you can use the property maxAcceptableDelay. e.g Server start.

`trigger.maxAcceptableDelay=300` (300 seconds)

```
UPDATE Trigger[batchmode=true];itemtype(code)[unique=true];maxAcceptableDelay
;Trigger;1800
```

### What happens if the cronjobs runs until next trigger?

Yes, there might be 2 instances running concurrently. 

Cronjob implementation may address it internally and subsequent instance skips execution if it detects already running instance.


### How do you make cronjob run in backoffice node?

- Assign the `CronJob.nodeGroup = backoffice`
- Assign the `cluster.node.groups = backoffice` for the backoffice node 

## WCMS

### What are the components in WCMS?

- Template : Holds the basis structure of pages.
- Page : Is somehow an instance of the template that inherit the same structure of the template.
- CMS Components : Elements to be placed in different sections (ContentSlot) of the page/template.

### What is a Template in WCMS?

- The template defines the shape and the structure of the pages, it’s composed of many sections (PageSlots).
- Every template is a Model and a JSP :
    - Model : is an instance of PageTemplateModel that hold different information about the template : uid, name, catalogVersion, contentSlots, and a frontendTemplateName which is the reference to the JSP file.
    - JSP : the structure of the template is defined by this JSP file.

### How to create a Template?

Template uid : CustomPageTemplate.
Template name : Custom Page Template.
Frontend template name : custom/customTemplatePage.
ContentSlots : HeaderSection, NavBarSection, SectionA, SectionB, SectionC, FooterSection.

```
$contentCV=...

INSERT_UPDATE PageTemplate	;$contentCV[unique=true];uid[unique=true]		;name					;frontendTemplateName		;active[default=true]
							;						;CustomPageTemplate		;Custom Page Template	;custom/customTemplatePage;

$contentCV=...

INSERT_UPDATE ContentSlotName	;name[unique=true];template(uid,$contentCV)[unique=true][default='CustomPageTemplate']
								;HeaderSection
								;NavBarSection
								;SectionA
								;SectionB
								;SectionC
								;FooterSection
```

Then create a new JSP file, we will call it customTemplatePage.jsp :

- The content of the file should be encapsulated inside <template:page> tag.
- Each section is a <cms:pageSlot> tag with different position id.
- Sections encapsulate the <cms:component> tag where the CMS Component will be rendered later on.

```jsp
<!-- custom/customTemplatePage.jsp -->

<%@ taglib prefix="template" tagdir="/WEB-INF/tags/desktop/template" %>
<%@ taglib prefix="cms" uri="http://hybris.com/tld/cmstags" %>

<template:page pageTitle="${pageTitle}">

	<div class="row">
		<cms:pageSlot position="HeaderSection" var="feature">
			<cms:component component="${feature}" element="div" class="col-12"/>
		</cms:pageSlot>
	</div>
    
    ...

</template:page>
```

### What is a Page in WCMS?

Pages are considered as instances of the template, so all the pages will inherits the same structure of the template but will contains different CMS Components hence each page will have different look and feel.

Pages may share common CMS Components (Header, NavBar And Footer), in this case it may be preferable to add them directly to the template instead of adding them separated to each page.

```
$contentCV=...

INSERT_UPDATE CustomPage;$contentCV[unique=true];uid[unique=true]	;name			;masterTemplate(uid,$contentCV);defaultPage[default='false'];approvalStatus(code)[default='approved'];
						;						;my-custom-page		;My Custom Page	;CustomPageTemplate;
```

To create a new page, first create a new item type that extends AbstractPageModel.

### What are the OOB Pages?

- Category
- Content
- Email
- Product
- etc

### What are components in Hybris?

Components are elements (blocks) that you want to display on the Webpage. Components in Hybris are allocated to Content slots in the Pages.

```
$contentCV=...

INSERT_UPDATE CMSParagraphComponent	;$contentCV[unique=true];uid[unique=true]	;content[lang=en]
									;						;MyCustomParagraph	;"Hello Word paragraph"
```

Then attach this CMSParagraphComponent to our CustomPage like this:

```
$contentCV=...

#Create a ContentSlot and Attach the CMS Component to it
INSERT_UPDATE ContentSlot	; $contentCV[unique=true]    ; uid[unique=true]			; name 							; active; cmsComponents(uid)
                            ;                            ; SectionASlot-CustomPage 	; SectionA Slot for CustomPage 	; true	; MyCustomParagraph

#Attach the ContentSlot to the Page via ContentSlotForPage
INSERT_UPDATE ContentSlotForPage; $contentCV[unique=true]	; uid[unique=true]      ; position[unique=true]; page(uid,$contentCV)[unique=true][default='my-custom-page']; contentSlot(uid,$contentCV)[unique=true]
								;                      		; SectionA-CustomPage	; SectionA  ;																		; SectionASlot-CustomPage
```

### What is the difference between ContentSlotForPage and ContentSlotForTemplate?

- ContentSlotForPage: is the link between a contentSlot and a Page
- ContentSlotForTemplate: is the link between a contentSlot and a PageTemplate

The technical difference is that both of the `ContentSlotForPageModel` and `ContentSlotForTemplateModel` has an attribute called contentSlot (of Type ContentSlotModel), however for the `ContentSlotForPageModel` there is another attribute called `page` (of type AbstractPageModel) and for the `ContentSlotForTemplateModel` the attribute called `pageTemplate` (and of type PageTemplateModel).

```

INSERT_UPDATE ContentSlotForTemplate;$contentCV[unique=true];uid[unique=true];position[unique=true];pageTemplate(uid,$contentCV)[unique=true][default='ProductDetailsPageTemplate'];contentSlot(uid,$contentCV)[unique=true];allowOverwrite
;;SiteLogo-ProductDetails;SiteLogo;;SiteLogoSlot;true

```

### How to Create a custom CMS component in Hybris?

A cms component consists of three elements interact with one another : Model, JSP and Controller.

- Model : identify the component with a uid, name, catalog version and also holds the inputs to be sent to the JSP.
- JSP : is the template of the component, describe the look and feel of it.
- Controller : is the mediator between the Model and the JSP, inputs are sent from the Model to the JSP with the help of it.

### Is it cumpulsory to create a controller in order to add new component?

Basically the controller is not compulsory for new custom component, and what really happens when you create a new component without its Controller
the component will be based on a default controller (`com.wonder.storefront.controllers.cms.DefaultCMSComponentController`) who is going to fill all components attributes on model
and you can get access to them from jsp by ${component.customAttribute}


