#springboot #microsserviços #Monitoramento 

# O que é

O Spring Cloud Sleuth é uma biblioteca do Spring Cloud que fornece suporte para rastreamento distribuído em aplicativos baseados em microsserviços. Ele permite que os desenvolvedores obtenham visibilidade do fluxo de uma solicitação em um ambiente distribuído, permitindo a correlação de logs e rastreamento de problemas.

O Spring Cloud Sleuth trabalha adicionando IDs exclusivos a cada solicitação recebida e propagando esses IDs por todas as chamadas de serviço subsequentes, permitindo que os desenvolvedores sigam o caminho completo da solicitação. Ele também pode integrar-se com outras ferramentas de rastreamento e monitoramento, como o [[Zipkin]], para fornecer uma visão mais detalhada do desempenho do sistema e da resolução de problemas em ambientes de microsserviços.

# Como funciona

Suponha que você tem um aplicativo de e-commerce que consiste em três microsserviços: um serviço de catálogo de produtos, um serviço de carrinho de compras e um serviço de pedidos. Quando um usuário adiciona um item ao carrinho de compras e conclui a compra, uma solicitação é enviada do cliente para o serviço de carrinho de compras, que, por sua vez, faz uma solicitação para o serviço de catálogo de produtos para obter informações sobre o item e, em seguida, faz uma solicitação para o serviço de pedidos para criar um pedido.

Com o Spring Cloud Sleuth, cada solicitação recebida em um serviço é atribuída um identificador único de rastreamento, que é propagado por todas as chamadas subsequentes. Esses identificadores de rastreamento podem ser visualizados em logs e em ferramentas de rastreamento, como o [[Zipkin]].

Por exemplo, suponha que um usuário está navegando em seu site de e-commerce e adiciona um item ao carrinho de compras. O Spring Cloud Sleuth atribui um identificador de rastreamento único a essa solicitação e o passa para o serviço de carrinho de compras. Quando o serviço de carrinho de compras faz uma solicitação para o serviço de catálogo de produtos para obter informações sobre o item, ele passa o mesmo identificador de rastreamento. Em seguida, quando o serviço de carrinho de compras faz uma solicitação para o serviço de pedidos para criar um pedido, ele também passa o mesmo identificador de rastreamento.

Se houver um problema com a solicitação, os desenvolvedores podem usar o identificador de rastreamento para seguir o caminho da solicitação, desde o serviço de carrinho de compras até o serviço de catálogo de produtos e, em seguida, para o serviço de pedidos. Isso pode ajudar a identificar rapidamente o problema e resolvê-lo.

Em resumo, o Spring Cloud Sleuth fornece um mecanismo de rastreamento distribuído que permite que os desenvolvedores sigam o caminho completo de uma solicitação em um ambiente de microsserviços, ajudando na resolução de problemas e no monitoramento de desempenho.

# Que identificador é esse?

O identificador que o Spring Cloud Sleuth atribui a cada solicitação é chamado de "Trace ID". O Trace ID é um identificador único que permite que o Sleuth rastreie uma solicitação específica em todo o seu caminho através de um sistema de microsserviços.

O Trace ID é gerado automaticamente pelo Sleuth quando uma solicitação é recebida por um serviço e é propagado por todas as chamadas subsequentes entre os serviços. Cada serviço adiciona um identificador adicional à solicitação chamado "Span ID", que é um identificador exclusivo para a operação que está sendo executada pelo serviço.

O Trace ID e o Span ID podem ser usados para correlacionar logs e outras informações de rastreamento em todos os serviços envolvidos em uma solicitação específica. Isso permite que os desenvolvedores sigam o caminho completo da solicitação em um ambiente de microsserviços e identifiquem rapidamente qualquer problema que possa ocorrer.

# Como imprementar

>[!error]+ Muita atenção
> O spring cloud sleuth não trabalha com a versão 3.x do Spring boot. Sua ultima versão durante a confecção deste documento só funciona na versão 2.x 

Para implementar o Spring Cloud Sleuth em sua aplicação, você pode seguir estes passos:

