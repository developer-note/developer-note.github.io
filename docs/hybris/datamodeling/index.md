---
layout: default
title: Data Modeling
parent: Hybris
has_children: false
---

# Data Modeling

{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

# Type and Item

- If we compare with Java, `Type` is like a `Class` and `Item` is like `Object` 
- `Item` is an instance of `Type`

## Types in Hybris

- `AtomicType`
    - primitive and Java 
- `CollectionType`
    - collection of target type
    - used as an attribute of a ComposedType
    - opposite side is not aware of the relationship
    - stored as comma separate values in DB
    - lower performance and overflow issues possible
- `MapType`
- `ComposedType`
    - composed objects of other Hybris type
- `EnumerationMetaType`
    - `ComposedType` that describes enumerations
- `RelationType`
    - `ComposedType` that describes binary relation 
    between items
    - One2Many and Many2Many
    - both Types are aware of each other 
    - better to use relations than `CollectionType`

```xml
<items xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="items.xsd">
    <atomictypes>
        <atomictype class="java.lang.Object" autocreate="true" generate="false"/>
        <atomictype class="java.lang.Number" extends="java.lang.Object" autocreate="true" generate="false"/>
        <atomictype class="java.lang.Integer" extends="java.lang.Number" autocreate="true" generate="false"/>
        
</atomictypes>

    <collectiontypes>
        <collectiontype code="ExampleCollection" elementtype="Item" autocreate="true" generate="false"/>
        <collectiontype code="ItemCollection" elementtype="Item" autocreate="true" generate="false"/>
        <collectiontype code="StringCollection" elementtype="java.lang.String" autocreate="true" generate="false"/>
        <collectiontype code="ObjectCollection" elementtype="java.lang.Object" autocreate="true" generate="false"/>
    </collectiontypes>

    <enumtypes>

        <enumtype code="TestEnum">
            <value code="testValue1"/>
            <value code="testValue2"/>
            <value code="testValue3"/>
            <value code="testValue4"/>
        </enumtype>

        <enumtype code="EncodingEnum" autocreate="true" generate="true" dynamic="true"/>

        <!-- order -->
        <enumtype code="CreditCardType" autocreate="true" generate="true">
            <value code="amex"/>
            <value code="visa"/>
            <value code="master"/>
            <value code="diners"/>
        </enumtype>

    </enumtypes>

    <maptypes>
        <maptype code="ExampleMap"
                 argumenttype="Language"
                 returntype="java.math.BigInteger"
                 autocreate="true"
                 generate="false"/>
        <maptype code="localized:java.lang.String"
                 argumenttype="Language"
                 returntype="java.lang.String"
                 autocreate="true"
                 generate="false"/>
        <maptype code="localized:java.lang.Integer"
                 argumenttype="Language"
                 returntype="java.lang.Integer"
                 autocreate="true"
                 generate="false"/>

    <relations>


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
                <custom-properties>
                    <property name="query.filter">
                        <value>"{original} is null"</value>
                    </property>
                </custom-properties>
            </targetElement>
        </relation>

        <relation code="PrincipalGroupRelation" autocreate="true" generate="false" localized="false"
                  deployment="de.hybris.platform.persistence.link.PrincipalGroupRelation">
            <sourceElement qualifier="members" type="Principal" collectiontype="set" cardinality="many" ordered="false">
                <modifiers read="true" write="true" search="true" optional="true"/>
            </sourceElement>
            <targetElement qualifier="groups" type="PrincipalGroup" collectiontype="set" cardinality="many"
                           ordered="false">
                <modifiers read="true" write="true" search="true" optional="true"/>
            </targetElement>
        </relation>

    </relations>

    <itemtypes>

        <!--
         modifier defaults

            inital      = false
            read        = true
            write       = true
            optional    = true
            private     = false
            search      = true

   -->

        <!-- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        ~~ base item types
        ~~ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ -->

        <itemtype code="Item"
                  extends=""
                  jaloclass="de.hybris.platform.jalo.Item"
                  deployment="de.hybris.platform.persistence.Item"
                  autocreate="true"
                  generate="false"
                  abstract="true">
            <attributes>
                <attribute autocreate="true" qualifier="creationtime" type="java.util.Date">
                    <persistence type="cmp" qualifier="creationTimestampInternal"/>
                    <modifiers read="true" write="false" search="true" optional="true" initial="true"/>
                </attribute>
                <attribute autocreate="true" qualifier="modifiedtime" type="java.util.Date">
                    <persistence type="cmp" qualifier="modifiedTimestampInternal"/>
                    <modifiers read="true" write="true" search="true" optional="true"/>
                </attribute>
                <attribute autocreate="true" qualifier="itemtype" type="ComposedType">
                    <persistence type="cmp" qualifier="typePkString"/>
                    <modifiers read="true" write="true" search="true" optional="true"/>
                </attribute>
                <attribute autocreate="true" qualifier="owner" type="Item">
                    <persistence type="cmp" qualifier="ownerPkString"/>
                    <modifiers read="true" write="false" search="true" optional="true" private="false" initial="true"/>
                </attribute>
                <attribute autocreate="true" qualifier="pk" type="de.hybris.platform.core.PK">
                    <persistence type="cmp" qualifier="pkString"/>
                    <modifiers read="true" write="false" search="true" optional="false"/>
                </attribute>
                 <attribute autocreate="true" qualifier="sealed" type="boolean">
                   	<persistence type="property" qualifier="sealed"/>
                    <modifiers read="true" write="false" search="true" optional="true"/>
                </attribute>
            </attributes>
        </itemtype>

    </itemtypes>

</items>

```

# Autogeneration

- hybris item defintions are declared in `<extension-name>-items.xml`
- `Ant` proccess assembles the type definitions and generates these models, DTOs and resources
- hybris also creates the DB tables when invoking the system initialize /update 
- type code `1` to `32767`
    - `1` to `10000` are reseved for Hybris
    - `10001` to `32767` are available 

# Deployment


- By Default items are stored in same DB tables as its super type
- by specifying `deployment` we create DB tables for its own
- if extending `GenericItem`, it is recommended to use `deployment` (`build.development.mode=true` can enforce this)

# Object Relationship Mapping

![O-R Mapping](/img/hybris/o-r-mapping.png)

- `atomicType` values are stored directly in table columns (String = VARCHAR)
- `ComposedType` references are stored as PK in columns
- `One2Many` at Many side will hold PK of One side
- `Many2Many` new DB table having `source` and `target` pks
- `CollectionType`in a single column as comma separated PKs or Atomic values

# Localization

- provides support to i18n
    - localize item and attribute names
    - localize item properties

## Localize the Item and item their attributes

- stored in `<extension-name>-locales_XY.properties`
    - `XY` - ISO code   
- localizations are stored in DB, System Update and Initialize has the option `Localize types`
- table name is `<typename>lp` e.g `productlp` 

```properties
type.{typename}.name=value
type.{typename}.description=value
type.{typename}.{attributename}.name=value
type.{typename}.{attributename}.description=value
type.{enumcode}.{valuecode}.name=value
```  

## Localize the Item Data

- item properies can be localized

```xml
<attribute
qualifier="quote"
type="localized:java.lang.String"
/>
```