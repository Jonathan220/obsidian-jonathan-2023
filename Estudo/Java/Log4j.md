#Java #microsserviços #springboot #Monitoramento 

# O que é

O Log4j é uma biblioteca de registro de eventos (logging) para aplicativos Java. Ele fornece uma estrutura de registro flexível e configurável que permite aos desenvolvedores personalizar a maneira como as mensagens de log são armazenadas, formatadas e filtradas.

O Log4j é amplamente utilizado em aplicativos Java devido à sua facilidade de uso e flexibilidade. Ele permite que os desenvolvedores controlem a saída de registro de seus aplicativos por meio de diferentes níveis de gravidade, como DEBUG, INFO, WARN e ERROR, permitindo que os usuários configurem o nível de detalhes que desejam ver no registro. Além disso, o Log4j permite que as mensagens de log sejam filtradas com base em critérios específicos, permitindo que os desenvolvedores encontrem facilmente informações importantes em grandes volumes de registros.

O Log4j também fornece várias opções de destino de log (log appenders), que permitem que os desenvolvedores escolham onde e como as mensagens de log devem ser armazenadas. Alguns dos destinos mais comuns incluem arquivos de log, bancos de dados e consoles.

Em resumo, o Log4j é uma biblioteca de registro de eventos que permite que os desenvolvedores controlem a saída de registro de seus aplicativos Java de maneira flexível e configurável. Ele é amplamente utilizado em aplicativos Java devido à sua facilidade de uso e flexibilidade.

# Como implementá-lo

Para implementar o log4j em um projeto spring boot siga os passos abaixo:

1. Adicione as dependencias do log4j ao projeto. Nós iremos precisar das dependencias do Apache log4j Core e Apache log4j API.

```xml
<dependency>
	<groupId>org.apache.logging.log4j</groupId>
	<artifactId>log4j-core</artifactId>
</dependency>
<dependency>
	<groupId>org.apache.logging.log4j</groupId>
	<artifactId>log4j-api</artifactId>
</dependency>
```

2. Todo projeto spring boot faz uso do Spring Boot Starter, que depende do spring-boot-starter-logging, o qual faz uso do [[logback]] por padrão como ferramenta de logging. Para fazer uso do log4j vai ser necessário excluir o spring-boot-starter-logging.

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter</artifactId>
	<exclusions>
		<exclusion>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-logging</artifactId>
		</exclusion>
	</exclusions>
</dependency>
```

3. Spring boot automaticamente configura o log4j se encontra um arquivo nomeado como log4j2.xml, log4j2.json ou log4j2.yml no classpath.
	Aqui fazemos uso do log4j.xml. Crie um novo arquivo dentro da pasta src/main/resources e adicione a seguinte configuração abaixo:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
  <Appenders>
    <Console name="Console" target="SYSTEM_OUT">
      <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n" />
    </Console>
    <File name="File" fileName="logs/myapp.log">
      <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n" />
    </File>
  </Appenders>
  <Loggers>
    <Root level="info">
      <AppenderRef ref="Console" />
      <AppenderRef ref="File" />
    </Root>
  </Loggers>
</Configuration>
```

Este arquivo de configuração define um appender de console que registra mensagens de log no console padrão do sistema. O layout `PatternLayout` define o formato das mensagens de log. A appender foi nomeado como `Console` para futuras referencias. 

Também adicionamos um appender de arquivo nomeado como `File`, que registra mensagens de log em um arquivo chamado `myapp.log` no diretório `logs`. O layout `PatternLayout` define o formato das mensagens de log.

Na parte `Loggers` definimos o log raiz como nível info e fazemos referencia aos dois appenders criados anteriormente, ou seja, os configuramos como nível info.

