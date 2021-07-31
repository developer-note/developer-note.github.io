---
layout: default
title: Cron Job
parent: Hybris
has_children: false
---

# Cron Job

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

## Cronjob

- to automated task performed at certain time or fixed interval
- use cases
    - backups
    - synchronization
    - import/ export
- consists of 
    - `ConJob` - run time informations
    - `Job` - what to do
    - `Trigger` - when to run
- they have thier own `SessionContext` (with user)

# Creating the Cronjob

## Service Layer Job

```java
public class MyJob extends AbstractJobPerformable<CronJobModel>
{
  public PerformResult perform(final CronJobModel cronJob)
  {
  // Do something...
  return  new PerformResult(CronJobResult.SUCCESS, CronJobStatus.FINISHED); 
  }
}
```
- implement `JobPerformable<CronJobModel>` or extend `AbstractJobPerformable<CronJobModel>`
    ```java
    package de.hybris.platform.servicelayer.cronjob;

    import de.hybris.platform.cronjob.model.CronJobModel;

    public interface JobPerformable<T extends CronJobModel> {
      PerformResult perform(T var1);

      boolean isPerformable();

      boolean isAbortable();
    }
    ```
- `CronJobResult`
    - `ERROR`
    - `FAILURE`
    - `SUCCESS`
    - `UNKNOWN`
- `CronJobStatus`
    - `ABORTED`
    - `FINISHED`
    - `PAUSED`
    - `RUNNING`
    - `RUNNINGSTART`
    - `UNKNOWN`

##  Register a Spring Bean and create a ServiceLayerJob

```xml
<bean id="myJob" class="my.bookstore.MyJob" parent="abstractJobPerformable"/>
```

## Create a ServiceLayerJob item

- running TSU for essential data will create the `ServiceLayerJob` for each bean implementing `JobPerformable`
- Impex

```
INSERT_UPDATE ServicelayerJob;code[unique=true];springId
;myJob;myJob
```

## Create a CronJob

```
INSERT_UPDATE CronJob; code[unique=true];job(code);singleExecutable;sessionLanguage(isocode)
;myCronJob;myJob;false;de
```

## Create a Trigger

```
INSERT_UPDATE Trigger;cronjob(code)[unique=true];cronExpression
;myCronJob; 0 0 0 * * ? 

# Cron expression format = sec min hour day month weekday year 
# * in any field means any value, ? means ignore. Year is optiona
```

# Different Ways to trigger

- Backoffice > System > Background Process > Cronjob
- Impex

```
#%beanshell%  afterEach: impex.getLastImportedItem().setActivationTime(new Date());
```

- Ant

```shell
ant runcronjob -­‐Dcronjob=myCronJob
```

- Script

```groovy
cronJobService.performCronJob( myCronJobModel);
```

# ConJob Scripting

## Script 

- the item type where the script content is stored

```
INSERT_UPDATE Script;code[unique=true];content 
;myGroovyScript;println 'hello groovy! '+ new Date()
```

## ScriptingJob

- subtype of ServicelayerJob, which contains the scriptURI
- the script can be retrieved at runtime from classpath, DB etc.)
- current CronJob model is in the context parameter as `'cronjob'` (key)
- can return a cronjob result

```groovy
println 'hello groovy! '+ new Date()
println cronjob.code
println cronjob.status
return new PerformResult(CronJobResult.SUCCESS,CronJobStatus.FINISHED)
```

```
INSERT_UPDATE ScriptingJob; code[unique=true];scriptURI
;mydynamicJob;model://myGroovyScript
```

## ScriptingJobPerformable

- the implicit spring bean assigned to every ScriptingJob instance
- it implements the usual perform() method.

## Creating a cronjob instance

```
INSERT_UPDATE CronJob; code[unique=true];job(code)
;mydynamicCronJob;mydynamicJob
```

# Executing a cronjob using a script

```groovy
def dynamicCJ = cronJobService.getCronJob("mydynamicCronJob")
cronJobService.performCronJob(dynamicCJ,true)
```
- Trigger, manual execution in hMC/Backoffice, and impex beanshell