1.  Adicione a dependência do Sleuth em seu arquivo de manifesto de dependência (pom.xml, build.gradle ou similar), dependendo de qual ferramenta de construção você está usando. Por exemplo, para o Maven, adicione o seguinte trecho no arquivo pom.xml:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
```

>[!attention]+ Atenção
>Se a inserção da dependencia acima apresentar problemas pois está pedindo o campo version, basta adicionar a versão ou então adicionar a dependencia do spring cloud.


2.  Se você estiver usando o Spring Boot, o Sleuth será automaticamente configurado para você. Caso contrário, adicione a seguinte anotação na classe principal da sua aplicação:

```java
@SpringBootApplication
@EnableSleuth
public class MinhaAplicacao {
    // ...
}
```

3.  Configure o formato de rastreamento que você deseja usar. O Sleuth oferece suporte a vários formatos de rastreamento, incluindo o padrão "BAGGAGE", que usa cabeçalhos HTTP para propagar informações de rastreamento. Para usar esse formato, adicione a seguinte propriedade em seu arquivo de propriedades (application.properties ou application.yml):

```properties
spring.sleuth.baggage.remote-fields=baggage
```

4.  Teste o rastreamento. Depois de configurar o Sleuth em sua aplicação, você pode testar o rastreamento de solicitações em seu ambiente de microsserviços. Faça algumas solicitações e verifique se os IDs de rastreamento são consistentes em todos os serviços envolvidos.

Com esses passos, você poderá implementar o Spring Cloud Sleuth em sua aplicação e começar a rastrear solicitações em um ambiente de microsserviços.

## Padrão BAGGAGE

O padrão BAGGAGE é uma técnica usada pelo Spring Cloud Sleuth para propagar informações de rastreamento em uma solicitação HTTP por meio de cabeçalhos HTTP. Isso permite que as informações de rastreamento sejam transmitidas de forma transparente entre os serviços em um ambiente de microsserviços.

O padrão BAGGAGE é baseado no uso de campos de bagagem (baggage fields), que são campos personalizados adicionados aos cabeçalhos HTTP de uma solicitação. Os campos de bagagem podem conter informações personalizadas de rastreamento, como IDs de usuário, informações de contexto e outras informações relevantes para a operação sendo executada.

O Sleuth suporta o padrão BAGGAGE por meio da propriedade "spring.sleuth.baggage.remote-fields" no arquivo de propriedades. Ao definir essa propriedade, você pode especificar os campos de bagagem que devem ser propagados para outras solicitações HTTP.

Por exemplo, se você quiser propagar o campo "X-My-Header" para outras solicitações HTTP, adicione o seguinte ao seu arquivo de propriedades:

```properties
spring.sleuth.baggage.remote-fields=X-My-Header
```

Isso fará com que o Sleuth inclua o campo "X-My-Header" em todas as solicitações HTTP e permitirá que você acompanhe facilmente as informações de rastreamento relevantes para sua aplicação.

## Exemplo prático

Para testar na prática se o spring cloud sleuth está funcionando corretamente siga os passos abaixo:

1. Crie um projeto spring e adicione as dependencias necessárias:

```xml
<dependencies> 
<!-- Boot's Web support --> 
	<dependency> 
		<groupId>org.springframework.boot</groupId> 
		<artifactId>spring-boot-starter-web</artifactId>
	 </dependency> 
	 <!-- Sleuth with Brave tracer implementation --> 
	 <dependency> 
		 <groupId>org.springframework.cloud</groupId> 
		 <artifactId>spring-cloud-starter-sleuth</artifactId> 
	 </dependency> 
 </dependencies>
```

2.  Em seguida, adicione o seguinte código ao seu controlador REST:

```java
@RestController
public class SleuthController {

    private static final Logger log = LoggerFactory.getLogger(SleuthController.class);

    @Autowired
    private RestTemplate restTemplate;

    @GetMapping("/hello")
    public String hello() {
        log.info("Hello from SleuthController");
        String response = restTemplate.getForObject("http://localhost:8080/world", String.class);
        return "Hello, " + response + "!";
    }

