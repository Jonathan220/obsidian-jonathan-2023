#springboot #microsserviços 

# O que é

O Eureka é um serviço de registro e descoberta de serviços desenvolvido pelo Spring Cloud, que faz parte do ecossistema do Spring Boot. Ele permite que os aplicativos cliente localizem facilmente os serviços necessários sem precisar saber detalhes sobre o endereço físico ou a porta do servidor onde o serviço está sendo executado.

Em outras palavras, o Eureka é uma solução para implementar o balanceamento de carga e a escalabilidade horizontal em uma arquitetura de microserviços. Ele funciona como um catálogo de serviços, onde cada serviço é registrado com um nome único e as informações sobre sua localização (endereço IP, porta etc.). Os clientes, por sua vez, podem consultar o Eureka para obter informações atualizadas sobre os serviços disponíveis e sua localização, permitindo que eles se comuniquem com outros serviços de forma transparente.

O Eureka é um componente importante do ecossistema do Spring Boot e é frequentemente usado em conjunto com outras ferramentas do Spring Cloud, como o Ribbon (balanceamento de carga do lado do cliente) e o Hystrix (tolerância a falhas e resiliência).

Existem outros serviços de registro e descoberta que podem ser usados com o Spring Boot, além do Eureka. Alguns exemplos são:

1.  Consul: é uma ferramenta de registro e descoberta de serviços da HashiCorp, que oferece recursos adicionais, como DNS e monitoramento de integridade.
    
2.  ZooKeeper: é um serviço de coordenação distribuída da Apache que pode ser usado como um registro de serviços.
    
3.  Etcd: é um banco de dados de chave-valor distribuído que pode ser usado como um registro de serviços.
    
4.  Kubernetes: é uma plataforma de orquestração de contêineres que inclui um serviço de descoberta de serviços chamado "Kubernetes Service".
    

O Spring Cloud suporta todos esses serviços de registro e descoberta, além do Eureka. Portanto, você pode escolher aquele que melhor atende às suas necessidades e integrá-lo facilmente com o Spring Boot usando as bibliotecas do Spring Cloud.

>[!info]+ Mais detalhes
>Imagine que você está em uma grande festa com muitos amigos. Alguns amigos estão brincando jogos na sala de jogos, outros estão comendo bolo na cozinha e outros estão assistindo filmes na sala de estar.
>
>Agora, imagine que você quer jogar um jogo com seus amigos, mas não sabe em qual sala eles estão jogando. O que você faz? Você pode perguntar a alguém onde eles estão ou pode procurar cada sala até encontrá-los. Mas isso pode levar muito tempo e você pode não encontrar todos os seus amigos.
>
>O Eureka é como um mapa da festa que mostra onde todos os seus amigos estão. Cada amigo tem uma etiqueta com seu nome e a sala em que estão. Então, quando você quer jogar um jogo, pode olhar no mapa do Eureka e ver onde seus amigos estão. Isso facilita muito encontrar seus amigos e jogar o jogo com eles.
>
>No mundo da tecnologia, os "amigos" são serviços de software que precisam se comunicar uns com os outros. Eles podem estar em diferentes computadores ou servidores. O Eureka funciona como o mapa da festa, mas para serviços de software. Cada serviço se registra no Eureka com um nome e informações sobre sua localização, como endereço IP e número da porta.
>
>Quando um serviço precisa se comunicar com outro serviço, ele consulta o Eureka para descobrir onde o serviço está e como se comunicar com ele. Isso torna mais fácil para os serviços se encontrarem e se comunicarem, sem precisar saber exatamente onde o outro serviço está fisicamente.

# Como implementá-lo

Para implementar o Eureka no seu projeto Spring Boot, você precisa adicionar as dependências do Spring Cloud Netflix Eureka no seu arquivo pom.xml (ou build.gradle, se você estiver usando o Gradle) e, em seguida, configurar o Eureka como um servidor ou um cliente, dependendo da sua necessidade.

## Eureka server

```xml
<!-- Dependências do Spring Cloud Netflix -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
    </dependency>
```

Este exemplo adiciona as dependências do Spring Boot e do Eureka Server no seu projeto. Agora, você precisa configurar o Eureka como um servidor ou um cliente.

Para configurar o Eureka como um servidor, você precisa adicionar a anotação `@EnableEurekaServer` na classe principal do seu aplicativo Spring Boot. Isso diz ao Spring para configurar o Eureka como um servidor:

```java
@SpringBootApplication
@EnableEurekaServer
public class EurekaServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(EurekaServerApplication.class, args);
    }

}
```

Depois disso, você precisa configurar algumas propriedades do Eureka no seu arquivo application.properties (ou application.yml), como o nome do servidor, o endereço e a porta:

```yaml
server:
  port: 8761
spring:
  application:
    name: eureka-server
```

## Eureka client

Para configurar o Eureka como um cliente, você precisa adicionar a dependência do Eureka Client no seu arquivo pom.xml (ou build.gradle):

```xml
<!-- Dependências do Spring Cloud Netflix -->
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>

```

Depois disso, você precisa adicionar a anotação `@EnableDiscoveryClient` na classe principal do seu aplicativo Spring Boot. Isso diz ao Spring para configurar o Eureka como um cliente:

```java
@SpringBootApplication
@EnableDiscoveryClient
public class MyAppApplication {

    public static void main(String[] args) {
        SpringApplication.run(MyAppApplication.class, args);
    }

}
```

Você também precisa configurar algumas propriedades do Eureka no seu arquivo application.properties (ou application.yml), como o endereço do servidor Eureka:

```yaml
server:
  port: 8761
spring:
  application:
    name: eureka-server
```

Essas são as configurações básicas para configurar o Eureka no seu projeto Spring Boot como servidor ou cliente. Depois de configurar o Eureka, você pode registrar e descobrir serviços facilmente em sua arquitetura de microserviços e acessar a página do eureka pelo endereço http://localhost:8761/eureka.

