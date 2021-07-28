# Building SAP Commerce

- does code generation, copying, parsing and compiling files
- uses XSL, Apache Ant, Eclipse compiler

## Extension Concept

- encapsulated piece of software that extends or introduces commerce functionality
- can contain business logic, type definitions, web application or Backoffice Administration cockpit configuration

## yempty extension 


- `/.externalToolBuilders` - eclipse builder for models from items.xml
- `/.settings` - eclipse related configurations
- `/lib` - external libraries
- `/resources` - *items.xml and localizations files
- `/src` - source code file
- `/testsrc` - junit test files
- `/web` - web modules, resources are only accessible to this Web application 
- `/gensrc` 
- `.classpath`
- `.project`
- `.springBeans` - config for Spring IDE plugin
- `buildcallbacks.xml` - allows to build custom build framework
- `extensioninfo.xml` - list all dependent extensions for build framework 
- `external-dependencies.xml` - third party dependencies
- `extgen.properties` 
- `project.properties` - properties related to this extension

Additonal files & folders are created after build 

- `/classes` - contains .class files from src, testsrc and gensrc
- `build.xml` - this build file calls platform build file 
- `extensioninfo.xsd`
- `platformhome.properties`

## Extension Module

- core module 
    - Type system definitions (`/resources/<extension-name>-items.xml`)
    - Java Source files (`/src`, `/testsrc`, `/gensrc`)
        - Manager class
        - Jalo classes
        - ServiceLayer classes
        - Junit test classes
    - Extension version (`/resources/<extension-name>.build`)
    - Localization Files (`/resources/localization`)
- web module
    - part of the extension accessible through browser
    - default url is `https://localhost:9002/<extension-name>`

## Adding an Extension to SAP Commerce

- add the extension to `<${HYBRIS_CONFIG_DIR}>/localextensions.xml`

## External Libraries

- `/lib` - other extensions may dependend on these libraries
- `/web/webroot/WEB-INF/lib` -loaded by Web App Classloader, not share with core module or other extension

## extension-dependencies.xml

- third party dependencies are listed in this maven pom.xml file
- locations
    - `/extension-dependencies.xml`
    - `/web/webroot/WEB-INF/extension-dependencies.xml`

## extensioninfo.xml

- extension is configured here
    - list of dependent extensions (`requires-extension`)
    - core module
        - `packageroot` - item and extension classes will be generated
        - `manager` - extension manager
    - web module
        - `webroot` - web root is configured here
        - `jspcompile` - jsp files will be precompiled

## Dependencies to Platform Extensions

- all extensions by default have dependencies to platform extensions, except extensions in `<HYBRIS_BIN_DIR> /platform/ext` 
- enable or disable this through `autoload` in `localextensions.xml`

## Jalo-Logic-Free Extension

- no custom logic in Jalo classess, only generated classes
- one non-abstract class generated for each items and Manager is annotated with `@SLDSafe` to user ServiceLayer Direct
- e.g. yempty, yvoid, and yhacext extension

### Migrating to Jalo-logic-free 

- add `jaloLogicFree="true"` to ` <extension>` element in `extensioninfo.xml`
- remove the `Generated<Extension>Manager.java`
- remove the non-abstract Jalo class for each type in `<Extension>-items.xml`
- custom logic should be migrated to ServiceLayer
- build using `ant clean all`

