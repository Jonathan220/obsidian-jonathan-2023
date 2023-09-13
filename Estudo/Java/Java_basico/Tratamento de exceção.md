#Java 

Uma exceção é qualquer condição de erro ou comportamento inesperado encontrado por um programa em execução.

Em java, uma exceção é um objeto herdado da classe:
 - java,lang.Exception - o compilador obriga a tratar ou propagar
 - java.lang.RuntimeException - o compilador não obriga a tratar ou propagar

Quando lançada, uma exceção é propagada na pilha de chamadas de métodos em execução, até que seja capturada (tratada) ou o programa seja encerrado.

![[Pasted image 20230612103305.png]]

**Error** - contém objetos cujo erro o programador não precisa tratar no código.

**Exception** - já são as classes de erros que o programador deve tratar.

## Por que exceções?

O modelo de tratamento de exceções permite que erros sejam tratados de forma consistente e flexível, usando boas práticas.

Vantagens:
- Delega a lógica do erro para a classe responsável por conhecer as regras que podem ocasionar o erro.
- Trata de forma organizada (inclusive hierárquica) exceções de tipos diferentes
- A exceção pode carregar dados quaisquer


# Estrutura try-catch

- **Bloco try**
	- Contém o código que representa a execução normal do trecho de código que pode acarretar em uma exceção.
- **Bloco catch
	- Contém o código a ser executado caso uma exceção ocorra
	- Deve ser especificado o tipo da exceção a ser tratada (upcasting é permitido)

```java
try {

}catch (Exception e){

}catch (Exception e){

}catch (Exception e){

}

```


# Bloco finally

É um bloco que contém código a ser executado independente de ter ocorrido ou não uma exceção.

Exemplo clássico: fechar um arquivo, uma conexão de banco de dados, ou outro recurso específico ao final do processamento.

```java
try {

}catch (Exception e){

}finally{

}
```


# Criando Exceção personalizada

Crie uma classe da Exceção desejada.

```java
public class DomainException extends Exception { //
	private static final long serialVersionUID = 1L;

	public DomainException(String msg){
		super(msg);
	}
}
```

No código acima estendemos da classe **Exception**, mas podemos estender da classe **RuntimeException**. Ao estendermos de Exception o compilador obriga o tratamento da exceção, com Runtime o compilador não irá exigir do programado o tratamento da exceção.

No construtor adicionamos um parâmetro e o passamos para a superclasse.

