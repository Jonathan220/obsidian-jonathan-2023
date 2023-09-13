#microsserviços #springboot #Monitoramento 

## O que é

O Logback é um framework de registro (logging) em Java de código aberto que permite a geração de arquivos de log altamente personalizados para aplicativos Java. É uma evolução do popular framework de registro log4j, e foi projetado para ser mais rápido e mais fácil de usar do que seu antecessor.

O Logback possui recursos avançados de configuração, permitindo que os desenvolvedores personalizem completamente a saída de registro. Ele suporta vários formatos de log, incluindo XML, JSON e padrões de conversão personalizados. Além disso, o Logback pode ser integrado a outros frameworks de registro e possui uma API consistente e fácil de usar.

O Logback é amplamente utilizado em projetos Java e é uma opção popular para o registro de aplicativos baseados em Spring Framework. Ele é compatível com a API de registro [[SLF4J]] e pode ser facilmente integrado com outras bibliotecas que usam esta API.

Em resumo, o Logback é uma ferramenta poderosa e flexível para o registro em Java, que permite aos desenvolvedores personalizar completamente a saída de registro de seus aplicativos.

## Como implementá-lo

Para implementar o Logback em um projeto Java, siga os seguintes passos:

1.  Adicione a dependência do Logback ao seu arquivo de configuração de dependências (por exemplo, o arquivo `pom.xml` para projetos Maven):

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-core</artifactId>
    <version>1.3.5</version>
</dependency>

<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>2.0.4</version>
    <scope>test</scope>
</dependency>
```

Logback also requires [_logback-classic.jar_](https://search.maven.org/classic/#search%7Cga%7C1%7Clogback-classic) on the classpath for runtime.

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.3.5</version>
</dependency>
```

2.  Crie um arquivo de configuração do Logback. Por padrão, o Logback procura por um arquivo chamado `logback.xml` ou `logback.groovy` no diretório raiz do classpath. Você também pode especificar um caminho diferente para o arquivo de configuração usando a propriedade do sistema `logback.configurationFile`.
    
3.  Configure o Logback de acordo com as necessidades do seu projeto. O arquivo de configuração do Logback especifica as configurações de log para diferentes níveis de log (por exemplo, DEBUG, INFO, WARN, ERROR) e pode incluir configurações para formatos de log personalizados, anexos de arquivos de log e muito mais,

4. Use a API do Logback em seu código Java para registrar mensagens de log. A API do Logback é compatível com a API de registro [[SLF4J]], que é amplamente utilizada em projetos Java. Aqui está um exemplo simples de como registrar uma mensagem de log usando o Logback:

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class MyApp {
    private static final Logger logger = LoggerFactory.getLogger(MyApp.class);

    public static void main(String[] args) {
        logger.info("Hello, Logback!");
    }
}

```

### Alterando o local do arquivo de configuração

Por padrão, o Logback procura pelo arquivo de configuração `logback.xml` ou `logback.groovy` no diretório raiz do classpath. Se você deseja especificar um caminho diferente para o arquivo de configuração do Logback, você pode usar a propriedade do sistema `logback.configurationFile`.

Existem várias maneiras de definir a propriedade `logback.configurationFile`, dependendo do ambiente em que seu aplicativo é executado:

1.  Defina a propriedade do sistema no comando de linha de comando ao iniciar seu aplicativo:

```shell
java -Dlogback.configurationFile=/caminho/para/logback.xml MyApp
```

2.  Defina a propriedade do sistema no arquivo de inicialização do seu aplicativo (por exemplo, `setenv.sh` ou `setenv.bat` em Tomcat):

```shell
set LOG_CONFIG=/caminho/para/logback.xml
java -Dlogback.configurationFile=%LOG_CONFIG% MyApp
```

3.  Defina a propriedade do sistema em tempo de execução em seu código Java:

```java
System.setProperty("logback.configurationFile", "/caminho/para/logback.xml");
```

Certifique-se de substituir `/caminho/para/logback.xml` pelo caminho absoluto para o seu arquivo de configuração do Logback. Com a propriedade `logback.configurationFile` definida corretamente, o Logback usará o arquivo de configuração especificado em vez do arquivo padrão `logback.xml` ou `logback.groovy`.

### Configurando o arquivo de configuração

#### 1.  Escolhendo o formato do arquivo de configuração

O Logback suporta dois formatos de arquivo de configuração: XML e Groovy. O formato XML é mais popular e amplamente usado, mas o formato Groovy é mais flexível e fácil de ler e escrever.

Para este tutorial, usaremos o formato XML, mas as mesmas configurações também podem ser aplicadas no formato Groovy.

#### 2.  Definindo a raiz do logger

A primeira coisa que você precisa fazer no arquivo de configuração do Logback é definir a raiz do logger. A raiz do logger é o ponto de entrada para todas as mensagens de log em seu aplicativo.

```xml
<configuration>
    <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
        <!-- Configurações do appender Console -->
    </appender>

    <root level="INFO">
        <appender-ref ref="console" />
    </root>
