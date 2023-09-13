#Java 

# Principais funcionalidades

## Data-hora-local
LocalDate
LocalDataTime

## Data-hora global
Instant

## Duração
Duration

## Outros
ZoneId
ChronoUnit

# Instanciar a hora atual

```java
LocalDate d01 = LocalDate.now();
LocalDateTime d02 = LocalDateTime.now();
Instant d03 = Instant.now();
```

As hora são impressas no formato ISO 8601: **2023-10-29T16:30:52.45343**.
O Instant retorna a hora com fuso horários de Londres (GMT).

# Converter Texto ISO 8601 para objeto data

```java
LocalDate d04 = LocalDate.parse("2022-04-15");
LocalDateTime d05 = LocalDateTime.parse("2022-07-15T14:22:25");
Instant d06 = Instant.parse("2022-07-15T14:22:25Z");
```

É possível informar uma hora com fuso horário diferente do de Londres para se convertida em Instant. Ao fazer isso a hora é salva no horário de Londres.

```java
Instant d07 = Instant.parse("2022-07-15T14:22:25-03:00");
```

# Converter texto em formato customizado para objeto data

```java
DateTimeFormatter fmt = DateTimeFormatter.ofPattern("dd/MM/yyyy");

DateTimeFormatter fmt1 = DateTimeFormatter.ofPattern("dd/MM/yyyy HH:mm:ss");

LocalDate d08 = LocalDate.parse("22/10/1990", fmt);

LocalDateTime d09 = LocalDateTime.parse("22/10/1990 22:43:55", fmt1);
```

Necessário o uso do DateTimeFormatter para informar o formato à função **parse**.

https://docs.oracle.com/javase/8/docs/api/java/time/format/DateTimeFormatter.html

# Instanciar objeto data a partir de dados isolados

```java
LocalDate d10 = LocalDate.of(1990, 10, 9);
LocalDateTime d11 = LocalDateTime.of(1990, 10, 29, 10, 11, 22);
```

# Converter objeto data em texto customizado

```java
LocalDate d01 = LocalDate.parse("2022-07-20");
LocalDateTime d02 = LocalDateTime.parse("2022-07-20T01:30:26");

DateTimeFormatter fmt1 = DateTimeFormatter.ofPattern("dd/MM/yyyy");
DateTimeFormatter fmt2 = DateTimeFormatter.ofPattern("dd/MM/yyyy HH:mm");

System.out.println("d01 = " + d01.format(fmt1));
System.out.println("d01 = " + fmt1.format(d01));
System.out.println("d01 = " + d01.format(DateTimeFormatter.ofPattern("dd/MM/yyyy")));

System.out.println("d02 = " + d02.format(fmt1)); // Somente imprime a data
System.out.println("d02 = " + d02.format(fmt2));
```

O Instant não possui método **format**.
Para converter o Instant em um formato específico é necessário informar no DateTimeFormatter o fuso horários:

```java
Instant d03 = Instant.parse("2022-07-20T01:30:26Z");

DateTimeFormatter fmt3 = DateTimeFormatter.
ofPattern("dd/MM/yyyy HH:mm").withZone(ZoneId.systemDefault());

System.out.println("d03 = " + fmt3.format(d03)); // a data será impressa no fuso horário informado
```


Também é possível o uso de constantes com formatos já pre-definidos no **DateTimeFormatter**.

```java
LocalDateTime d02 = LocalDateTime.parse("2022-07-20T01:30:26");
Instant d03 = Instant.parse("2022-07-20T01:30:26Z");

DateTimeFormatter fmt4 = DateTimeFormatter.ISO_DATE_TIME;
DateTimeFormatter fmt5 = DateTimeFormatter.ISO_INSTANT;

System.out.println("d02 = " + d02.format(fmt4));
System.out.println("d03 = " + fmt5.format(d03));
```

# Convertendo uma data-hora global em local

É necessário informar o fuso horário

O comando abaixo retorna uma lista dos nomes dos fuso horários customizados:

```java
for (String s : ZoneId.getAvailableZoneIds()){
	System.out.println(s);
}
```

Para converter para o horário da máquina local:

```java
Instant d03 = Instant.parse("2022-07-20T01:30:26Z");

LocalDate r1 = LocalDate.ofInstant(d03, ZoneId.systemDefault());
```

Para converter para um fuso horários diferente:

```java
Instant d03 = Instant.parse("2022-07-20T01:30:26Z");

LocalDate r2 = LocalDate.ofInstant(d03, ZoneId.of("Portugal"));
```