>[!Attention]+ Atenção
>Pode ocorrer de o Spring boot não conseguir encontrar o arquivo de configuração do log. Nesse caso você precisará garantir que ele o encontre.
>Uma maneira de garantir que o Log4j seja inicializado corretamente é adicionar a seguinte propriedade do sistema na sua aplicação Spring Boot:
>
>`-Dlog4j.configurationFile=/path/to/log4j2.xml`
>
>Essa propriedade diz ao Log4j para usar o arquivo de configuração especificado em vez de procurar automaticamente um arquivo de configuração no diretório padrão.
>Substitua `/path/to/log4j2.xml` pelo caminho completo do arquivo de configuração `log4j2.xml` em sua aplicação. Certifique-se de que o caminho esteja correto e que o arquivo de configuração esteja presente no local especificado.
>
>Você pode adicionar essa propriedade do sistema à sua aplicação Spring Boot de várias maneiras, dependendo de como você a executa. Por exemplo, se você estiver executando sua aplicação a partir da linha de comando, poderá adicionar a propriedade usando o seguinte comando:
>
>`java -Dlog4j.configurationFile=/path/to/log4j2.xml -jar your-application.jar`
>
>Se você estiver usando um servidor de aplicativos, como o Tomcat, poderá adicionar a propriedade do sistema ao arquivo de configuração do servidor, como `setenv.sh` ou `setenv.bat`, dependendo do seu sistema operacional.
>
>Para adicionar a propriedade do sistema `-Dlog4j.configurationFile` em sua aplicação Spring Boot no Spring Tool Suite, siga estes passos:
>1.  Clique com o botão direito do mouse no projeto no painel de navegação e selecione `Run As` -> `Run Configurations`.
> 
>2.  Na janela `Run Configurations`, selecione a configuração que você está usando para executar sua aplicação Spring Boot.
>  
>3.  Na guia `Arguments`, role para baixo até a seção `VM arguments`.
>    
>4.  Na seção `VM arguments`, adicione a seguinte propriedade do sistema:
>
>![[Pasted image 20230330085652.png]]
>
>`-Dlog4j.configurationFile=/path/to/log4j2.xml`
>
>Substitua `/path/to/log4j2.xml` pelo caminho completo do arquivo de configuração `log4j2.xml` em sua aplicação. Certifique-se de que o caminho esteja correto e que o arquivo de configuração esteja presente no local especificado.
>
>5.  Clique em `Apply` para salvar as alterações e em `Run` para executar sua aplicação com a nova configuração do Log4j.
>
>Isso deve garantir que o Log4j seja inicializado corretamente em sua aplicação Spring Boot e que o arquivo de configuração `log4j2.xml` seja usado para registrar as mensagens de log.


# Rotacionar logs

A rotação de logs é uma técnica usada para evitar que arquivos de log cresçam indefinidamente. O Log4j permite que você configure a rotação de logs com várias opções diferentes, incluindo o tamanho máximo do arquivo, o número máximo de arquivos de log e o intervalo de tempo de rotação. Aqui está um exemplo de como você pode configurar a rotação de logs no Log4j:

1.  Crie um arquivo de configuração em XML com o nome `log4j2.xml` e adicione o seguinte conteúdo:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
  <Appenders>
    <RollingFile name="RollingFile" fileName="logs/app.log"
      filePattern="logs/app-%d{yyyy-MM-dd}.log.gz">
      <PatternLayout pattern="%d %p %c{1.} [%t] %m%n" />
      <Policies>
        <SizeBasedTriggeringPolicy size="10 MB" />
      </Policies>
      <DefaultRolloverStrategy max="10"/>
    </RollingFile>
  </Appenders>
  <Loggers>
    <Root level="info">
      <AppenderRef ref="RollingFile" />
    </Root>
  </Loggers>
</Configuration>
```

Neste exemplo, estamos usando o appender `RollingFile` para configurar a rotação de logs. A configuração `fileName` define o caminho para o arquivo de log, enquanto a configuração `filePattern` define o padrão de nomeação dos arquivos de log gerados pela rotação. No exemplo acima, o arquivo de log é rolado diariamente, com o nome contendo a data.

A configuração `SizeBasedTriggeringPolicy` é usada para definir a política de rotação do arquivo de log com base no tamanho máximo do arquivo. Neste exemplo, o arquivo de log é rolado quando atinge 10 MB.

A configuração `DefaultRolloverStrategy` é usada para definir o número máximo de arquivos de log gerados pela rotação. Neste exemplo, o número máximo de arquivos de log é 10.

A configuração `Root` define o nível de gravidade padrão como INFO e aponta para o appender `RollingFile` como destino de logs.

Com essas configurações, o Log4j irá rolar o arquivo de log sempre que o tamanho máximo for atingido e manterá apenas um número limitado de arquivos de log. A rotação de logs é uma técnica essencial para manter seus arquivos de log organizados e gerenciáveis, e o Log4j torna fácil a configuração deste recurso em seu projeto Spring Boot.

Ao adicionar o sufixo .gz, o log rotacionado estará sendo complimido.

Se você quiser configurar a rotação de logs para ocorrer a cada hora, você pode alterar a configuração do `filePattern` para usar um formato de data que inclua horas, minutos e segundos. Além disso, você precisará ajustar a configuração do `SizeBasedTriggeringPolicy` e `DefaultRolloverStrategy` para atender às suas necessidades específicas.

Aqui está um exemplo de arquivo de configuração em XML que rotaciona o arquivo de log a cada hora:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
  <Appenders>
    <RollingFile name="RollingFile" fileName="logs/app.log"
      filePattern="logs/app-%d{yyyy-MM-dd-HH}.log.gz">
      <PatternLayout pattern="%d %p %c{1.} [%t] %m%n" />
      <Policies>
        <SizeBasedTriggeringPolicy size="10 MB" />
        <TimeBasedTriggeringPolicy />
      </Policies>
      <DefaultRolloverStrategy max="24"/>
    </RollingFile>
  </Appenders>
  <Loggers>
    <Root level="info">
      <AppenderRef ref="RollingFile" />
    </Root>
  </Loggers>
</Configuration>
```

