#microsserviços #springboot #Monitoramento 

In this tutorial, we're going to look at ways we can change the logging level of a Spring Boot application at runtime. As with many things, [[Logging in spring boot |Spring Boot has built-in logging functionality]] that configures it for us. We're going to explore how to adjust the logging levels of a running application.

We'll look at three ways of doing that: using the Spring Boot [[Spring Actuator | Actuator]] loggers endpoint, the auto-scan functionality in [[Logback]] and finally using the [[Spring Boot Admin]] tool.

## Spring boot actuator

We're going to start by using the /_loggers_ Actuator endpoint to display and change our logging level. **The /_loggers_ endpoint is available at _actuator/loggers_ and we can access a specific logger by appending its name as part of the path.**

For example, we can access the root logger with the URL [_http://localhost:8080/actuator/loggers/root_](http://localhost:8080/actuator/loggers/root).

### Setup

Let's start by setting up our application to use the Spring Boot Actuator.

First, we need to add the [Spring Boot Actuator Maven dependency](https://search.maven.org/search?q=spring-boot-starter-actuator) to our _pom.xml_ file:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
    <version>2.4.0</version>
</dependency>
```

**Starting with Spring Boot 2.x, most endpoints are disabled by default**, so we'll also need to enable the /_loggers_ endpoint in our _application.properties_ file:

```yaml
management:
  endpoints:
    web:
      exposure:
        include: loggers
  endpoint:
    loggers:
      enabled: 'true'
```

Lastly, let's create a controller with a series of logging statements so that we can see the effects of our experiments:

```java
@RestController
@RequestMapping("/log")
public class LoggingController {
    private Log log = LogFactory.getLog(LoggingController.class);

    @GetMapping
    public String log() {
        log.trace("This is a TRACE level message");
        log.debug("This is a DEBUG level message");
        log.info("This is an INFO level message");
        log.warn("This is a WARN level message");
        log.error("This is an ERROR level message");
        return "See the log for details";
    }
}
```

Let's start our application and access our log API:

```shell
curl http://localhost:8080/log
```

Then, let's check the logs where we should find three logging statements:

```shell
2019-09-02 09:51:53.498  INFO 12208 --- [nio-8080-exec-1] c.b.s.b.m.logging.LoggingController      : This is an INFO level message
2019-09-02 09:51:53.498  WARN 12208 --- [nio-8080-exec-1] c.b.s.b.m.logging.LoggingController      : This is a WARN level message
2019-09-02 09:51:53.498 ERROR 12208 --- [nio-8080-exec-1] c.b.s.b.m.logging.LoggingController      : This is an ERROR level message
```

Now, let's call the /_loggers_ Actuator endpoint to check on the logging level for our _com.baeldung.spring.boot.management.logging_ package:

```shell
curl http://localhost:8080/actuator/loggers/com.baeldung.spring.boot.management.logging
  {"configuredLevel":null,"effectiveLevel":"INFO"}
```

To change the logging level, we can issue a _POST_ request to the /_loggers_ endpoint:

```shell
curl -i -X POST -H 'Content-Type: application/json' -d '{"configuredLevel": "TRACE"}'
  http://localhost:8080/actuator/loggers/com.baeldung.spring.boot.management.logging
  HTTP/1.1 204
  Date: Mon, 02 Sep 2019 13:56:52 GMT
```

If we check the logging level again, we should see it set to _TRACE_:

```shell
curl http://localhost:8080/actuator/loggers/com.baeldung.spring.boot.management.logging
  {"configuredLevel":"TRACE","effectiveLevel":"TRACE"}
```

Finally, we can re-run our log API and see our changes in action:

```shell
2019-09-02 09:59:20.283 TRACE 12208 --- [io-8080-exec-10] c.b.s.b.m.logging.LoggingController      : This is a TRACE level message
2019-09-02 09:59:20.283 DEBUG 12208 --- [io-8080-exec-10] c.b.s.b.m.logging.LoggingController      : This is a DEBUG level message
2019-09-02 09:59:20.283  INFO 12208 --- [io-8080-exec-10] c.b.s.b.m.logging.LoggingController      : This is an INFO level message
2019-09-02 09:59:20.283  WARN 12208 --- [io-8080-exec-10] c.b.s.b.m.logging.LoggingController      : This is a WARN level message
2019-09-02 09:59:20.283 ERROR 12208 --- [io-8080-exec-10] c.b.s.b.m.logging.LoggingController      : This is an ERROR level message
```

## Logback Auto-scan

**By default, our Spring Boot applications are using the Logback logging library.** Let's now look at how we can take advantage of Logback's auto-scan feature to change our logging level.

First, let's add some Logback configuration by placing a file named _logback.xml_ under our _src/main/resources_ directory:

```xml
<configuration scan="true" scanPeriod="15 seconds">
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <logger name="com.baeldung.spring.boot.management.logging" level="INFO" />

    <root level="info">
        <appender-ref ref="STDOUT" />
    </root>
</configuration>
```

The key detail is in the first line of the _logback.xml_ file. **By setting the _scan_ attribute to _true_, we're telling Logback to check the configuration file for changes. The auto-scan happens every 60 seconds by default.**

Setting the _scanPeriod_ to 15 seconds tells it to reload every 15 seconds so we don't have to wait around as long during our experiments.

Let's try it out by starting the application and calling our log API again:

```shell
curl http://localhost:8080/log
```

Our output should reflect the _INFO_ logging level for our package:

```shell
10:21:13.167 [http-nio-8080-exec-1] INFO  c.b.s.b.m.logging.LoggingController - This is an INFO level message
10:21:13.167 [http-nio-8080-exec-1] WARN  c.b.s.b.m.logging.LoggingController - This is a WARN level message
10:21:13.168 [http-nio-8080-exec-1] ERROR c.b.s.b.m.logging.LoggingController - This is an ERROR level message
```

Now, let's modify our _com.baeldung.spring.boot.management.logging_ logger in _logback.xml_ to _TRACE_:

```xml
<logger name="com.baeldung.spring.boot.management.logging" level="TRACE" />
```

After giving it 15 seconds, let's rerun the log API at [_http://localhost:8080/log_](http://localhost:8080/log) and check our log output:

```shell
10:24:18.429 [http-nio-8080-exec-2] TRACE c.b.s.b.m.logging.LoggingController - This is a TRACE level message
10:24:18.430 [http-nio-8080-exec-2] DEBUG c.b.s.b.m.logging.LoggingController - This is a DEBUG level message
10:24:18.430 [http-nio-8080-exec-2] INFO  c.b.s.b.m.logging.LoggingController - This is an INFO level message
10:24:18.430 [http-nio-8080-exec-2] WARN  c.b.s.b.m.logging.LoggingController - This is a WARN level message
10:24:18.430 [http-nio-8080-exec-2] ERROR c.b.s.b.m.logging.LoggingController - This is an ERROR level message
```

