#microsserviços #Monitoramento #springboot 

Biblioteca do spring que permite a obtenção de métricas e status da aplicação. Essa biblioteca também facilita a coleta de dados por parte de outras aplicações de monitoramento.

Actuator é principalmente usado para **expor informações operacionais sobre aplicações em execução** - health, metrics, info, env, etc. Faz uso de endpoints HTTP ou [[beans JMX]] para nos permitir interagir com as métricas.

Para usar o actuator é necessário adicionar sua dependencia ao pom

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

O Actuator disponibiliza vários endpoints para a obtenção de informações sobre a aplicação, mas por padrão somente poucos ficam disponíveis.

Para disponibilizar todos os endpoints configure seu properties com o código abaixo:

```yaml
management:
  endpoints:
    web:
      exposure:
        include:
        - '*'
```

Também é possível usar a propriedade acima para expor somente alguns endpoints.

Para visualizar todos os endpoints disponíveis acesse o endereço em que sua aplicação está rodando e digite após a ''/'' actuator.

![[Pasted image 20230222134809.png]]

Cada um desses endpoints tem sua própria função. Nós podemos acessar cada um desses endpoints individualmente.

Por padrão todos os endpoints são colocados depois do path actuator, mas podemos alterar este path com a propriedade:

```yaml
management:
	endpoints:
		web:
			base-path: /manage
```

## Endpoint Health

Exibe o status da aplicação. Se a aplicação estiver saudável e executando sem qualquer tipo de interrupção os status será UP. Para acessar o endpoint digite **/actuator/health**.

![[Pasted image 20230226125816.png]]
By default, Spring Boot defines four different values as the health Status:

-   _UP —_ The component or subsystem is working as expected
-   _DOWN_ — The component is not working
-   _OUT_OF_SERVICE_ — The component is out of service temporarily
-   _UNKNOWN_ — The component state is unknown

**The health status affects the HTTP status code of the health endpoint**. By default, Spring Boot maps the _DOWN_, and _OUT_OF_SERVICE_ states to throw a 503 status code. On the other hand, _UP_ and any other unmapped statuses will be translated to a 200 OK status code.

Para customizar o mapeamento dos endpoint de acordo com os status que desejamos, nós podemos configurar no properties:

```YAML
management:
	endpoint:
		health:
			status:
				http-mapping.down: 500
				http-mapping.out_of_service: 503
				http-mapping.warning: 500
```

Também podemos implementar essas mudanças através de código:

```java
@Component
public class CustomStatusCodeMapper implements HttpCodeStatusMapper {

    @Override
    public int getStatusCode(Status status) {
        if (status == Status.DOWN) {
            return 500;
        }
        
        if (status == Status.OUT_OF_SERVICE) {
            return 503;
        }
        
        if (status == Status.UNKNOWN) {
            return 500;
        }

        return 200;
    }
}
```



## Endpoint metrics

Lista todas as metricas disponíveis para rastrear a aplicação. Basta digitar **/actuator/metrics**.

![[Pasted image 20230226130211.png]]

Para obter detalhes sobre uma métrica você precisa passar o nome da métrica na URL como no formato: 
http://localhost:8080/actuator/metrics/{MetricName}.

Por exemplo, para obter detalhes da metrica **system.cpu.usage**, use a URL:
http://localhost:8080/actuator/metrics/system.cpu.usage

A sáida deverá ser algo como abaixo:

![[Pasted image 20230226131837.png]]

## Outros endpoints predefinidos

Outros endpoints disponíveis:
-   _/auditevents_ lists security audit-related events such as user login/logout. Also, we can filter by principal or type among other fields.
-   _/beans_ returns all available [[beans]] in our _[[BeanFactory]]_. Unlike _/auditevents_, it doesn't support filtering.
-   _/conditions_, formerly known as /_autoconfig_, builds a report of conditions around autoconfiguration.
-   _/configprops_ allows us to fetch all _@ConfigurationProperties_ beans.
-   _/env_ returns the current environment properties. Additionally, we can retrieve single properties.
-   _/flyway_ provides details about our [[Flyway]] database migrations.
-   _/health_ summarizes the health status of our application.
-   _/heapdump_ builds and returns a heap dump from the [[JVM]] used by our application.
-   _/info_ returns general information. It might be custom data, build information or details about the latest commit.
-   _/liquibase_ behaves like _/flyway_ but for [[Liquibase]].
-   _/logfile_ returns ordinary application logs.
-   _/loggers_ enables us to query and modify the [[|logging level]] of our application.
-   _/metrics_ details metrics of our application. This might include generic metrics as well as custom ones.
-   _/prometheus_ returns metrics like the previous one, but formatted to work with a [[Prometheus]] server.
-   _/scheduledtasks_ provides details about every scheduled task within our application.
-   _/sessions_ lists HTTP sessions given we are using [[Spring Session]].
-   _/shutdown_ performs a graceful shutdown of the application.
-   _/threaddump_ dumps the thread information of the underlying [[JVM]].

## Health indicators

