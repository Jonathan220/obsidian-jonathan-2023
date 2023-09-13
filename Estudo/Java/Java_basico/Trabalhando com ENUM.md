#Java 

Crie uma classe Enum.

```java
public enum OrderStatus{
	PENDING_PAYMENT,
	PROCESSING,
	SHIPPED,
	DELIVERED;
}
```

Depois crie uma classe que faça uso da Enumeração criada.

```java
public class Order{
	private Integer id;
	private Date moment;
	private OrderStatus status;
}
```

Para chamar uma constante específica da classe, chame a classe enumerada como uma classe estática.

```java
public static void main(String args[]){
	Order order = new Order(1080, new Date(), OrderStatus.PENDING_PATMENT);
}
```

# Conversão de String para Enum

```java
OrderStatus os1 = OrderStatus.valueOf("Delivered");
```