</configuration>

```

Aqui, estamos definindo a raiz do logger com o nível de log `INFO`, o que significa que todas as mensagens de log com níveis `INFO`, `WARN`, `ERROR` ou `FATAL` serão registradas.

>[!info]+
>A _Logger_ has a _Level,_ which can be set either via configuration or with _Logger.setLevel()._ Setting the level in code overrides configuration files.
>The possible levels are, in order of precedence: _TRACE, DEBUG, INFO, WARN_ and _ERROR._ Each level has a corresponding method that we use to log a message at that level.

Também estamos definindo um appender chamado `console`, que é um appender do tipo `ConsoleAppender`. O appender `console` envia todas as mensagens de log para a saída padrão do console.

#### 3.  Adicionando loggers específicos

Além da raiz do logger, você pode adicionar loggers específicos para diferentes pacotes ou classes em seu aplicativo. Isso permite que você controle o registro com mais precisão e personalize a saída de registro para diferentes partes do seu aplicativo.

```xml
<configuration>
    <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
        <!-- Configurações do appender Console -->
    </appender>

    <logger name="com.meuapp" level="DEBUG">
        <appender-ref ref="console" />
    </logger>

    <root level="INFO">
        <appender-ref ref="console" />
    </root>
</configuration>

```

Aqui, estamos adicionando um logger específico para o pacote `com.meuapp` com o nível de log `DEBUG`. Todas as mensagens de log com níveis `DEBUG`, `INFO`, `WARN`, `ERROR` ou `FATAL` para este pacote serão registradas no appender `console`.

Observe que o logger específico para `com.meuapp` está aninhado dentro do elemento `<configuration>`, mas fora do elemento `<root>`. Isso significa que o logger específico terá precedência sobre o logger raiz, e as mensagens de log para o pacote `com.meuapp` serão registradas com o nível `DEBUG`, enquanto as mensagens de log para outras partes do aplicativo serão registradas com o nível `INFO`.

#### 4.  Configurando os appenders

Os appenders são responsáveis por enviar as mensagens de log para os destinos desejados. O Logback suporta vários tipos de appenders, incluindo ConsoleAppender, FileAppender, RollingFileAppender, entre outros.

Vamos ver alguns exemplos de como configurar os appenders mais comuns:

1.  ConsoleAppender

O ConsoleAppender envia todas as mensagens de log para a saída padrão do console.

```xml
<appender name="console" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
        <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
    </encoder>
</appender>

```

O padrão `%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n` significa:

-   `%d{HH:mm:ss.SSS}`: a data e hora da mensagem no formato `HH:mm:ss.SSS`.
-   `[%thread]`: o nome do thread que gerou a mensagem.
-   `%-5level`: o nível de log da mensagem com no máximo 5 caracteres.
-   `%logger{36}`: o nome do logger que gerou a mensagem com no máximo 36 caracteres.
-   `%msg%n`: a mensagem de log e uma nova linha.

2.  FileAppender

O FileAppender envia todas as mensagens de log para um arquivo específico.

```xml
<appender name="file" class="ch.qos.logback.core.FileAppender">
    <file>/caminho/para/meu/arquivo.log</file>
    <append>true</append>
    <encoder>
        <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
    </encoder>