Observe que a única alteração que fizemos em relação ao exemplo anterior é a alteração do `filePattern` para usar `%d{yyyy-MM-dd-HH}` em vez de `%d{yyyy-MM-dd}`. Isso significa que o arquivo de log será rolado a cada hora, com o nome do arquivo contendo a data e a hora.

Também adicionamos um `TimeBasedTriggeringPolicy` para garantir que o arquivo de log seja rolado a cada hora, independentemente do tamanho do arquivo. Além disso, ajustamos o `DefaultRolloverStrategy` para manter até 24 arquivos de log, ou seja, manter as últimas 24 horas de logs.

Com essas configurações, o Log4j irá rolar o arquivo de log a cada hora, mantendo os logs organizados e gerenciáveis.

# Expurgo de logs

Para configurar o expurgo (ou exclusão) automática de arquivos de log antigos com o Log4j, você pode usar o `Delete` action do plugin `DefaultRolloverStrategy`. O `Delete` action é acionado quando o número máximo de arquivos de log é atingido, e os arquivos mais antigos serão excluídos para permitir que novos arquivos de log sejam criados.

Para configurar o expurgo automático, você pode definir a propriedade `max` do `DefaultRolloverStrategy` com o número máximo de arquivos de log que deseja manter e adicionar o `Delete` action como uma política adicional de rotação de arquivo. Aqui está um exemplo de como configurar o expurgo de logs:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
  <Appenders>
    <RollingFile name="RollingFile" fileName="logs/app.log"
      filePattern="logs/app-%d{yyyy-MM-dd}.log.gz">
      <PatternLayout pattern="%d %p %c{1.} [%t] %m%n" />
      <Policies>
        <SizeBasedTriggeringPolicy size="10 MB" />
        <TimeBasedTriggeringPolicy />
      </Policies>
      <DefaultRolloverStrategy max="30">
        <Delete basePath="logs/" maxDepth="1">
          <IfFileName glob="app-*.log.gz" />
          <IfLastModified age="30d" />
        </Delete>
      </DefaultRolloverStrategy>
    </RollingFile>
  </Appenders>
  <Loggers>
    <Root level="info">
      <AppenderRef ref="RollingFile" />
    </Root>
  </Loggers>
</Configuration>
```

Nesse exemplo, o `max` do `DefaultRolloverStrategy` foi definido como 30, o que significa que o Log4j manterá no máximo 30 arquivos de log. Além disso, adicionamos o `Delete` action como uma política adicional de rotação de arquivo.

O `Delete` action exclui arquivos de log mais antigos quando o número máximo de arquivos é atingido. Nesse caso, estamos definindo a pasta base para procurar os arquivos com `basePath="logs/"`, e configurando para excluir apenas arquivos que correspondem a `app-*.log.gz` com a tag `<IfFileName glob="app-*.log.gz" />`.

Além disso, estamos definindo uma política de exclusão baseada no tempo com a tag `<IfLastModified age="30d" />`, o que significa que arquivos com mais de 30 dias serão excluídos.

Com essas configurações, o Log4j irá manter no máximo 30 arquivos de log e excluir automaticamente arquivos mais antigos que não atendam a essas condições, garantindo que seus logs sejam gerenciados de forma eficiente e ocupem espaço mínimo em disco.

É possível definir o `age` (idade) para horas ao configurar a política de exclusão do Log4j. Para isso, basta definir a unidade de tempo como "hours" (horas) ao usar a tag `<IfLastModified>`.

Por exemplo, para excluir arquivos de log que têm mais de 24 horas de idade, você pode configurar a tag `<IfLastModified>` da seguinte maneira:

```xml
<Delete basePath="logs/" maxDepth="1">
  <IfFileName glob="app-*.log.gz" />
  <IfLastModified age="24" unit="hours" />
