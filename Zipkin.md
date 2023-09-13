#microsserviços #Monitoramento 


# O que é

Zipkin é uma ferramenta de rastreamento distribuído de código aberto desenvolvida pela Twitter. Ela ajuda a coletar, visualizar e pesquisar rastros de transações distribuídas, como solicitações de serviço, em uma arquitetura de microservices. Zipkin suporta vários protocolos e formatos de rastreamento, incluindo OpenTracing, OpenCensus, [[Spring Cloud Sleuth]] e Brave.

Com o Zipkin, você pode visualizar a latência e a causa-raiz de solicitações de serviço, rastrear solicitações em vários serviços e ver o tempo que cada serviço demorou para processar a solicitação. Além disso, o Zipkin permite filtrar, pesquisar e analisar rastreamentos de transações distribuídas para depurar problemas e otimizar o desempenho do sistema.

Zipkin consiste em vários componentes, incluindo um servidor de coleta, um serviço de armazenamento, uma interface da web e um conjunto de clientes para diferentes linguagens de programação. O servidor de coleta recebe os rastros de transações distribuídas dos clientes e os armazena em um banco de dados. A interface da web permite visualizar e pesquisar os rastros armazenados. Os clientes são usados para instrumentar o código do aplicativo e enviar os rastros de transações para o servidor de coleta.

Zipkin é amplamente utilizado em arquiteturas de microservices e pode ser integrado com várias ferramentas e serviços, como Kubernetes, Docker, Istio e Prometheus.

O Zipkin suporta diversas plataformas, o que acaba possibilitando diversos cenários de instrumentação, dentre elas JAVA, Python, Go, Ruby, PHP, entre outras.

Informações de tracing são coletadas em cada hosting usando as bibliotecas de intrumentação e envidas para o zipkin. Quando um host envia uma requisição para outra aplicação é passado alguns identificadores de tracing junto com a requisição para o zipkin e depois podemos ligar estes dados com os spans.

A UI do Zipkin trás visões interessantes no que diz respeito a nossa aplicação, entre elas um “mapa de serviços”, que mostra a quantidade de requisições, quantas ocorreram erro, chamadas para algum serviço que não deveria, etc.

![[Pasted image 20230407152353.png]]

Para instrumentar esses dados no Zipkin é bem simples, poder ser feito através de coletor de dados via HTTP, mas é possível fazer isso via Kafka, ActiveMQ, RabbitMQ, etc.

Ele possui 4 componentes:

**Coletores:** Depois que os dados chegam ao daemon do coletor Zipkin, eles são validados, armazenados e indexados para pesquisas pelo coletor Zipkin.

**Storage**: É o banco de dados que será utilizado para armazenar os dados, por padrão é o MySQL, mas caso você deseje ter mais desempenho, é recomendado a utilização do Cassandra. Existem outras opções que podem ser utilizadas também.

**Query Service**: Basicamente a barra de busca para uma determinada informação, onde você define os filtros, tempo e a quantidade de amostragem.

**Interface WEB**: É a interface propriamente dita, que inclusive é bem simples e objetiva.

# Como implementa-lo

Para implementar o Zipkin em uma aplicação, você precisa seguir os seguintes passos:

1. Configurar um servidor zipkin

A maneira mais rápida de iniciar é baixar e executar o servidor Zipkin como um jar executável independente. Zipkin server requer o uso minimo do JRE 8.

```bash
curl -sSL https://zipkin.io/quickstart.sh | bash -s
java -jar zipkin.jar
```

Você pode também iniciar via docker, que é a maneira preferida.

```bash
docker run -d -p 9411:9411 openzipkin/zipkin
```

A versão com docker pode ser feita com uma stack de tecnologias já embutidas como banco de dados para armazenamento e ferramentas de monitoramento como [[Prometheus]] e [[Grafana]], abaixo listo agumas delas que dependem do uso de docker-compose.

Uma vez que o servidor está em execução você pode ver o traços através da interface web no endereço: http://localhost:9411/zipkin/.

2. Instrumentar a aplicação cliente.
O próximo passo é adicionar as dependências do Zipkin: Você precisa adicionar as dependências do Zipkin ao seu projeto. Existem bibliotecas clientes disponíveis para várias linguagens de programação, incluindo Java, Node.js, Python, Go, entre outras. No caso do Java, você pode usar o cliente [[Spring cloud sleuth|Spring Cloud Sleuth]], que já inclui o Zipkin como um transportador.