</appender>

```

Observe que estamos configurando o arquivo de log com o caminho `/caminho/para/meu/arquivo.log` e definindo a propriedade `append` como `true` para adicionar novas mensagens ao arquivo existente.

3.  RollingFileAppender

O RollingFileAppender é semelhante ao FileAppender, mas cria novos arquivos de log quando o arquivo atual atinge um determinado tamanho ou idade.

```xml
<appender name="rollingFile" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>/caminho/para/meu/arquivo.log</file>
    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
        <fileNamePattern>/caminho/para/meu/arquivo.%d{yyyy-MM-dd}.log</fileNamePattern>
        <maxHistory>30</maxHistory>
    </rollingPolicy>
    <encoder>
        <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
    </encoder>
</appender>
```

Observe que estamos configurando a política de rotação para criar um novo arquivo de log a cada dia (`<fileNamePattern>/caminho/para/meu/arquivo.%d{yyyy-MM-dd}.log</fileNamePattern>`) e manter os últimos 30 arquivos de log (`<maxHistory>30</maxHistory>`).

Você também pode criar seu próprio [[Criando seu próprio rollingPolicy|RollingPolicy]].

4.  SMTPAppender

O SMTPAppender envia todas as mensagens de log por e-mail.

```xml
<appender name="smtp" class="ch.qos.logback.classic.net.SMTPAppender">
    <smtpHost>smtp.gmail.com</smtpHost>
    <smtpPort>587</smtpPort>
    <STARTTLS>true</STARTTLS>
    <username>seuemail@gmail.com</username>
    <password>sua_senha</password>
    <to>destinatario1@exemplo.com, destinatario2@exemplo.com</to>
    <from>seuemail@gmail.com</from>
    <subject>Log de Erros</subject>
    <layout class="ch.qos.logback.classic.html.HTMLLayout" />
    <cyclicBufferTracker class="ch.qos.logback.core.spi.CyclicBufferTracker">
        <bufferSize>10</bufferSize>
    </cyclicBufferTracker>
</appender>
```

Observe que estamos configurando o servidor SMTP do Gmail para enviar as mensagens de log. Além disso, estamos especificando as informações de autenticação do remetente e o destinatário para onde as mensagens serão enviadas. Também estamos definindo o layout como HTML para tornar o e-mail mais legível.

Esses são apenas alguns exemplos de como configurar os appenders no Logback. Você pode encontrar mais informações na documentação oficial: [http://logback.qos.ch/manual/appenders.html](http://logback.qos.ch/manual/appenders.html).

Você pode criar seu próprio [[Criando seu um appender customizado|appender]] de for necessário.

#### 5. Entendendo os encoders possíveis

Os encoders no Logback são responsáveis por transformar os objetos de registro em um formato que possa ser armazenado ou exibido. Eles fazem a codificação dos dados de registro em um formato específico, como texto simples, JSON, XML, HTML, entre outros.

O Logback possui vários encoders já disponíveis, que podem ser usados em conjunto com os appenders para personalizar a saída de registro. Alguns dos encoders mais comuns são:

1.  PatternLayoutEncoder

O PatternLayoutEncoder é o encoder padrão do Logback. Ele permite configurar um layout personalizado de saída de registro usando uma string de padrão. Essa string pode incluir marcadores especiais que representam informações de registro, como o nível de log, a data e hora, o nome do logger, a mensagem, etc. Por exemplo:

```xml
<encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
    <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