    @GetMapping("/world")
    public String world() {
        log.info("World from SleuthController");
        return "World";
    }
}
```

Este controlador REST possui dois endpoints `/hello` e `/world`, e usa um objeto `RestTemplate` para fazer uma chamada HTTP do endpoint `/hello` para o endpoint `/world`.

Observe que estamos usando o `LoggerFactory` e o `Logger` do `org.slf4j` para criar um logger com o nome `SleuthController`. Isso permitirá que o Spring Cloud Sleuth intercepte as chamadas de logging e adicione informações de rastreamento a elas.

3.  Agora, inicie seu aplicativo Spring Boot e faça uma chamada HTTP para o endpoint `/hello`.
    
4.  Verifique se as informações de rastreamento estão sendo registradas em seu log centralizado ou em seu log de aplicativo. As mensagens de log do Spring Cloud Sleuth devem estar no formato "span=< nome do span>, traceId=< ID de rastreamento>, spanId=< ID do span>, ...". Certifique-se de que as informações de rastreamento (como o ID de rastreamento e o nome dos spans) estejam presentes em cada solicitação.

![[Pasted image 20230318154616.png]]

3.  Você também pode usar uma ferramenta de visualização de rastreamento, como o [[Zipkin]], para obter uma visão mais clara e detalhada de como as solicitações estão se movendo em sua arquitetura distribuída. Inicie o servidor [[Zipkin]] e navegue até a interface do usuário do [[Zipkin]] em seu navegador. Procure as solicitações que acabou de fazer e verifique se as informações de rastreamento estão presentes para cada solicitação.

	You can notice that the logging format has been updated with the following information `[SpringSleuth,13f9f90a23343679,13f9f90a23343679]`. This entry corresponds to `[application name,trace id, span id]`. The application name got read from the `SPRING_APPLICATION_NAME` environment variable.

Esse é um exemplo básico de como testar se o Spring Cloud Sleuth está funcionando corretamente em seu aplicativo Spring Boot. Você pode expandir esse exemplo adicionando mais serviços e endpoints para ver como o rastreamento funciona em uma arquitetura distribuída mais complexa.

>[!info]+
>Para que o nome da aplicação apareça no log acima é necessário [[Nomear uma aplicação no spring boot|dar um nome a ela]]

# Configurando o logback para exibir os indentificadores

Para configurar o Logback para imprimir de forma legível os identificadores do Sleuth, você pode adicionar uma entrada em seu arquivo de configuração do Logback (logback.xml ou logback-spring.xml) que define um novo padrão de impressão. Aqui está um exemplo:

```xml
<configuration>
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level [%X{traceId}/%X{spanId}] %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>
    
    <logger name="org.springframework.cloud.sleuth" level="DEBUG" additivity="false">
        <appender-ref ref="CONSOLE" />
    </logger>
    
    <root level="INFO">
        <appender-ref ref="CONSOLE" />
    </root>
</configuration>
```

Este exemplo configura o Logback para imprimir o Trace ID e o Span ID em cada linha de log gerada pelo Sleuth. A linha de padrão contém as seguintes partes:

-   `%d{yyyy-MM-dd HH:mm:ss.SSS}`: a data e hora em que a mensagem de log foi gerada
-   `[%thread]`: o nome da thread que gerou a mensagem de log
-   `%-5level`: o nível de log (por exemplo, DEBUG, INFO, WARN ou ERROR)
-   `[%X{traceId}/%X{spanId}]`: o Trace ID e o Span ID separados por uma barra (/)
-   `%logger{36}`: o nome do logger que gerou a mensagem de log (limitado a 36 caracteres)
-   `-%msg%n`: o texto da mensagem de log seguido por uma nova linha

Observe que a entrada `<logger>` é adicionada para configurar o nível de log e definir o appender que deve ser usado pelo Sleuth.

Com essa configuração, você poderá ver os IDs de rastreamento em cada linha de log gerada pelo Sleuth.

Para configurar o rollingFile do Logback para imprimir os identificadores de rastreamento do Sleuth, você pode adicionar uma entrada em seu arquivo de configuração do Logback (logback.xml ou logback-spring.xml) que define um novo appender do tipo `RollingFileAppender`. Aqui está um exemplo:

```xml
<configuration>
    <appender name="ROLLING_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>logs/myapp.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>logs/myapp.%d{yyyy-MM-dd}.log</fileNamePattern>
            <maxHistory>30</maxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level [%X{traceId}/%X{spanId}] %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>
    
    <logger name="org.springframework.cloud.sleuth" level="DEBUG" additivity="false">
        <appender-ref ref="ROLLING_FILE" />
    </logger>
    
    <root level="INFO">
        <appender-ref ref="ROLLING_FILE" />
    </root>
