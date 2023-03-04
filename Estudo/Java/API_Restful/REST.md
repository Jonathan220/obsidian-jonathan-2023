 - O que é
 - Por que do seu uso
 - Quem é o responsável
 - Onde é implementado
 - Quando deve ser implementado
 - Como é sua implementação
	 - Parâmetros
	 - Http status
	 - Verbos http
 - Níveis de maturidade de richardson
 - [[REST#Tratamento de Exceção | Tratamento de Exceção]]

## Tratamento de Exceção

1 - Crie a classe a ser usada na resposta do body da requisição

```java
	
	public class ExceptionResponse implements Serializable{

	private static final long serialVersionUID = 1L;
	
	private Date timestamp;
	private String message;
	private String details;
	
	public ExceptionResponse(Date timestamp, String message, String details) {
		this.timestamp = timestamp;
		this.message = message;
		this.details = details;
	}

	public Date getTimestamp() {
		return timestamp;
	}

	public String getMessage() {
		return message;
	}

	public String getDetails() {
		return details;
	}
	
	
}

```

2 - Criar o erro customizado que irá acionar a resposta. Para acionar a resposta basta lançar este erro.

```java
@ResponseStatus(HttpStatus.NOT_FOUND)
public class ResourceNotFoundException extends RuntimeException{
	private static final long serialVersionUID = 1L;

	public ResourceNotFoundException(String ex) {
		super(ex);
	}

	
}
```

3 - Criar a classe que irá lidar com os erros e enviar uma resposta de acordo com o erro lançado.

```java

@ControllerAdvice
@RestController
public class CustomizedResponseEntityExceptionHandler extends ResponseEntityExceptionHandler{
	
	@ExceptionHandler(Exception.class)
	public final ResponseEntity<ExceptionResponse> handleAllExceptions(Exception ex, WebRequest request){
		ExceptionResponse exceptionResponse = new ExceptionResponse(new Date(), ex.getMessage(), request.getDescription(false));
		return new ResponseEntity<>(exceptionResponse, HttpStatus.INTERNAL_SERVER_ERROR);
	}
	
	@ExceptionHandler(ResourceNotFoundException.class)
	public final ResponseEntity<ExceptionResponse> handleNotFoundExceptions(Exception ex, WebRequest request){
		ExceptionResponse exceptionResponse = new ExceptionResponse(new Date(), ex.getMessage(), request.getDescription(false));
		return new ResponseEntity<>(exceptionResponse, HttpStatus.NOT_FOUND);
	}
}
```

No exemplo de código acima a primeira função é acionada quando uma exceção generica é lançada. A segunda função é acionada quando o erro customizado que criamos é lançado em alguma parte da aplicação. Aqui criamos o objeto com a resposta e retornamos para o cliente.
O ExceptionHandler deve ter a classe de erro customizada para acionar a funação. O status deve ser o mesmo criado na classe customizada.