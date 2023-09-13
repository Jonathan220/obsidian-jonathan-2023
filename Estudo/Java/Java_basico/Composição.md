#Java 

É um tipo de associação que permite que um objeto contenha outro.

 - Relação `tem-um` e `tem-vários`
 - Vantagens:
	 - Organização: divisão de responsabilidades.
	 - Coesão. Cada objeto é responsável por uma única coisa.
	 - Flexibilidade
	 - Reuso


![[Pasted image 20230608150441.png]]

O objeto Post é composto de um ou mais objetos Comment.

```java
public class Post {
    private Date moment;
    private String title;
    private String content;
    private Integer likes;

    private List<Comment> comments = new ArrayList<>();

    private static SimpleDateFormat sdf = new SimpleDateFormat("dd/MM/yyyy HH:mm:ss");
}
```

