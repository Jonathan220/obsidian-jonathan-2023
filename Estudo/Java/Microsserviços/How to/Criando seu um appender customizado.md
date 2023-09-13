#microsserviços #Monitoramento #springboot 

In this article, we'll explore creating a custom Logback appender.

Logback ships with many built-in appenders that write to standard out, file system, or database. The beauty of this framework’s architecture is its modularity, which means we can easily customize it.

In this tutorial, we'll focus on _logback-classic_, which requires the following Maven dependency:

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.3.5</version>
</dependency>
```

The latest version of this dependency is available on [Maven Central](https://search.maven.org/classic/#search%7Cgav%7C1%7Cg%3A%22ch.qos.logback%22%20AND%20a%3A%22logback-classic%22).

Logback provides base classes we can extend to create a custom appender.

_Appender_ is the generic interface that all appenders must implement. The generic type is either _ILoggingEvent_ or _AccessEvent_, depending on if we're using _logback-classic_ or _logback-access_, respectively.

**Our custom appender should extend either** **_AppenderBase_** **or** **_UnsynchronizedAppenderBase_**_,_ which both implement _Appender_ and handle functions such as filters and status messages.

_AppenderBase_ is thread-safe; _UnsynchronizedAppenderBase_ subclasses are responsible for managing their thread safety.

Just as the _ConsoleAppender_ and the _FileAppender_ both extend _OutputStreamAppender_ and call the super method _setOutputStream()_, **the** **custom appender should subclass** **_OutputStreamAppender_** **if it is writing to an** **_OutputStream_**.

For our custom example, we'll create a toy appender named _MapAppender_. This appender will insert all logging events into a _Concurrent__HashMap_, with the timestamp for the key. To begin, we'll subclass _AppenderBase_ and use _ILoggingEvent_ as the generic type:

```java
public class MapAppender extends AppenderBase<ILoggingEvent> {

    private ConcurrentMap<String, ILoggingEvent> eventMap 
      = new ConcurrentHashMap<>();

    @Override
    protected void append(ILoggingEvent event) {
        eventMap.put(String.valueOf(System.currentTimeMillis()), event);
    }
    
    public Map<String, ILoggingEvent> getEventMap() {
        return eventMap;
    }
}
```

Next, to enable the _MapAppender_ to start receiving logging events, let's add it as an appender in our configuration file _logback.xml_:

```xml
<configuration>
    <appender name="map" class="com.baeldung.logback.MapAppender"/>
    <root level="info">
        <appender-ref ref="map"/>
    </root>
</configuration>
```

## **Setting Properties**

Logback uses JavaBeans introspection to analyze properties set on the appender. Our custom appender will need getter and setter methods to allow the introspector to find and set these properties.

Let's add a property to _MapAppender_ that gives the _eventMap_ a prefix for its key:

```java
public class MapAppender extends AppenderBase<ILoggingEvent> {

    //...

    private String prefix;

    @Override
    protected void append(ILoggingEvent event) {
        eventMap.put(prefix + System.currentTimeMillis(), event);
    }

    public String getPrefix() {
        return prefix;
    }

    public void setPrefix(String prefix) {
        this.prefix = prefix;
    }

    //...

}
```

Next, add a property to our configuration to set this prefix:

```xml
<configuration debug="true">

    <appender name="map" class="com.baeldung.logback.MapAppender">
        <prefix>test</prefix>
    </appender>

    //...

</configuration>
```

## **Error Handling**

To handle errors during the creation and configuration of our custom appender, we can use methods inherited from _AppenderBase_.

For example, when the prefix property is a null or an empty string, the _MapAppender_ can call _addError()_ and return early:

```java
public class MapAppender extends AppenderBase<ILoggingEvent> {

    //...

    @Override
    protected void append(final ILoggingEvent event) {
        if (prefix == null || "".equals(prefix)) {
            addError("Prefix is not set for MapAppender.");
            return;
        }

        eventMap.put(prefix + System.currentTimeMillis(), event);
    }

    //...

}
```

When the debug flag is turned on in our configuration, we'll see an error in the console that alerts us that the prefix property has not been set:

```xml
<configuration debug="true">

    //...

</configuration>
```

