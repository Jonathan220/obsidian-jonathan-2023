#microsserviços 

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

## Endepoint metrics

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
-   _/loggers_ enables us to query and modify the [[logging level]] of our application.
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

