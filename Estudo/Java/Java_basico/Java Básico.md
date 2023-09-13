#Java 


# Configurando a localização da aplicação

O java interpreta valores decimais com o uso da virgula, caso precise fazer uso do ponto final, faça uso da configuração abaixo.

```java
Locale.setDefault(Locale.US);
```

# Utilizando debug no java

![[Pasted image 20230409153114.png]]

# Lendo dados da tela

```java
Scanner sc = new Scanner(System.in);

sc.close();
```

- `sc.next()` - Para ler uma palavra em string.
- `sc.nextInt()` - Para ler inteiro
- `sc.nextDouble` - Para ler valores em ponto flutuante. Por padrão aceita somente como entrada a virgula. Para aceitar o ponto é necessário alterar a localização, caso contrário haverá erro.
- `sc.next.charAt(0)` - Para ler um caractere apenas
- `sc.nextLine()` - Para ler texto até a quebra de linha.

**Quebra de linha pendente** - Funções como nextInt(), next() e nextDouble(), consomem somente os valores digitados , mas não a quebra de linha gerada pelo usuário. Essa quebra de linha fica pendente e pode ser consumida pela próxima função de leitura de linha de texto.

Faça um nextLine() extra antes de fazer o nextLine() do seu interesse.

# Formatar impressão de texto em tela

```java
double valor = 2.00;

System.out.printf("%.2f%n", valor)
```

%f = ponto flutuante
%n = quebra de linha
%d = inteiro
%s = texto

Colocar valor em double com numeros decimais: 6.0
Colocar valor em float com sufixo F no final do valor: 6F

# Estruturas de repetição

## Enquanto (while)

É uma estrutura de controle que repete um bloco de comandos enquanto uma condição for verdadeira.
**Quando usar**: quando não se sabe previamente a quantidade de repetições que será realizada.

```java
while(true){
	comando1;
	comando2;
}
```

## Para (for)

É uma estrutura de controle que repete um bloco de comandos para um certo intervalo de valores.

**Quando usar**: quando se sabe previamente a quantidade de repetições, ou o intervalo de valores.

```java
for(int i = 0; i < n; i++){
	comando1;
	comando2;
}
```

## Faça enquanto

Menos utilizada, mas em alguns casos se encaixa melhor ao problema.

O bloco de comandos executa pelo menos uma vez, pois a condição é verificada no final.

```java
do{
	comando1;
	comando2;
}while(true)
```


# StringBuilder

Uma melhor forma de imprimir informações de um objeto e com melhor performance.

```java
public String toString(){

        StringBuilder sb = new StringBuilder();
        sb.append(title + "\n");
        sb.append(likes);
        sb.append(" Likes - ");
        sb.append(sdf.format(moment) + "\n");
        sb.append(content + "\n");
        sb.append("Comments: \n");

        for(Comment c: comments){
            sb.append(c.getText() + "\n");
        }

        return sb.toString();
    }
```