</configuration>
```

Neste exemplo, um `RollingFileAppender` é definido para gravar mensagens de log em um arquivo de log rotativo (rolling file) em vez de imprimir no console. A propriedade `file` define o nome do arquivo de log a ser criado, e a propriedade `rollingPolicy` define o padrão de rotação do arquivo. Aqui, o arquivo é rolado diariamente e mantido por até 30 dias.

A linha de padrão no elemento `encoder` contém os identificadores de rastreamento do Sleuth da mesma maneira que no exemplo anterior.

Observe que a entrada `<logger>` é adicionada para configurar o nível de log e definir o appender que deve ser usado pelo Sleuth.

Com essa configuração, os identificadores de rastreamento do Sleuth serão incluídos em cada mensagem de log gravada no arquivo de log rotativo.

# Entendendo mais sobre o span

O span é uma unidade única de trabalho que precisa ser iniciada e parada. Contem timing information, eventos e tags.

Você pode manipular o span através do uso da API da biblioteca.

O ciclo de vida do span segue os seguintes passos:

**start** - When you start a span, its name is assigned and the start timestamp is recorded
**end** - The span gets finished (the end time of the span is recorded) and, if the span is sampled, it is eligible for collection (e.g. to [[Zipkin]]).
**continue** - The span gets continued e.g. in another thread.

## Iniciando e finalizando Spans

You can manually create spans by using the `Tracer`, as shown in the following example:

```java
// Start a span. If there was a span present in this thread it will become 
// the `newSpan`'s parent. 
Span newSpan = this.tracer.nextSpan().name("calculateTax"); 
try (Tracer.SpanInScope ws = this.tracer.withSpan(newSpan.start())) { 
	// ... 
	// You can tag a span 
	newSpan.tag("taxValue", taxValue); 
	// o código a ser executado como um span aqui 
	// ...
	// You can log an event on a span 
	newSpan.event("taxCalculated"); 
} finally { 
	// Once done remember to end the span. This will allow collecting 
	// the span to send it to a distributed tracing system e.g. Zipkin 
	newSpan.end(); 
}
```

In the preceding example, we could see how to create a new instance of the span. If there is already a span in this thread, it becomes the parent of the new span.

>[!Attention]+ Atenção
>Always clean after you create a span.

Ou então você pode usar a anotação `@NewSpan` ou `@Span` antes do método que deseja representar como um span. Por exemplo:

```java
import org.springframework.cloud.sleuth.annotation.NewSpan;

// ...

public class MyService {
  
  @NewSpan("mySpanName")
  public void myMethod() {
    // o código a ser executado como um span aqui
  }

}
```

Nesse exemplo, o método `myMethod()` será executado como um novo span com o nome `mySpanName`.

Annotating the method without any parameter leads to creating a new span whose name equals the annotated method name.

If you provide the value in the annotation (either directly or by setting the `name` parameter), the created span has the provided value as the name.

```java
// method declaration 
@NewSpan(name = "customNameOnTestMethod5") 
void testMethod5(@SpanTag("testTag") String param); 

// and method execution 
this.testBean.testMethod5("test");
```

You can combine both the name and a tag. Let’s focus on the latter. In this case, the value of the annotated method’s parameter runtime value becomes the value of the tag. In our sample, the tag key is `testTag`, and the tag value is `test`.

## Continuando Spans

Sometimes, you do not want to create a new span but you want to continue one. An example of such a situation might be as follows:

If there was already a span created before an aspect was reached, you might not want to create a new span.

To continue a span, you can store the span in one thread and pass it on to another one as shown in the example below.

```java
Span spanFromThreadX = this.tracer.nextSpan().name("calculateTax"); 

