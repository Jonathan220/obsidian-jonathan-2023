#springboot #Monitoramento #microsserviços 

# O que é

O Spring Boot Admin é uma aplicação web que fornece uma interface de usuário para gerenciar e monitorar aplicações baseadas no Spring Boot. Ele fornece informações úteis sobre o estado da aplicação, incluindo métricas, informações sobre threads, logs e muito mais. Além disso, ele permite gerenciar e monitorar várias instâncias de uma aplicação em execução, o que é útil em ambientes de produção.

>[!info]+
>Spring Boot Admin is a monitoring tool that aims to visualize information provided by Spring Boot Actuators in a nice and accessible way. It consists of two major parts:
>
> -   A server that provides a user interface to display and interact with Spring Boot Actuators.
> 
> -   A client that is used to register at the server and allow to access actuator endpoints.

O Spring Boot Admin é construído em cima do Spring Boot e usa as bibliotecas [[Spring Actuator|Spring Boot Actuator]] e Spring Boot DevTools para obter informações sobre o estado da aplicação. Ele também fornece recursos adicionais, como a possibilidade de fazer alterações nas configurações da aplicação sem precisar reiniciá-la.

Em resumo, o Spring Boot Admin é uma ferramenta valiosa para monitorar e gerenciar aplicações baseadas no Spring Boot em tempo real.


# Como implementar

## Configurando o server

Spring Boot Admin Server é capaz de executar como uma aplicação [[servlet]] ou [[webflux]], então você precisa decidir como pretende executar o server e adicionar a dependencia necessária. Neste exempl nós fazemos uso de um servlet web starter. 

Para implementar o Spring Boot Admin, siga estes passos:

1.  Adicione a dependência do Spring Boot Admin e spring boot start web ao seu projeto Spring Boot. Isso pode ser feito adicionando o seguinte trecho de código no seu arquivo pom.xml (para projetos Maven) ou build.gradle (para projetos Gradle):

Para Maven:

```xml
<dependency>
	<groupId>de.codecentric</groupId>
	<artifactId>spring-boot-admin-starter-server</artifactId>
	<version>2.7.10</version>
</dependency>
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

2.  Adicione a anotação `@EnableAdminServer` na classe de configuração do Spring Boot:

```java
package br.com.jonathan;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Configuration;

import de.codecentric.boot.admin.server.config.EnableAdminServer;

@Configuration
@EnableAutoConfiguration
@EnableAdminServer
@SpringBootApplication
public class SpringAdminApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringAdminApplication.class, args);
	}

}
```

3.  Inicie sua aplicação Spring Boot.

Após esses passos, você pode acessar o painel do Spring Boot Admin no seu navegador usando a URL `http://localhost:8080/`. A partir daí, você poderá monitorar e gerenciar suas aplicações Spring Boot.

Note que o Spring Boot Admin oferece muitas configurações personalizáveis. Você pode ajustar a aparência, as configurações de segurança e muito mais. Consulte a documentação oficial para obter mais informações sobre as configurações disponíveis.

https://codecentric.github.io/spring-boot-admin/current/getting-started.html

## Registrando uma aplicação cliente

To register your application at the SBA Server, you can either include the SBA Client or use [Spring Cloud Discovery](http://projects.spring.io/spring-cloud/spring-cloud.html) (e.g. [[Eureka]], Consul, …​).

### Configurando com SBA Client

Cada aplicação que precisar ser registrada no Spring Boot Admin precisará incluir o Spring Boot Admin Client.

Adicione as seguintes dependecias:

```xml
<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-actuator -->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
<!-- https://mvnrepository.com/artifact/de.codecentric/spring-boot-admin-starter-client -->
<dependency>
	<groupId>de.codecentric</groupId>
	<artifactId>spring-boot-admin-starter-client</artifactId>
	<version>2.7.10</version>
</dependency>
```

Habilite o SBA Client configurando a URL do Spring Boot Admin Server, libere os endpoints do actuator para que o server possa coletar dados:

```properties
server.port=8099
management.endpoints.web.exposure.include=*
management.endpoint.health.show-details=always
spring.boot.admin.client.url=http://localhost:8080
```

>[!attention]+ Atenção
>Para as configurações acima foi feito uso da versão 2.x do Spring Boot: 2.7.10-SNAPSHOT

Após a configuração acima a aplicação cliente deve aparecer registrada no spring server.

![[Pasted image 20230320214458.png]]
Caso ocorra erro com a resolução de nome da aplicação, será necessário seguir os passos abaixo para resolver:

Se você estiver executando a aplicação cliente e o servidor na mesma máquina, é possível que o problema esteja relacionado a como o Windows trata o nome de domínio "PrillNotebook18.mshome.net".

Uma opção para tentar resolver o problema é adicionar a entrada "PrillNotebook18.mshome.net" ao arquivo "hosts" do seu sistema Windows. O arquivo "hosts" é um arquivo de texto simples que mapeia nomes de domínio para endereços IP.

Para editar o arquivo "hosts" no Windows, siga os seguintes passos:

1.  Abra o Bloco de Notas como administrador (clique com o botão direito do mouse no ícone do Bloco de Notas e selecione "Executar como administrador").
2.  No Bloco de Notas, clique em "Arquivo" e depois em "Abrir".
3.  Navegue até a pasta "C:\Windows\System32\drivers\etc".
4.  Selecione o arquivo "hosts" e clique em "Abrir".
5.  Adicione uma nova linha no arquivo com o seguinte formato: "<endereço IP do servidor> PrillNotebook18.mshome.net"
6.  Salve o arquivo "hosts" e feche o Bloco de Notas.

Depois de adicionar a entrada ao arquivo "hosts", tente executar novamente a sua aplicação cliente e verifique se o erro persiste.

### Configurando com Spring Cloud Discovery

Se você jpa usa Spring Cloud Discovery para suas aplicações não há necessidade de fazer uso do SBA Client. Apanas adicione um DiscoveryClient para o Spring boot Admin Server, o resto é feito pelo nosso AutoConfiguration.

Aqui fazemos uso do Eureka, mas outras implementações de Spring Cloud Discovery também são suportadas.

1. Adicione spring-cloud-starter-eureka às dependencias do Spring Boot Admin:

```xml
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

2. Habilite o Discovery adicionando a anotação @EnableDiscoveryClient:

```java
package br.com.jonathan;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.context.annotation.Configuration;

import de.codecentric.boot.admin.server.config.EnableAdminServer;

@Configuration
@EnableAutoConfiguration
@EnableAdminServer
@SpringBootApplication
@EnableDiscoveryClient
public class SpringAdminApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringAdminApplication.class, args);
	}

}
```

3. Configure o arquivo de propriedades da aplicação:

```yaml
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
spring:
  application:
    name: springAdmin
management:
  endpoint:
    health:
      show-details: always
  endpoints:
    web:
      exposure:
        include: '*'
```

Com as configurações acima será possível visualizar na interface web do Spring Boot Admin informações de todos os microsserviços registrados no eureka. Basicamente o que fizemos aqui foi adicionar o admin server ao Eureka e caso precise visualizar informações de uma aplicação no Spring Boot Admin, basta adicionar a mesma ao [[Eureka]].

A adição do Spring [[Spring Actuator|Actuator]] e a configuração de seus endpoints é necessária para que o Spring Boot Admin passe e exiba informações detalhadas de si mesmo e que o seu nome e de outras aplicações apareçam corretos nos seus dashboards e no [[Eureka]].

>[!Attention]+ Atenção
>Para o bom funcionamento do Spring boot Admin, sempre adicione nas aplicações clientes a dependencia do actuator e libere os seus endpoints.

