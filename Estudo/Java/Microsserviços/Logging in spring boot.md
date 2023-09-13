#springboot #microsserviços #Monitoramento 

Spring boot é um flamework muito útil. Ele nos permite esquecer sobre a maioria das configurações, muitas das quais são opcionalmente auto-configuradas.

No caso de logging, a única dependencia é o **Apache  commons logging**.

Nós só precisamos importar a dependencia **Apache commons logging** quando fazemos uso do **spring 4.x** (**Spring boot 1.x**), já que a mesma é provida pelo módulo **spring-jcl** no **Spring 5** (**spring boot 2.x**).

Não devemos nos preocupar em importar o modulo **spring-jcl** uma vez que fazemos uso do **spring boot starter**, isto por que todo starter, como o [[spring-boot-starter-web]], depende do **spring-boot-logging**.

## Default Logback Logging

Quando usando starters, [[logback]] é usado por padrão como logging.

Quando uma aplicação é iniciada com o uso do spring boot, o console imprime as informações de log por padrão no nível **INFO**, o que significa que mensagens no nível **TRACE** e **DEBUG** não vão estar disponível.

No sentindo de ativar estas mensagens sem mudar a sua configuração, nós podemos passar o `-debug` ou `-trace` como argumentos na linha de comando.

```Shell
	java -jar target/spring-boot-logging-0.0.1-SNAPSHOT.jar --trace
```

## Log levels

Spring boot também nos dá acesso a um refinamento maior na configuração dos níveis de log via varáveis de ambiente. Há várias formas de realizar essa configuração.

Primeiro, nós podemos configurar o nível de log dentro das opções da VM, como no [[weblogic]].

```shell
-Dlogging.level.org.springframework=TRACE 
-Dlogging.level.com.baeldung=TRACE
```

Alternativamente, se estivermos usando **Maven** podemos definir via linha de comando:

```shell
mvn spring-boot:run -Dspring-boot.run.arguments=--logging.level.org.springframework=TRACE,--logging.level.com.baeldung=TRACE
```

A alteração também pode ser feita através de configuração no properties:

```YAML
logging:
  level:
    root: WARN
    com.baeldung: TRACE
```

Observe que o nível de log "root" foi definido como "WARN" e o nível de log "com.baeldung" foi definido como "TRACE".

Também podemos mudar o nível de logging permanentemente usando o arquivo de configuração de logging fornecido pelo flamework.
Se estiver usando o [[logback]] como logging, os flagmentos abaixo são responsáveis por configurar o nível de log em dois pacotes diferentes:

```XML
<logger name="org.springframework" level="INFO" />
<logger name="com.baeldung" level="INFO" />
```

Lembresse, se o nível do log para um pacote é definido em mais de um lugar, usando as diferentes opções de configuração mencionadas acima ao mesmo tempo e também com diferentes níves de log o nível mais baixo será usado.
Então, se nós configurarmos níveis usando o [[logback]], spring boot e variáveis ao mesmo tempo, o nível de log irá ser **TRACE**, pois é o nível mais baixo entre os níveis requisitados.

## Logback configuration

Embora a configuração padrão seja útil para começar do zero ou realizar algum experimento, é provável que não seja o suficiente para um monitoramento mais detalhado.

Vamos verificar como incluir uma configuração de [[logback]] com uma cor e formato das mensagens de log diferente do padrão.

Primeiro devemos optar por uma solução que permita configurar nosso log sem precisar popular o application.properties com as configurações de log, pois o mesmo é usado por muitas outras configurações da aplicação.

Quando um arquivo no classpath tem um dos nomes abaixo, o spring boot irá automáticamente carrega-lo acima das configurações padrões:
- logback-spring.xml
- logback.xml
- logback-spring.groovy
- logback.groovy

É recomendado usar a opção com -spring sempre que possível.

Um exemplo de arquivo de configuração do [[logback]] segue abaixo:

```XML
<?xml version="1.0" encoding="UTF-8"?>
<configuration>

    <property name="LOGS" value="./logs" />

    <appender name="Console"
        class="ch.qos.logback.core.ConsoleAppender">
        <layout class="ch.qos.logback.classic.PatternLayout">
            <Pattern>
                %black(%d{ISO8601}) %highlight(%-5level) [%blue(%t)] %yellow(%C{1.}): %msg%n%throwable
            </Pattern>
        </layout>
    </appender>

    <appender name="RollingFile"
        class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${LOGS}/spring-boot-logger.log</file>
        <encoder
            class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <Pattern>%d %p %C{1.} [%t] %m%n</Pattern>
        </encoder>

        <rollingPolicy
            class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- rollover daily and when the file reaches 10 MegaBytes -->
            <fileNamePattern>${LOGS}/archived/spring-boot-logger-%d{yyyy-MM-dd}.%i.log
            </fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy
                class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>10MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
    </appender>
    
    <!-- LOG everything at INFO level -->
    <root level="info">
        <appender-ref ref="RollingFile" />
        <appender-ref ref="Console" />
    </root>

    <!-- LOG "com.baeldung*" at TRACE level -->
    <logger name="com.baeldung" level="trace" additivity="false">
        <appender-ref ref="RollingFile" />
        <appender-ref ref="Console" />
    </logger>

</configuration>

```