No Spring Boot, os indicadores de saúde (health indicators) são componentes que fornecem informações sobre o estado de saúde da aplicação. Os indicadores de saúde são usados para monitorar o status da aplicação e relatar seu estado atual. Eles são especialmente úteis em ambientes de produção onde é crítico garantir que a aplicação esteja funcionando sem problemas.

Os indicadores de saúde podem ser usados para fornecer informações sobre vários aspectos da saúde da aplicação, como conexão com o banco de dados, conectividade com filas de mensagens ou até mesmo métricas específicas do aplicativo.

Cada indicador de saúde retorna um status que indica se o componente está saudável, não saudável ou em um estado desconhecido.

O Spring Boot fornece um conjunto de indicadores de saúde integrados que cobrem casos de uso comuns, como verificar a conectividade do banco de dados, espaço em disco e uso da CPU. Você também pode criar indicadores de saúde personalizados para fornecer informações sobre aspectos específicos de sua aplicação.

Os indicadores de saúde são expostos por meio de um ponto de extremidade HTTP `/actuator/health`, que retorna uma resposta JSON contendo o status de todos os indicadores de saúde registrados. Este ponto de extremidade pode ser usado por ferramentas de monitoramento para verificar periodicamente a saúde da aplicação e alertar os administradores se houver algum problema detectado.

>[!attention]- Atenção
>Health Indicators devem ser utilizados para verificar se a aplicação consegue se comunicar com outros componentes como um banco de dados [[Cassandra]] e uma aplicação de fila como o [[Apache Kafka]]. Se a comunicação falha ou fica lenta então temos uma aplicação não saudável que deveria receber atenção, em outras palavras estes indicadores devem ser utilizados para reportar a saúde de diferentes componentes so sistema. 

## Bult-in health indicators

Spring Boot provides several built-in health indicators that you can use to check the health of your application and its dependencies. These health indicators are automatically registered with the `/actuator/health` endpoint and included in the JSON response.

Here are some of the built-in health indicators provided by Spring Boot:

1.  DiskSpaceHealthIndicator: Checks the available disk space.
  
2.  DataSourceHealthIndicator: Checks the health of the configured data source.

3.  RedisHealthIndicator: Checks the health of [[Redis]].
   
4.  MongoHealthIndicator: Checks the health of [[MongoDB]].
   
5.  RabbitHealthIndicator: Checks the health of [[RabbitMQ]].
   
6.  KafkaHealthIndicator: Checks the health of [[Apache Kafka]].
 
7.  CassandraHealthIndicator: Checks the health of [[Cassandra]].
   
8.  SolrHealthIndicator: Checks the health of [[Apache Solr]].
   
9.  ElasticsearchHealthIndicator: Checks the health of [[Elasticsearch]].

> [!attention]
> Alguns built-in health só ficam disponíveis quando certas condições são alcançadas como é o caso do **CassandraHealthIndicator**, que só deve aparecer em caso do banco cassandra estiver sendo usado com a aplicação 
 

These built-in health indicators provide a quick and easy way to check the health of your application and its dependencies without having to write any custom code. You can also create your own custom health indicators, as I described earlier, to check the health of other components in your application.

To use the built-in health indicators in Spring Boot, you don't need to do anything special. They are automatically registered with the `/actuator/health` endpoint and included in the JSON response.

Here's an example of how to access the health endpoint using `curl`:

```bash
$ curl http://localhost:8080/actuator/health
```