</encoder>
```

Essa configuração define um padrão de saída de registro que inclui a data e hora do registro, o nome da thread, o nível de log, o nome do logger (com no máximo 36 caracteres), a mensagem de registro e uma nova linha.

2.  JsonEncoder

O JsonEncoder permite a saída de log no formato JSON. Ele é útil quando os logs precisam ser processados por outras ferramentas, como o [[Elasticsearch]]. Por exemplo:

```xml
<encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
    <providers>
        <timestamp/>
        <message/>
        <loggerName/>
        <threadName/>
        <logLevel/>
        <stackTrace/>
    </providers>
</encoder>

```

Essa configuração define um encoder que produz uma saída JSON para os campos timestamp, message, loggerName, threadName, logLevel e stackTrace.

3.  XmlEncoder

O XmlEncoder permite a saída de log no formato XML. Ele é semelhante ao JsonEncoder, mas produz saída em XML em vez de JSON. Por exemplo:

```xml
<encoder class="ch.qos.logback.core.encoder.EchoEncoder"/>
```

Essa configuração define um encoder simples que produz uma saída XML para cada evento de log.

Esses são apenas alguns exemplos de como configurar encoders no Logback. Existem muitos outros tipos de encoders disponíveis, e você pode escolher o que melhor se adapta às suas necessidades.

### Comprimindo logs rotacionados

O Logback possui a opção de comprimir os arquivos de log que foram rotacionados, para economizar espaço em disco. Para fazer isso, você precisa usar o RollingFileAppender e configurar o atributo "fileNamePattern" para incluir a extensão ".gz" para os arquivos antigos que serão comprimidos.

Por exemplo, a configuração a seguir irá comprimir os arquivos de log que foram rotacionados e que têm mais de 10 dias:

```xml
<appender name="file-appender" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>/caminho/para/os/logs/meu-log.log</file>
    <encoder>
        <pattern>%d{HH:mm:ss.SSS} %-5level %logger{36} - %msg%n</pattern>
    </encoder>
    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
        <fileNamePattern>/caminho/para/os/logs/meu-log.%d{yyyy-MM-dd}.%i.log.gz</fileNamePattern>
        <maxHistory>10</maxHistory>
        <totalSizeCap>10GB</totalSizeCap>
    </rollingPolicy>
</appender>
```

Neste exemplo, a configuração do RollingFileAppender inclui a extensão ".gz" no padrão do arquivo de nome de arquivo (`meu-log.%d{yyyy-MM-dd}.%i.log.gz`). Além disso, o atributo `totalSizeCap` é configurado para 10 GB, o que significa que o Logback irá limitar o tamanho total dos arquivos de log e apagar os arquivos mais antigos quando atingir esse limite. Neste caso, apenas os últimos 10 arquivos de log são mantidos.

Ao utilizar essa configuração, o Logback irá automaticamente comprimir os arquivos de log rotacionados com mais de 10 dias, economizando espaço em disco e mantendo um histórico de logs mais completo.

### Configurando expurgo no logback

É possível fazer expurgo (ou purging, em inglês) de logs com o Logback. Para isso, você pode usar a política de expurgo embutida na biblioteca.

A política de expurgo permite que você defina critérios para excluir automaticamente arquivos de log antigos. Você pode escolher o período de tempo após o qual os arquivos de log serão excluídos, a quantidade máxima de arquivos de log a serem mantidos e/ou o tamanho máximo dos arquivos de log a serem mantidos.

Para configurar a política de expurgo, você precisa definir um elemento `<policies>` dentro do `<rollingPolicy>` do seu arquivo de configuração do Logback. Por exemplo:

```xml
<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
    <fileNamePattern>/caminho/para/os/logs/meu-log.%d{yyyy-MM-dd}.log</fileNamePattern>
    <maxHistory>30</maxHistory>
    <totalSizeCap>100MB</totalSizeCap>
    <policies>
        <timeBasedFileNamingAndTriggeringPolicy />
        <sizeBasedTriggeringPolicy size="10MB" />
    </policies>
