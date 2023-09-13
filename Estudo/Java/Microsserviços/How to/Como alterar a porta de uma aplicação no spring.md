#springboot #microsserviços 

Para mudar a porta em que uma aplicação Spring Boot é executada, você pode especificar uma propriedade `server.port` no arquivo `application.properties` ou `application.yml`. Aqui estão os exemplos de como fazer isso:

Para `application.properties`:

```properties
server.port=8081
```

Para `application.yml`:

```yaml
server:
  port: 8081
```

Nesses exemplos, a porta da aplicação Spring Boot foi alterada para `8081`. Basta substituir esse valor pela porta que você deseja usar.

Se você deseja especificar uma porta diferente para diferentes perfis de execução, pode usar a notação `${}` para especificar o valor da propriedade com base no perfil ativo. Por exemplo:

Para `application.properties`:

```properties
server.port=${myapp.port:8080}
```

Nesse exemplo, a porta da aplicação Spring Boot será definida como o valor da propriedade `myapp.port` se estiver definida, caso contrário, será usada a porta padrão `8080`.

Para `application.yml`:

```yaml
server:
  port: ${myapp.port:8080}
```

Nesse exemplo, a porta da aplicação Spring Boot será definida como o valor da propriedade `myapp.port` se estiver definida, caso contrário, será usada a porta padrão `8080`.