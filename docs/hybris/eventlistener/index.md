---
layout: default
title: Event and Listener
parent: Hybris
has_children: false
---

# Event and Listener

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

# Events

- source publishes an event while any interested party registers a listener that is able to receive these events and then perform logic
- Events are published using an event service.
- Listeners are registered with the event service and can then react to the events.
- published locally or across cluster nodes
- based on Spring Events

# Event Service

- to publish event and register Listeners

```java
package de.hybris.platform.servicelayer.event;

import de.hybris.platform.servicelayer.event.events.AbstractEvent;
import java.util.Set;
import org.springframework.context.ApplicationListener;

public interface EventService {
	void publishEvent(AbstractEvent var1);

	boolean registerEventListener(ApplicationListener var1);

	boolean unregisterEventListener(ApplicationListener var1);

	Set<ApplicationListener> getEventListeners();
}
```
# Event 

- instance of a subclass of `AbstractEvent`
- contains a `source` object that denotes the origin of the event.

```java
public class MyEvent extends AbstractEvent implements ClusterAwareEvent
{
  Object source;

  public MyEvent(Serializable source)
  {
    super(source);
    this.source=source;
  }

  @Override
  public boolean publish(int sourceNodeId, int targetNodeId)
  {
    //decide from and to which clusternode this event should be sent
    return targetNodeId == 12;  //broadcast to cluster node 12 only
    //return sourceNodeId < 5 && targetNodeId >= 5; //broadcast any event from any cluster node 1-4 to the cluster nodes 5-?
    //return true; //broadcast from all to all cluster nodes
  }
}
```

## ClusterAwareEvent

- possible to send an event from one node to a specific second node or broadcast events across all nodes of the cluster
- always asynchrously process even in the same instance

```java
package de.hybris.platform.servicelayer.event;

import java.io.Serializable;
import java.util.Objects;

public interface ClusterAwareEvent extends Serializable {

	default boolean publish(int sourceNodeId, int targetNodeId) {
		throw new UnsupportedOperationException(
				"This method is deprecated. Use #canPublish(PublishEventContext) method instead.");
	}

	default boolean canPublish(PublishEventContext publishEventContext) {
		Objects.requireNonNull(publishEventContext, "publishEventContext is required");
		return this.publish(publishEventContext.getSourceNodeId(), publishEventContext.getTargetNodeId());
	}
}
```

## TransactionAwareEvent 

- publish events at the end of a transaction to get notified of model changes instead of attribute changes


```java
package de.hybris.platform.servicelayer.event;

public interface TransactionAwareEvent {
	boolean publishOnCommitOnly();

	Object getId();
}
```


# Event Listener

- objects that are notified of events and perform business logic depending on the kind of event.
- extend from the abstract class `AbstractEventListener` and implement `onEvent()` method
- by default event publishing is synchronous on same instance listener receives (thread is locked until listeners react!)
- event publishing in other cluster nodes is asynchronous

## Register Event listeners

- XML based 

```xml
<bean id="myListener" class="my.package.MyListener" parent="abstractEventListener">
```

```java
public class MyEventListener extends AbstractEventListener<AfterItemCreationEvent>
{
   @Override
   protected void onEvent(final AfterItemCreationEvent event)
   {
          // Your logic here
   }
}
```

- Java based

```java
@Resource
EventService eventService;
...
eventService.registerEventListener(new MyListener());
```

## Publishing Events

```java
source = //any java object which might be interesting
MyEvent event = new MyEvent(source);

eventService.publishEvent(event);

```

# Script as Event Listeners

- `Script` item has the content
- `ScriptingEventService` allows registering and unregistering the dynamic event listeners by scriptURI at runtime
- `ScriptListenerWrapper` wraps the dynamic listener, which is necessary to always get the same listener instance for a given ScriptURI.
- extend `ScriptingEvent` to create custom events 

## Event Listener Script

- import below script through HAC

```groovy
import de.hybris.platform.servicelayer.event.impl.AbstractEventListener
import de.hybris.platform.servicelayer.event.events.AbstractEvent
import de.hybris.platform.scripting.events.TestScriptingEvent
class MyScriptingEventListener extends AbstractEventListener<AbstractEvent>{
     
@Override    
void onEvent(AbstractEvent event)
{   
  if(event instanceof TestScriptingEvent){
    println 'hello groovy! '+ new Date();
  }
  else{
   println 'another event published '
   println event
  }
}
}
new MyScriptingEventListener();
```

- use save option to store in DB 
- register the Listner

```groovy
scriptingEventService.registerScriptingEventListener('model://myEventListenerScript')
```

- create and publish event

```groovy
import de.hybris.platform.scripting.events.TestScriptingEvent
event = new TestScriptingEvent('myEvent')
eventService.publishEvent(event);
```