</rollingPolicy>
```

Neste exemplo, o elemento `<policies>` contém duas políticas de expurgo:

-   `<timeBasedFileNamingAndTriggeringPolicy />`: esta política é usada para definir o nome do arquivo de log com base no tempo. Ela é necessária para usar a política de expurgo baseada em tempo.
-   `<sizeBasedTriggeringPolicy size="10MB" />`: esta política é usada para definir o tamanho máximo do arquivo de log. Quando o tamanho do arquivo atinge o limite de 10 MB, o Logback irá criar um novo arquivo de log e excluir automaticamente o arquivo mais antigo.

Com essas políticas de expurgo configuradas, o Logback irá manter até 30 arquivos de log com base no tempo e 100 MB de arquivos de log no total. Quando o limite de 30 arquivos de log for atingido, o Logback irá excluir automaticamente o arquivo mais antigo. E quando o limite de 100 MB for atingido, o Logback irá criar um novo arquivo de log e excluir o arquivo mais antigo.

Observe que o uso da política de expurgo depende das suas necessidades específicas de log e da quantidade de espaço em disco disponível no seu servidor. É importante escolher as configurações adequadas para garantir que você mantenha um histórico de log útil e eficaz.

### Entendendo o campo additivity em logger

Em Logback, o campo `additivity` é usado para determinar se um logger específico deve herdar os handlers (manipuladores de log) dos seus pais na hierarquia de loggers.

Quando o valor do campo `additivity` é definido como `true`, o logger herdará os handlers dos seus pais na hierarquia. Quando o valor é definido como `false`, o logger não herdará os handlers.

Por exemplo, suponha que você tenha dois loggers: `com.exemplo.app` e `com.exemplo.app.foo`. Se você quiser que `com.exemplo.app.foo` herde os handlers do logger `com.exemplo.app`, você pode definir o campo `additivity` como `true` no logger `com.exemplo.app.foo`. Nesse caso, as mensagens de log emitidas pelo logger `com.exemplo.app.foo` serão enviadas para todos os handlers definidos no logger `com.exemplo.app`, além de quaisquer handlers definidos diretamente no logger `com.exemplo.app.foo`.

Por padrão, o valor do campo `additivity` é definido como `true`. Isso significa que, a menos que você especifique o contrário, um logger herdará todos os handlers definidos em seus pais. No entanto, em alguns casos, pode ser útil definir o valor como `false` para evitar a duplicação de logs em certas partes do seu aplicativo.

Você pode definir o valor do campo `additivity` no seu arquivo de configuração do Logback usando a tag `<logger>`:

```xml
<logger name="com.exemplo.app.foo" level="DEBUG" additivity="false">
    <appender-ref ref="fileAppender" />
</logger>
```

Neste exemplo, o logger `com.exemplo.app.foo` está configurado para enviar as mensagens de log para o handler definido em `fileAppender`, mas não para os handlers definidos em seus pais na hierarquia de loggers.

### **Parameterized Messages**

Unlike the messages in the sample snippets above, most useful log messages require appending _Strings._ This entails allocating memory, serializing objects, concatenating _Strings,_ and potentially cleaning up the garbage later.

Consider the following message:

```java
log.debug("Current count is " + count);
```

We incur the cost of building the message whether the _Logger_ logs the message or not.

Logback offers an alternative with its parameterized messages:

```java
log.debug("Current count is {}", count);
```

**The braces {} will accept any _Object_ and uses its _toString()_ method to build a message only after verifying that the log message is required.**

Let's try some different parameters:

```java
String message = "This is a String";
Integer zero = 0;

try {
    logger.debug("Logging message: {}", message);
    logger.debug("Going to divide {} by {}", 42, zero);
    int result = 42 / zero;
} catch (Exception e) {
    logger.error("Error dividing {} by {} ", 42, zero, e);
}
```

This snippet yields:

```shell
21:32:10.311 [main] DEBUG com.baeldung.logback.LogbackTests - Logging message: This is a String
21:32:10.316 [main] DEBUG com.baeldung.logback.LogbackTests - Going to divide 42 by 0
21:32:10.316 [main] ERROR com.baeldung.logback.LogbackTests - Error dividing 42 by 0
java.lang.ArithmeticException: / by zero
  at com.baeldung.logback.LogbackTests.givenParameters_ValuesLogged(LogbackTests.java:64)