També podesmo usar o LocalDateTime:

```java
LocalDateTime r3 = LocalDateTime.ofInstant(d03, ZoneId.systemDefault());
LocalDateTime r4 = LocalDateTime.ofInstant(d03, ZoneId.of("Portugal"));
```

# Recuperar os dados de uma data-hora local

```java
LocalDate d01 = LocalDate.parse("2022-07-20");
LocalDateTime d02 = LocalDateTime.parse("2022-07-20T01:30:26");

System.out.println("Dia = " + d01.getDayOfMonth());
System.out.println("Mês = " + d01.getMonth());
System.out.println("Ano = " + d01.getYear());
System.out.println("Hora = " + d02.getHour());
System.out.println("Minutos = " + d02.getMinute());
```


# Calculos com data-hora

## Adicionando ou removendo tempo a uma data

```java
LocalDate d01 = LocalDate.parse("2022-07-20");
Instant d03 = Instant.parse("2022-07-20T01:30:26Z");

LocalDate pastWeekLocalDate = d01.minus(7, ChronoUnit.DAYS);
LocalDate nextWeekLocalDate = d01.plus(7, ChronoUnit.DAYS);

Instant pastWeekInstant = d03.minus(7, ChronoUnit.DAYS);
Instant nextWeekInstant = d03.plus(7, ChronoUnit.DAYS);

```

Objetos data-hora são imutáveis.

# Obtendo a diferença de tempo entre duas datas

```java
 Duration t1 = Duration.between(pastWeekInstant, d03);
```

No caso de LocalDate é necessário convertê-lo para LocalDateTime, e para isso podemos fazer uso dos métodos abaixo:

```java
Duration t2 = Duration.between(pastWeekLocalDate.atTime(0, 0), d01.atTime(0, 0));
Duration t2 = Duration.between(pastWeekLocalDate.atStartOfDay(), d01.atStartOfDay());
```

Para imprimir a duração dispomos de métodos do Duration com unidades de tempo:

```java
System.out.println("duração Instant = " + t1.toDays() + " dias");
```


# Trabalhando com datas antes do java 8

Representa um Instant

Um objeto Date internamente armazena o número de milissegundos desde a meia noite do dia 1 de janeiro de 1970 GMT.

O Date imprime a data no horário local da máquina e no formato padrão do java.

## Convertendo uma data no formato ISO 8601 para Date

```java
Date data = Date.from(Instant.parse("2018=05-04T15:42:07Z"))
```

## Convertendo uma data customizada em Date

Para isso é necessário informar o formato da data através do uso do **SimpleDateFormat**.

```java
SimpleDateFormat sdf1 = new SimpleDateFormat("dd/MM/yyyy");

Date data = sdf1.parse("25/10/1990");
```

## Convertendo data em texto customizado

```java
SimpleDateFormat sdf1 = new SimpleDateFormat("dd/MM/yyyy");

Date data = sdf1.parse("25/10/1990");

System.out.println(sdf1.format(data));
```

## Instanciando um objeto Date com a data exata

```java
Date data = new Date();
Date date = new Date(System.currentTimeMillis());
```

## Somando uma unidade de tempo

Para somar ou subtrair unidades de  tempo de um objeto Date é necessário fazer uso da classe **Calendar**.

```java
SimpleDateFormat sdf1 = new SimpleDateDormat("dd/MM/yyyy HH:mm:ss");
Date data = Date.from(Instant.parse("28/05/2022T18:30:25"));

Calendar cal = Calendar.getInstance(); //cria uma instancia do calendar
cal.setTime(data); //Adiciona o valor da data ao Calendar cal
cal.add(Calendar.HOUR_OF_DAY, 4); //Permite adicionar a quantidade de tempo com qualquer unidade
data = cal.getTime(); //Recuperar a data alterada
```

## Obtendo uma unidade de tempo de um objeto Date

```java
SimpleDateFormat sdf1 = new SimpleDateDormat("dd/MM/yyyy HH:mm:ss");
Date data = Date.from(Instant.parse("28/05/2022T18:30:25"));

Calendar cal = Calendar.getInstance(); //cria uma instancia do calendar
cal.setTime(data); //Adiciona o valor da data ao Calendar cal

int minutes = cal.get(Calendar.MINUTE);
int month = 1 + cal.get(Calendar.MONTH); // Retorna o mês contando a partir de zero
```