```xml
<!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-sleuth -->
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>

<!-- https://mvnrepository.com/artifact/io.zipkin.reporter2/zipkin-sender-okhttp3 -->
<dependency>
	<groupId>io.zipkin.reporter2</groupId>
	<artifactId>zipkin-sender-okhttp3</artifactId>
</dependency>
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter</artifactId>
</dependency>
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-sleuth-zipkin</artifactId>
</dependency>
```

Por fim, instrumentar o código do aplicativo: Você precisa instrumentar o código do aplicativo para coletar e enviar rastros de transações para o servidor Zipkin. Isso pode ser feito com a ajuda das bibliotecas de clientes do Zipkin mencionadas anteriormente. No caso do Spring Cloud Sleuth, por exemplo, você só precisa adicionar a dependência ao seu projeto e configurar a URL do servidor Zipkin no arquivo de propriedades.

```properties
spring.zipkin.base-url=http://localhost:9411/
spring.application.name=my-application
spring.sleuth.sampler.probability=1.0
```

A propriedade `spring.zipkin.base-url=http://localhost:9411/` não é necessária se o servidor estiver rodando localmente, a aplicação utiliza o endereço padrão localhost:9411 para enviar os traces.

Por padrão os traces são enviados via HTTP para o zipkin no localhost:9411. Se sua aplicação depende de `spring-rabbit` ou `spring-kafka`, os traces serão enviados para o broker ao invés do http.

Ao iniciar sua aplicação no spring boot, é possível visualizar através dos logs se a mesma conseguiu se conectar com o servidor. As mensagens abaixo exibem o que seria uma conexão bem sucedida.

```log
2023-04-08 16:43:00.659 [main] INFO  [traceId = /spanId = ] o.s.b.w.s.c.ServletWebServerApplicationContext - Root WebApplicationContext: initialization completed in 4236 ms
2023-04-08 16:43:01.345 [pool-1-thread-1] DEBUG [traceId = /spanId = ] o.s.c.s.z.CachingZipkinUrlExtractor - The port in Zipkin's URL [http://localhost:9411/] is provided - that means that load balancing will not take place
2023-04-08 16:43:01.360 [pool-1-thread-1] DEBUG [traceId = /spanId = ] o.s.c.s.z.ZipkinRestTemplateWrapper - HTTP POST http://localhost:9411/api/v2/spans
2023-04-08 16:43:01.365 [pool-1-thread-1] DEBUG [traceId = /spanId = ] o.s.c.s.z.ZipkinRestTemplateWrapper - Accept=[text/plain, application/json, application/*+json, */*]
2023-04-08 16:43:01.376 [pool-1-thread-1] DEBUG [traceId = /spanId = ] o.s.c.s.z.ZipkinRestTemplateWrapper - Writing [[B@45a4b7a0] as "application/json"
2023-04-08 16:43:01.793 [pool-1-thread-1] DEBUG [traceId = /spanId = ] o.s.c.s.z.ZipkinRestTemplateWrapper - Response 202 ACCEPTED
2023-04-08 16:43:01.800 [pool-1-thread-1] DEBUG [traceId = /spanId = ] o.s.c.s.a.z.ZipkinAutoConfiguration - Check result of the [RestTemplateSender{http://localhost:9411/api/v2/spans}] is [CheckResult{ok=true, error=null}]

```

Depois que o servidor Zipkin estiver em execução e o código do aplicativo for instrumentado corretamente, você pode visualizar os rastros de transações no navegador usando a interface da web do Zipkin. A interface da web permite filtrar, pesquisar e analisar os rastros de transações distribuídas para depurar problemas e otimizar o desempenho do sistema.

Para verificar se os dados de tracing estão sendo enviados corretamente para a aplicação você pode verificar se nos logs as seguinte mensagem aparece, lembrando que o log do zipkin deve estar em debug.

