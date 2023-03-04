#### @PathParam
Obrigatório. Colocado depois da barra. Se não colocar dar erro na aplicação.

### @RequestParam
Opcional. Colocado depois do ponto de interrogação em pares de campo e valor. Para colocar mais de um utilize o &. Não precisa colocar a variável no request mapping.

### Alterando o json apresentado ao cliente

#### @JsonProperty("")
Permite alterar o nome dos campos do json apresentado ao cliente. Deve ser adicionado na classe retornada para o cliente.

#### @JsonIgnore
O campo não aparece para o cliente

#### @JsonPropertyOrder({})
Ordem em que as propriedades são apresentadas para o cliente

#### Content negotiation
Prover formatos diferentes de respostas para cada necessidade do cliente.
 - Adicionar dependencia no pom
 - Criar classe de configuração
 - Caso necessário alterar os formatos consumidos no controller

### HATEOAS
Permite adicionar links ao body de retorno das requisições.
- Adicionar dependencia no pom
- Adicionar import static
```java
import static org.springframework.hateoas.server.mvc.WebMvcLinkBuilder.linkTo;
import static org.springframework.hateoas.server.mvc.WebMvcLinkBuilder.methodOn;
```
- Adicionar link na resposta
```java
persons.stream().forEach(p -> {
	try {
	p.add(linkTo(methodOn(PersonController.class).findById(p.getId())).withSelfRel());
			} catch (Exception e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		});
```

```java
try {
	vo.add(linkTo(methodOn(PersonController.class).findById(vo.getId())).withSelfRel());
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
```