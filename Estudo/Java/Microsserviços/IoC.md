#microsserviços #springboot 

## IoC - Inversion of Control

### O que é

IOC (Inversion of Control) é um padrão de projeto que define uma inversão no fluxo de controle em uma aplicação. Em vez de o código da aplicação controlar a criação e gerenciamento de objetos, esse controle é passado para um contêiner de IoC (Inversão de Controle) que gerencia os objetos e suas dependências.

Em outras palavras, o IoC é responsável por criar e gerenciar os objetos da aplicação, incluindo a resolução de suas dependências. O IoC permite que a aplicação seja mais flexível, modular e fácil de manter, pois separa as responsabilidades de criação e gerenciamento de objetos do restante da aplicação.

Em Spring Boot, o contêiner de IoC é implementado pelo Spring Framework e é chamado de contêiner Spring. O contêiner Spring usa a anotação `@Autowired` para injetar as dependências dos objetos gerenciados pelo contêiner. Isso permite que os objetos dependam de outros objetos sem saberem como eles são criados ou gerenciados.

Por exemplo, imagine que temos duas classes, `MyService` e `MyDao`, em que `MyService` depende de `MyDao`. Usando o IoC do Spring Boot, podemos injetar a dependência do `MyDao` no `MyService` usando a anotação `@Autowired`:

```java
@Service
public class MyService {
    
    @Autowired
    private MyDao myDao;
    
    // ...
}
```

Com isso, o contêiner Spring é responsável por criar e gerenciar as instâncias dos objetos `MyService` e `MyDao`, e o `MyService` não precisa saber como o `MyDao` é criado ou gerenciado. Isso torna a aplicação mais modular e fácil de manter.

Em resumo, o IoC é um padrão de projeto que define uma inversão no fluxo de controle em uma aplicação, passando o controle da criação e gerenciamento de objetos para um contêiner de IoC. Em Spring Boot, o contêiner Spring implementa o IoC, permitindo que as dependências dos objetos sejam injetadas usando a anotação `@Autowired`.

