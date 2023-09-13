#microsserviços 
 
 - O que é
 - O Por quê do seu uso
 - Quem é o responsável
 - Onde é implementado
 - Quando deve ser implementado
 - Como é sua implementação

## O que é microsserviço
Microsserviços são um estilo arquitetural, uma alternativa às aplicações monolíticas tradicionais. As aplicações são implementadas como um conjunto de pequenos serviços, cada um dos quais sendo executado como um processo independente e cada um dos quais se comunicando com os outros por meio de algum tipo de protocolo bem conhecido.

Outras definições:

>"Uma aplicação única, desenvolvida como um conjunto de pequenos serviços, cada um executando o seu próprio processo e comunicando com mecanismos leves, geralmente por [[API | APIs REST]]  através do protocolo HTTP"
>- Martin Fowler

>"Microsserviços são o bom e velho [[SOA]] otimizado"
>- Adrian Cockcroft - Netflix

## O por quê do seu uso
São fáceis de compreender, desenvolver, implantar e refatorar. Não precisam ser feitos em uma punica linguagem ou flamework como toda a aplicação, dando assim oportunidades de fazer uso das tecnologias mais adequadas para a solução.
Também são fáceis testar, gerenciar, versionar e escalar serviços únicos.
Mas a complexidade foi movida para fora da aplicação e foi distribuida entre os microsserviços. OS serviços podem ficar eventualmente indisponíveis e portanto é necessário muito mais monitoramento.

## Quem é o responsável
Os microsserviços podem ser construídos fazendo uso do flameworks como spring boot, que facilitam sua criação. Também existem outras maneiras de contrui microsserviços utilizando ferramentas de cloud ou utilizando a tecnologia disponível na linguagem desejada.
Em javascript se faz uso do [[node.js]] e [[express.js]].

## Onde é implementado
Os microsserviços podem rodar como uma aplicação normal roda. No entanto eles irão se comunicar entre si para distribuirem os seus processamentos. Podem também serem implementados para rodarem dentro de [[containers]] do [[Docker]].

## Quando deve ser implementado
Os microsserviços atendem bem sistemas que precisam ter alta disponiblidade e escalabilidade. A arquitetura facilita o desenvolvimento de multiplos serviços ao mesmo tempo, o que aumenta a produtividade da equipe.
Deve ser implementado:
1. Quando existe a necessidade de escalar um componente individual ou implementar, rapidamente
2. Quando precisa reescrever sistemas legados em linguagens mais recentes
3. Quando a performance do monolito no nível de sistema e de negócio caiu - nessa caso existe três pontos para melhorar a performance:
	- Horizontal Duplication (escala horizontal): colocar mais máquinas para processar)
	- Slit by Function or Service ([[Decomposição funcional]]): aqui sim entram microsserviços.
	- Lookup Oriented Splits (ex: Customer Database) roda parcela dos dados: aqui temos, por exemplo, a instalação de um  um ERP em vários locais que têm sua própria infraestrutura, o que também é uma maneira de escalar.

## Como é sua implementação
Utilizando o ecossistema do Spring, um microsserviço é facilmente implementado. É possível iniciar sua construção no flamework spring boot criando um projeto spring start ou então no site https://start.spring.io/, selecionando as dependencias necessárias, a versão do java e preenchendo as informações pertinentes ao projeto.
Se o projeto for criado a partir do site https://start.spring.io/, será necessário importar o zip para o seu editor de código.
Com isso o projeto será criado somente com a classe main que já o permitirá ser rodado, além das dependencias básicas que o próprio spring irá já criar no pom.
Para que o microsserviço possa fazer uso de API REST para se comunicar, será necessário adicionar a dependencia abaixo:

```java
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

Caso utilize outro meio de comunicação será necessário adicionar a dependencia correspondente ao meio.