try (Tracer.SpanInScope ws = this.tracer.withSpan(spanFromThreadX.start())) { 
	executorService.submit(() -> { 
		// Pass the span from thread X 
		Span continuedSpan = spanFromThreadX; 
		// ... 
		// You can tag a span 
		continuedSpan.tag("taxValue", taxValue); 
		// ... 
		// You can log an event on a span 
		continuedSpan.event("taxCalculated"); 
	}).get(); 
} finally { 
	spanFromThreadX.end(); 
}
```

If you want to add tags and annotations to an existing span, you can use the `@ContinueSpan` annotation, as shown in the following example:

```java
// method declaration 
@ContinueSpan(log = "testMethod11") 
void testMethod11(@SpanTag("testTag11") String param); 

// method execution 
this.testBean.testMethod11("test");
this.testBean.testMethod13();
```

(Note that, in contrast with the `@NewSpan` annotation ,you can also add logs with the `log` parameter.)

That way, the span gets continued and:

-   Log entries named `testMethod11.before` and `testMethod11.after` are created.
    
-   If an exception is thrown, a log entry named `testMethod11.afterFailure` is also created.
    
-   A tag with a key of `testTag11` and a value of `test` is created.

# Nomear e adicionar tags ao span

Adicionar tags e nomear spans no Sleuth pode ajudar a tornar a análise e a depuração de uma aplicação distribuída mais fácil e eficiente. Aqui estão algumas maneiras pelas quais esses recursos podem ser úteis:

-   **Identificar informações relevantes:** tags podem ser usadas para adicionar informações relevantes a um span, como o nome do usuário que iniciou a solicitação ou o ID do pedido. Essas informações podem ser usadas posteriormente para pesquisar ou filtrar spans com base nesses dados.
    
-   **Fornecer contexto adicional:** nomear um span pode ajudar a fornecer contexto adicional sobre o que está acontecendo em uma determinada parte da aplicação. Por exemplo, se houver um span para a execução de uma consulta de banco de dados, o nome do span pode ser definido como "db.query" para indicar que a consulta está sendo executada.
    
-   **Facilitar a depuração:** com nomes de spans significativos e tags bem definidas, é mais fácil rastrear o fluxo de uma solicitação por toda a aplicação. Isso pode ser particularmente útil quando ocorrem erros ou problemas de desempenho, permitindo que os desenvolvedores identifiquem rapidamente a origem do problema e tomem medidas corretivas.
    
-   **Integração com ferramentas de monitoramento:** tags e nomes de spans também podem ser usados para integrar o Sleuth com ferramentas de monitoramento, como o [[Zipkin]]. Essas ferramentas podem usar as informações adicionais fornecidas pelos tags e nomes para gerar gráficos, alertas e outros insights úteis sobre a operação da aplicação.
    

No geral, o uso de tags e nomes de spans pode ajudar a fornecer um contexto mais completo e fácil de entender para o rastreamento distribuído em uma aplicação, o que pode ser inestimável durante a depuração e análise de problemas.

Para nomear um span no Sleuth, você pode usar a anotação `@NewSpan` ou `@Span` do Sleuth em torno do método que deve ser representado como um span. O nome do span é definido pelo valor do atributo `name` na anotação.

Por exemplo:

```java
@NewSpan("mySpanName")
public void myMethod() {
    // código a ser executado como um span
}
```

Neste exemplo, o método `myMethod()` será executado como um span nomeado "mySpanName". Você pode definir o nome do span de acordo com a funcionalidade que está sendo executada no método.

Os spans podem ser visualizados e rastreados usando ferramentas como o [[Zipkin]]. O Sleuth pode enviar dados de rastreamento para o [[Zipkin]] por meio de um servidor [[Zipkin]], que pode ser configurado em sua aplicação. Depois que o [[Zipkin]] é configurado, você pode acessar a interface da web do [[Zipkin]] para visualizar os dados de rastreamento. Lá, você pode pesquisar e filtrar spans com base em seus nomes, tags e outros atributos.

Por exemplo, se você nomeou um span como "mySpanName", pode pesquisá-lo na interface do [[Zipkin]] e ver todos os outros spans associados a ele, permitindo que você rastreie a solicitação de uma maneira mais completa.