</Delete>
```

Dessa forma, o Log4j excluirá automaticamente os arquivos de log mais antigos que têm mais de 24 horas de idade a partir do momento em que a exclusão é acionada. Se você quiser configurar um valor de idade diferente, basta ajustar o valor numérico e a unidade de tempo para atender às suas necessidades.

# Filtrar mensagens dos logs

Sim, o Log4j permite filtrar mensagens nos logs com o uso de filtros configuráveis. Os filtros do Log4j são classes que implementam a interface `org.apache.logging.log4j.core.Filter` e permitem que você especifique as condições nas quais as mensagens de log serão permitidas ou negadas.

Existem vários tipos de filtros disponíveis no Log4j, incluindo filtros baseados em nível, filtros baseados em expressões regulares e filtros personalizados. Aqui está um exemplo de como configurar um filtro baseado em nível no arquivo `log4j2.xml`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
  <Appenders>
    <Console name="Console" target="SYSTEM_OUT">
      <PatternLayout pattern="%d %p %c{1.} [%t] %m%n" />
    </Console>
  </Appenders>
  <Loggers>
    <Root level="debug">
      <AppenderRef ref="Console" />
    </Root>
    <Logger name="com.example" level="info">
      <AppenderRef ref="Console" />
      <Filters>
        <ThresholdFilter level="error" onMatch="DENY" onMismatch="NEUTRAL" />
      </Filters>
    </Logger>
  </Loggers>
</Configuration>
```

Nesse exemplo, estamos usando um filtro baseado em nível para permitir que apenas as mensagens com um nível de log igual ou superior a "error" sejam registradas para o logger com o nome "com.example". Isso significa que as mensagens de log com um nível de log inferior a "error" serão negadas e não serão registradas.

A configuração do filtro é feita dentro do elemento `<Logger>` que representa o logger que deseja configurar. O filtro é adicionado com a tag `<Filters>` e pode conter um ou mais filtros.

No exemplo acima, estamos adicionando um filtro de limite (`ThresholdFilter`) que verifica se o nível da mensagem de log está abaixo do nível "error". O atributo `onMatch="DENY"` significa que as mensagens que correspondem ao filtro serão negadas, enquanto o atributo `onMismatch="NEUTRAL"` significa que as mensagens que não correspondem ao filtro serão permitidas.

Essa é apenas uma maneira de configurar filtros no Log4j, e existem muitos outros tipos de filtros e maneiras de configurá-los. Com a filtragem, você pode personalizar os registros de log para atender às suas necessidades e ajudar a encontrar rapidamente informações úteis nos logs.

# Diferenças entre log4j e log4j2

O Log4j e o Log4j2 são frameworks de registro de log para aplicações Java, desenvolvidos pela Apache Software Foundation. Embora compartilhem um nome semelhante e sejam projetados para realizar tarefas semelhantes, existem algumas diferenças significativas entre eles:

1.  Arquitetura: O Log4j2 foi reescrito do zero, com uma arquitetura moderna que é baseada em threads e eventos. Isso torna o Log4j2 muito mais rápido e escalável do que o Log4j, especialmente em aplicativos de alta carga.
    
2.  Configuração: O Log4j2 tem uma configuração mais avançada e flexível do que o Log4j. A configuração do Log4j2 pode ser feita em arquivos de configuração XML, JSON ou YAML, enquanto o Log4j usa apenas arquivos de propriedades. Além disso, o Log4j2 suporta recarga automática de configurações e permite a separação de configurações por contextos.
    
3.  Funcionalidades: O Log4j2 oferece algumas funcionalidades adicionais que não estão disponíveis no Log4j, como suporte a anotações para registro de log em classes e métodos específicos, suporte a marcadores para filtragem de log mais sofisticada, suporte a ThreadContext para registro de informações de contexto em logs, entre outras.
    
4.  Compatibilidade: O Log4j2 é compatível com o Log4j e é possível migrar para o Log4j2 a partir do Log4j sem grandes dificuldades. No entanto, como o Log4j2 é uma reescrita completa, há algumas diferenças na API, o que significa que pode ser necessário modificar o código existente ao migrar.
    

Em resumo, o Log4j2 é uma versão mais moderna, rápida e flexível do Log4j, com mais recursos e melhorias em relação ao seu antecessor. No entanto, a escolha entre o Log4j e o Log4j2 dependerá das necessidades e requisitos do seu projeto específico.