...
```

We see how a _String,_ an _int,_ and an _Integer_ can be passed in as parameters.

Also, when an _Exception_ is passed as the last argument to a logging method, Logback will print the stack trace for us.

### **Troubleshooting Configuration**

Logback configuration files can get complicated, so there are several built-in mechanisms for troubleshooting.

To see debug information as Logback processes the configuration, we can turn on debug logging:

```xml
<configuration debug="true">
  ...
</configuration>
```

Logback will print status information to the console as it processes the configuration:

```shell
23:54:23,040 |-INFO in ch.qos.logback.classic.LoggerContext[default] - Found resource [logback-test.xml] 
  at [file:/Users/egoebelbecker/ideaProjects/logback-guide/out/test/resources/logback-test.xml]
23:54:23,230 |-INFO in ch.qos.logback.core.joran.action.AppenderAction - About to instantiate appender 
  of type [ch.qos.logback.core.ConsoleAppender]
23:54:23,236 |-INFO in ch.qos.logback.core.joran.action.AppenderAction - Naming appender as [STDOUT]
23:54:23,247 |-INFO in ch.qos.logback.core.joran.action.NestedComplexPropertyIA - Assuming default type 
  [ch.qos.logback.classic.encoder.PatternLayoutEncoder] for [encoder] property
23:54:23,308 |-INFO in ch.qos.logback.classic.joran.action.RootLoggerAction - Setting level of ROOT logger to DEBUG
23:54:23,309 |-INFO in ch.qos.logback.core.joran.action.AppenderRefAction - Attaching appender named [STDOUT] to Logger[ROOT]
23:54:23,310 |-INFO in ch.qos.logback.classic.joran.action.ConfigurationAction - End of configuration.
23:54:23,313 |-INFO in ch.qos.logback.classic.joran.JoranConfigurator@5afa04c - Registering current configuration 
  as safe fallback point
```

If warnings or errors are encountered while parsing the configuration file, Logback writes status messages to the console.

There is a second mechanism for printing status information:

```xml
<configuration>
    <statusListener class="ch.qos.logback.core.status.OnConsoleStatusListener" />  
    ...
</configuration>
```

**The _StatusListener_ intercepts status messages and prints them during configuration, as well as while the program is running**.

The output from all configuration files is printed, making it useful for locating “rogue” configuration files on the classpath.

### **Reloading Configuration Automatically**

Reloading logging configuration while an application is running is a powerful troubleshooting tool. Logback makes this possible with the _scan_ parameter:

```xml
<configuration scan="true">
  ...
</configuration>
```

The default behavior is to scan the configuration file for changes every 60 seconds. We can modify this interval by adding _scanPeriod_:

```xml
<configuration scan="true" scanPeriod="15 seconds">
  ...
</configuration>
```

We can specify values in milliseconds, seconds, minutes, or hours.

### **Variable Substitution**

Logback configuration files support variables. We define variables inside the configuration script or externally. A variable can be specified at any point in a configuration script in place of a value.

For example, here is the configuration for a _FileAppender_:

```xml
<property name="LOG_DIR" value="/var/log/application" />
<appender name="FILE" class="ch.qos.logback.core.FileAppender">
    <file>${LOG_DIR}/tests.log</file>
    <append>true</append>
    <encoder>
        <pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>
    </encoder>
</appender>
```

At the top of the configuration, we declared a _property_ named _LOG_DIR._ Then we used it as part of the path to the file inside the _appender_ definition.

Properties are declared in a < property> tag in configuration scripts, but they're also available from outside sources, such as system properties. We could omit the _property_ declaration in this example and set the value of _LOG_DIR_ on the command line:

```shell
$ java -DLOG_DIR=/var/log/application com.baeldung.logback.LogbackTests
```

We specify the value of the property with _${propertyname}._ Logback implements variables as text replacement. Variable substitution can occur at any point in a configuration file where a value can be specified.

