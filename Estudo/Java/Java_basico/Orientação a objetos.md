#Java 

# Herança

É um tipo de associação que permite que uma classe herde todos dados e comportamentos de outra.

- vantagens: reuso e polimorfismo
- Herança é um associação entre classes (e não entre objetos)
- Alguns termos utilizados são: Generalização/especialização, superclasse (classe base)/ subclasse (classe derivada).

# Upcasting

 - Casting da subclasse para superclasse
 - Uso comum: polimorfismo

Basicamente seria fazer uma variável do tipo superclasse receber um tipo da subclasse.

```java
Veiculo v1 = new Veiculo();
Carro carro = new Carro();

v1 = carro; // não dá erro
```

# Downcasting

- Casting da superclasse para subclasse
- Palavra instanceof
- Uso comum: métodos que recebem parâmetros genéricos (ex: Equals).

Nesse caso é necessário forçar a conversão da superclasse para a subclasse com casting exlicito.

```java
Veiculo v1 = new Veiculo();
Carro carro = new Carro();

carro = v1 // Ocorre erro.
carro = (Carro) v1 // Necessário casting explicito
```


# Sobreposição e Sobrescrita

É a implementação de um método de uma superclasse na subclasse.

É fortemente recomendável usar a anotação @Override em um método sobrescrito.
- Facilita a leitura e compreensão do código 
- Avisamos ao compilador (boa prática)

# Super

A palavra super é utilizada para fazer uso do construtor da superclasse no construtor da subclasse.

```java
public class Carro(){

	private String modelo();
	private String marca();
	
	public Carro(String modelo, String marca, String montor){
		super(motor); // Chama o construtor da superclasse
		this.modelo = modelo;
		this.marca = marca;
	}
}
```

Também é possível fazer uso de métodos da superclasse utilizando a palavra **super**.

```java
@override
public void withdraw(double amount){
	super.withdraw(amount);
}
```


# Classes e métodos final

 - **Classe**: evita que a classe seja herdada

```java
public final class SavingsAccount {
	...
}
```
 
 - **Método**: evita que o método sob seja sobreposto

```java
public final void withdraw(double amount){ // o método não poderá ser sobrescrito nas classes filhas
	balance -= amount;
}
```

## Pra que classes e métodos final?

- **Segurança**: dependendo das regras do negócio, às vezes é desejável garantir que uma classe não seja herdada, ou que um método não seja sobreposto. Geralmente convém acrescentar **final** em métodos sobrepostos, pois sobreposições múltiplas podem ser uma porta de entrada para inconsistências.
- **Performance**: atributos de tipo de uma classe final são analisados de forma mais rápida em tempo de execução.
 Exemplo clássico: String


# Polimorfismo

Em programação orientada a objetos, polimorfismo é recurso que permite que variáveis de um mesmo tipo mais genérico possam apontar para objetos de tipos específicos diferentes, tendo assim comportamentos diferentes conforme cada tipo específico.

![[Pasted image 20230609144219.png]]
Na imagem acima, as variáveis x e y são do mesmo tipo genérico, mas apontam para tipos diferentes na memória, que são tipos mais específicos.

- A associação do tipo específico com o tipo genérico é feita em tempo de execução (upcasting).
- O compilador não sabe para qual tipo específico a chamada do método Withdraw está sendo feita (ele só sabe que são duas variáveis tipo Account).


# Classes abstratas

São classes que não podem ser instanciadas.

É uma forma de garantir herança total: somente subclasses não abstratas podem ser instanciadas, mas nunca a superclasse abstrata.

![[Pasted image 20230609184620.png]]


![[Pasted image 20230609184920.png]]

No exemplo acima a classe account foi colocada como abstrata, portanto instancia-la não é mais possível, mas é possível criar variáveis da classe que recebam instancias de suas subclasses. 

![[Pasted image 20230609185921.png]]

# Métodos abstratos

São métodos que não possuem implementação.

Métodos precisam ser abstratos quando a classe é genérica demais para conter sua implementação.

Se uma classe possuir pelo menos um método abstrato, então esta classe também é abstrata.

