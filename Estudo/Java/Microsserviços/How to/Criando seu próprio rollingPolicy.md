#microsserviços #springboot #Monitoramento 

Para criar um novo `RollingPolicy` personalizado no Logback, você precisa seguir os seguintes passos:

1.  Criar uma classe que implemente a interface `RollingPolicy`.
    
2.  Implementar os métodos definidos na interface `RollingPolicy`, que são:
    

-   `void start()` - método que é invocado quando a política de rotação é inicializada.
    
-   `void rollover()` - método que é invocado quando a rotação de arquivos é acionada.
    
-   `boolean isTriggeringEvent(ILoggingEvent event)` - método que é usado para determinar quando a rotação de arquivos deve ser acionada.
    
-   `String getActiveFileName()` - método que retorna o nome do arquivo de log atual.
    

3.  Configurar o seu `RollingPolicy` personalizado no arquivo de configuração do Logback.

Aqui está um exemplo de como criar uma política de rotação personalizada que roda o arquivo de log toda vez que ele atinge um tamanho máximo:

```java
import ch.qos.logback.core.rolling.RollingFileAppender;
import ch.qos.logback.core.rolling.RollingPolicy;
import ch.qos.logback.core.rolling.helper.CompressionMode;
import ch.qos.logback.core.rolling.helper.FileNamePattern;
import ch.qos.logback.core.rolling.helper.SizeAndTimeBasedFNATP;

public class SizeBasedRollingPolicy implements RollingPolicy {

  private static final long MAX_FILE_SIZE = 1024 * 1024 * 10; // 10 MB

  private RollingFileAppender appender;
  private String fileNamePattern;

  public void setFileNamePattern(String fileNamePattern) {
    this.fileNamePattern = fileNamePattern;
  }

  @Override
  public void start() {
    SizeAndTimeBasedFNATP sizeAndTimeBasedFNATP = new SizeAndTimeBasedFNATP();
    sizeAndTimeBasedFNATP.setMaxFileSize(MAX_FILE_SIZE);

    FileNamePattern fileNamePattern = new FileNamePattern(this.fileNamePattern, context);
    fileNamePattern.setPostfix(".log");

    appender.setRollingPolicy(sizeAndTimeBasedFNATP);
    appender.setTriggeringPolicy(sizeAndTimeBasedFNATP);
    appender.setFileNamePattern(fileNamePattern.convertMultipleArguments(0, null, null));
  }

  @Override
  public void rollover() {
    appender.rollOver();
  }

  @Override
  public boolean isTriggeringEvent(ILoggingEvent event) {
    return appender.getTriggeringPolicy().isTriggeringEvent(appender.getFile(), event);
  }

  @Override
  public String getActiveFileName() {
    return appender.getFile();
  }

  @Override
  public void setParent(RollingFileAppender parent) {
    this.appender = parent;
  }

  @Override
  public void stop() {
    // Do nothing
  }

  @Override
  public boolean isStarted() {
    return true;
  }
}
```

Nesse exemplo, a classe `SizeBasedRollingPolicy` implementa a interface `RollingPolicy` e define uma política de rotação baseada no tamanho do arquivo. O tamanho máximo do arquivo é definido como `10 MB` (variável `MAX_FILE_SIZE`).

No método `start()`, criamos uma instância de `SizeAndTimeBasedFNATP` e definimos o tamanho máximo do arquivo para o valor definido em `MAX_FILE_SIZE`. Em seguida, definimos o padrão de nome do arquivo usando a classe `FileNamePattern`.

No método `rollover()`, chamamos o método `rollOver()` da instância de `RollingFileAppender`.

No método `isTriggeringEvent()`, verificamos se a política de acionamento atual é acionada para um determinado evento.

No método `getActiveFileName()`, retornamos o nome do arquivo de log ativo.

Depois de criar sua política de rotação personalizada, você pode configurá-la no arquivo de configuração do Logback usando a tag `<rollingPolicy>`, conforme explicado anteriormente.

```xml
<configuration>

  <appender name="fileAppender" class="ch.qos.logback.core.FileAppender">
    <file>logs/myapp.log</file>
    <encoder>
      <pattern>%d %p %c{1.} [%t] %m%n</pattern>
    </encoder>
    <rollingPolicy class="com.example.myapp.SizeBasedRollingPolicy">
      <fileNamePattern>logs/myapp.%d{yyyy-MM-dd_HH-mm-ss}.%i</fileNamePattern>
    </rollingPolicy>
  </appender>

  <logger name="com.example.myapp" level="INFO">
    <appender-ref ref="fileAppender" />
  </logger>

</configuration>
```

Nesse exemplo, a política de rotação personalizada `SizeBasedRollingPolicy` é definida na tag `<rollingPolicy>` dentro do appender `fileAppender`. O valor do atributo `class` deve apontar para o caminho completo da classe que implementa a política de rotação personalizada.

Dentro da tag `<rollingPolicy>`, você pode definir os atributos da política de rotação personalizada. Neste exemplo, definimos o padrão de nome do arquivo com a tag `<fileNamePattern>`, que inclui a data e hora atual formatada, bem como o índice do arquivo.

Em seguida, o appender `fileAppender` é configurado para usar a política de rotação personalizada definida na tag `<rollingPolicy>`.

Por fim, o logger `com.example.myapp` é configurado para usar o appender `fileAppender` e gravar logs com o nível `INFO`.