## log4j

Outra biblioteca de logging é o [[Log4j]]. Enquanto o Apache commons logging está no core, o [[logback]] é a implementação de referencia provida para a aplicação. 

Para podermos fazer uso de qualquer outra biblioteca de logging, precisamos excluir o logback de nossas dependencias.

Para todo starter como o baixo:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

Nós precisamos excluir o logback de toda dependencia que faz uso dele como **spring-boot-starter-web** acima.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```

Neste ponto precisamos colocar no classpath um arquivo nomeado como:
-   log4j2-spring.xml
-   log4j2.xml

Com isso nós iremos printar as mensagens de log através do log4j2 (sobrescrevendo o SLF4J) sem mais modificações.

Abaixo uma simples configuração para log4j2-spring.xml:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
    <Appenders>
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout
                pattern="%style{%d{ISO8601}}{black} %highlight{%-5level }[%style{%t}{bright,blue}] %style{%C{1.}}{bright,yellow}: %msg%n%throwable" />
        </Console>

        <RollingFile name="RollingFile"
            fileName="./logs/spring-boot-logger-log4j2.log"
            filePattern="./logs/$${date:yyyy-MM}/spring-boot-logger-log4j2-%d{-dd-MMMM-yyyy}-%i.log.gz">
            <PatternLayout>
                <pattern>%d %p %C{1.} [%t] %m%n</pattern>
            </PatternLayout>
            <Policies>
                <!-- rollover on startup, daily and when the file reaches 
                    10 MegaBytes -->
                <OnStartupTriggeringPolicy />
                <SizeBasedTriggeringPolicy
                    size="10 MB" />
                <TimeBasedTriggeringPolicy />
            </Policies>
        </RollingFile>
    </Appenders>

    <Loggers>
        <!-- LOG everything at INFO level -->
        <Root level="info">
            <AppenderRef ref="Console" />
            <AppenderRef ref="RollingFile" />
        </Root>

        <!-- LOG "com.baeldung*" at TRACE level -->
        <Logger name="com.baeldung" level="trace"></Logger>
    </Loggers>

</Configuration>
```

## Log4j sem SLF4J

Nós podemos também fazer uso do log4j2 nativamente, sem precisar passar através do [[SLF4J]] para imprimi as mensagens de log, para isso basta fazermos uso de algumas classe do próprio log4j2:

```java
import org.apache.logging.log4j.Logger;
import org.apache.logging.log4j.LogManager;
// [...]
Logger logger = LogManager.getLogger(LoggingController.class);

```

Nós não precisamos realizar qualquer outra modificação para o padrão log4j2 na configuração do spring boot.

Nós podemos agora explorar a novas funcionalidades do log4j2 sem ficarmos travado pelo uso do velha interface do SLF4J.

## Logging com o Lombok

Nos exemplos anteriores fizemos uso do próprio log4j para imprimir as mensagens de logs, mas podemos reduzir a quantidade de código fazendo uso de [[lombok]]. Primeiro vamos precisar da dependencia do lombok em nossa aplicação:

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.20</version>
    <scope>provided</scope>
</dependency>
```


## @SLF4J, @CommonsLog e @Log4j2

[[SLF4J]] a Apache commons Logging nos permitem mudar nossos flameworkings de logging sem impactar o nosso código.

Podemos fazer uso das anotações do Lombok @SL4J e @CommonsLog para adicionar a instancia de logger correta na nossa classe: `org.slf4j.Logger` para [[SLF4J]] e `org.apache.commons.logging.Log` para Apache Commons Logging.

Para ver estas anotações em ação, criamos uma classe sem uma instancia de logger somente com a anotação @SLF4J.

```java
@RestController
@Slf4j
public class LombokLoggingController {
 
    @RequestMapping("/lombok")
    public String index() {
        log.trace("A TRACE Message");
        log.debug("A DEBUG Message");
        log.info("An INFO Message");
        log.warn("A WARN Message");
        log.error("An ERROR Message");
 
        return "Howdy! Check out the Logs to see the output...";
    }
}
```

No código acima usamos a palavra log para imprimir as mensagens de log, isso por que a anotação automáticamente a adiciona como variável de logger.

Para fazer uso do [[Log4j]] usamos a anotação @log4j2.

```java
@RestController
@Log4j2
public class LombokLoggingController {
 
    @RequestMapping("/lombok")
    public String index() {
        log.trace("A TRACE Message");
        log.debug("A DEBUG Message");
        log.info("An INFO Message");
        log.warn("A WARN Message");
        log.error("An ERROR Message");
 
        return "Howdy! Check out the Logs to see the output...";
    }
}   
```

>[!caution]+
>Spring Boot also supports JDK logging, through the _logging.properties_ configuration file. There are cases when it's not a good idea to use it.