This will return a JSON response containing the status and details (se o mesmo foi [[Spring Actuator#Visualizando detalhes do health indicator|configurado no properties]] para ser exibido) of all registered health indicators. For example, if you have a DataSourceHealthIndicator registered, the response might look like this:

```json
{
  "status": "UP",
  "details": {
    "db": {
      "status": "UP",
      "details": {
        "database": "MySQL",
        "hello": 1
      }
    },
    "diskSpace": {
      "status": "UP",
      "details": {
        "total": 500107862016,
        "free": 334231410432,
        "threshold": 10485760
      }
    },
    "refreshScope": {
      "status": "UP"
    },
    "rabbit": {
      "status": "UP",
      "details": {
        "version": "3.6.15"
      }
    },
    "solr": {
      "status": "UP",
      "details": {
        "version": "6.6.0"
      }
    }
  }
}
```

## Criando seus próprios health indicators

You can create a custom health indicator in Spring Boot by implementing the `HealthIndicator` interface. The `HealthIndicator` interface has a single method `health()`, which returns a `Health` object representing the status of the health indicator.

Here's an example of how to create a custom health indicator:

```java
@Component
public class CustomHealthIndicator implements HealthIndicator {

    @Override
    public Health health() {
        // perform some health check logic here
        boolean isHealthy = true;

        if (isHealthy) {
            return Health.up().withDetail("message", "Custom health check succeeded").build();
        } else {
            return Health.down().withDetail("message", "Custom health check failed").build();
        }
    }
}

```

In this example, we create a `CustomHealthIndicator` class that implements the `HealthIndicator` interface. The `health()` method performs some custom health check logic and returns a `Health` object that represents the status of the health indicator.

If the health check succeeds, we return a `Health` object with an "UP" status and a custom message using the `withDetail()` method. If the health check fails, we return a `Health` object with a "DOWN" status and a custom message.

Once you've created your custom health indicator, it will be automatically registered with Spring Boot's health endpoint `/actuator/health` and included in the JSON response. You can also use the `@HealthIndicator` annotation to give your custom health indicator a specific name that will be included in the response.


> [!attention]- atenção
> **In reactive applications, however, we should register a bean of type [[ReactiveHealthIndicator]]**. The reactive _health()_ method returns a [[Mono<Health>]] instead of a simple _Health_. Other than that, other details are the same for both web application types.

>[!info]- Informação adicional
>Você pode acessar o seu health indicator customizado apenas digitando o nome da classe sem a parte **HealthIndicator** como no exemplo: `http://localhost:8080/actuator/health/custom`. Por padrão o actuator nomeia o seu health indicator com o nome da classe sem o sufixo **HealthIndicator**, para mudar isso você pode colocar no **@component** o nome que deseja dar ao endpoint: `@component("random")`.

>[!Info]- Informação adicional
>Podemos adicionar qualquer outras informações no objeto **Health** retornado pela classe customizada do **healthIndicator**, para isso basta adicionar no método **withDetails** um par de chave e valor. No exemplo acima adicionamos o par `message` e a `mensagem enviada`, mas podemos adicionar mais de um **withDetails** e com outros valores. 

>[!Example]- Examplo:
>
```java
public Health health() {
    double chance = ThreadLocalRandom.current().nextDouble();
    Health.Builder status = Health.up();
    if (chance > 0.9) {
        status = Health.down();
    }

    return status
      .withDetail("chance", chance)
      .withDetail("strategy", "thread-local")
      .build();
}
```

> [!info]- 
> Também podemos alcançar o mesmo resultado fazendo uso do objeto **map<String, Object>** para **withDetails(map)**.

>[!Example]- Exemplo
>
```java
Map<String, Object> details = new HashMap<>();
details.put("chance", chance);
details.put("strategy", "thread-local");
        
return status.withDetails(details).build();
```

## Visualizando detalhes do health indicator

Para poder visualizar os detalhes de um health indicator é necessário habilitar no properties como no código abaixo:

```YAML
management:
  endpoints:
    web:
     exposure:
       include:
        - '*'
  endpoint:
    health:
      show-details: always
```

Com essa configuração ao acessar o endpoint `/actuator/health` mais informações serão exibidas incluindo dados dos bult-in health indicators.

Também será possível visualizar e acessar os built-in health indicators disponíveis pelo seu respectivo endpoint seguindo formato `/actuator/health/diskSpace`.

![[Pasted image 20230228084747.png]]

## Desativando o indicator
Para desativar um indicator em particular precisamos adicioanar a configuração abaixo no properties.

```YAML
management:
	health:
		random:
			enabled: false
```

Em seguida configurar na classe a anotação abaixo:

```java
@Component
@ConditionalOnEnabledHealthIndicator("random")
public class RandomHealthIndicator implements HealthIndicator { 
    // omitted
}
```

Com estas configurações, ao acessar o endpoint do indicator será apresentado a mensagem ==404 Not Found==.

## Acessando o actuator através do jmx

O Spring Boot Actuator fornece endpoints para monitorar e gerenciar a aplicação, como `/health`, `/metrics` e `/env`. Esses endpoints são expostos como [[beans JMX|MBean]], o que significa que podem ser monitorados e gerenciados por meio do protocolo [[JMX]].

Para expor as informações de monitoramento do Actuator como MBeans, basta adicionar a dependência `spring-boot-starter-actuator` ao projeto e definir a propriedade `spring.jmx.enabled=true` no arquivo `application.properties`.

Por exemplo, para expor as informações de saúde (`/health`) como um MBean, adicione a seguinte configuração ao arquivo `application.properties`:

```YAML
spring:
	jmx:
		enabled: true
management:
	endpoints:
		jmx:
			exposure:
				include: 
				- health
```

Isso expõe as informações de saúde como um [[beans JMX|MBean]] com o nome `org.springframework.boot:type=Endpoint,name=HealthEndpoint`. Você pode usar um cliente [[JMX]] para se conectar ao servidor [[JMX]] e monitorar o estado de saúde da aplicação.

Além disso, o Actuator também permite que você customize o nome e a descrição do [[beans JMX|MBean]]. Por exemplo, para definir o nome do [[beans JMX|MBean]] como `myapp:type=Health`, adicione a seguinte configuração ao arquivo `application.properties`:

```YAML
spring:
	jmx:
		enabled: true
management:
	endpoints:
		jmx:
			exposure:
				include:
				- health
		unique-names: true
		static-names: 
		 - "myapp:type=Health"
```

Com essa configuração, o [[beans JMX |MBean]] de saúde será registrado com o nome `myapp:type=Health`.

Dessa forma, é possível expor informações de monitoramento do Actuator como MBeans e monitorá-las por meio do protocolo [[JMX]].