```log
2023-04-08 17:17:15.433 [Thread-2] INFO  [traceId = /spanId = ] o.s.c.s.a.z.ZipkinAutoConfiguration - Flushing remaining spans on shutdown
2023-04-08 17:17:15.434 [Thread-2] DEBUG [traceId = /spanId = ] o.s.c.s.z.CachingZipkinUrlExtractor - The port in Zipkin's URL [http://localhost:9411/] is provided - that means that load balancing will not take place
2023-04-08 17:17:15.435 [Thread-2] DEBUG [traceId = /spanId = ] o.s.c.s.z.ZipkinRestTemplateWrapper - HTTP POST http://localhost:9411/api/v2/spans
2023-04-08 17:17:15.436 [Thread-2] DEBUG [traceId = /spanId = ] o.s.c.s.z.ZipkinRestTemplateWrapper - Accept=[text/plain, application/json, application/*+json, */*]
2023-04-08 17:17:15.437 [Thread-2] DEBUG [traceId = /spanId = ] o.s.c.s.z.ZipkinRestTemplateWrapper - Writing [[B@68854428] as "application/json"
2023-04-08 17:17:15.464 [Thread-2] DEBUG [traceId = /spanId = ] o.s.c.s.z.ZipkinRestTemplateWrapper - Response 202 ACCEPTED
```

# Implementação em Docker

This project is configured to run docker containers using [[docker-compose]]. Note that the default configuration requires docker-compose 1.6.0+ and docker-engine 1.10.0+.

To start the default docker-compose configuration, run:

```bash
docker-compose up
```

View the web UI at $(docker ip):9411. Traces are stored in memory.

To see specific traces in the UI, select "zipkin-server" in the dropdown and then click the "Find Traces" button.

## Elasticsearch

You can store traces in [Elasticsearch](https://github.com/openzipkin/zipkin/blob/master/docker/test-images/zipkin-elasticsearch7/README.md) instead of memory, using the `docker-compose-elasticsearch.yml` file. This configuration starts `zipkin`, `zipkin-elasticsearch` and `zipkin-dependencies` (cron job) in their own containers.

To start the Elasticsearch-backed configuration, run:

```shell
docker-compose -f docker-compose-elasticsearch.yml up
```

## Cassandra

You can store traces in [Cassandra](https://github.com/openzipkin/zipkin/blob/master/docker/test-images/zipkin-cassandra/README.md) instead of memory, using the `docker-compose-cassandra.yml` file. This configuration starts `zipkin`, `zipkin-cassandra` and `zipkin-dependencies` (cron job) in their own containers.

To start the Cassandra-backed configuration, run:

```shell
docker-compose -f docker-compose-cassandra.yml up
```

## MySQL

You can store traces in [MySQL](https://github.com/openzipkin/zipkin/blob/master/docker/test-images/zipkin-mysql/README.md) instead of memory, using the `docker-compose-mysql.yml` file. This configuration starts `zipkin`, `zipkin-mysql` and `zipkin-dependencies` (cron job) in their own containers.

To start the MySQL-backed configuration, run:

```shell
$ docker-compose -f docker-compose-mysql.yml up
```

## Kafka

You can collect traces from [Kafka](https://github.com/openzipkin/zipkin/blob/master/docker/test-images/zipkin-kafka/README.md) in addition to HTTP, using the `docker-compose-kafka.yml` file. This configuration starts `zipkin` and `zipkin-kafka` in their own containers.

To add Kafka configuration, run:

```shell
$ docker-compose -f docker-compose-kafka.yml up
```

Then configure the [Kafka sender](https://github.com/openzipkin/zipkin-reporter-java/blob/master/kafka11/src/main/java/zipkin2/reporter/kafka11/KafkaSender.java) using a `bootstrapServers` value of `host.docker.internal:9092` if your application is inside the same docker network or `localhost:19092` if not, but running on the same host.

In other words, if you are running a sample application on your laptop, you would use `localhost:19092` bootstrap server to send spans to the Kafka broker running in Docker.


## Prometheus

Zipkin comes with a built-in Prometheus metric exporter. The `docker-compose-prometheus.yml` file starts Prometheus configured to scrape Zipkin, exposes it on port `9090`. You can open `$DOCKER_HOST_IP:9090` and start exploring metrics (available on the `/prometheus` endpoint of Zipkin).

`docker-compose-prometheus.yml` also starts a Grafana with authentication disabled, exposing it on port 3000. On startup it's configured with the Prometheus instance started by `docker-compose` as a data source, and imports the dashboard published at [https://grafana.com/dashboards/1598](https://grafana.com/dashboards/1598). This means that, after running `docker-compose ... -f docker-compose-prometheus.yml up`, you can open `$DOCKER_IP:3000/dashboard/db/zipkin-prometheus` and play around with the dashboard.

