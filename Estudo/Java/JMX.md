#Java #Monitoramento 

JMX (Java Management Extensions) é uma tecnologia Java padrão que permite monitorar e gerenciar aplicativos em tempo de execução. O JMX fornece um conjunto de APIs e protocolos para monitorar, gerenciar e instrumentar aplicativos Java em tempo de execução.

O JMX permite expor informações de gerenciamento (como métricas, status e configurações) como [[beans JMX | MBean]] (Managed Beans) para que possam ser acessadas por ferramentas de gerenciamento, como consoles de gerenciamento remoto e ferramentas de monitoramento. O [[beans JMX | MBean]] é um objeto gerenciado que expõe atributos e operações para gerenciamento.

O JMX também fornece uma arquitetura de notificação baseada em eventos, que permite que os aplicativos sejam notificados de mudanças em um [[beans JMX | MBean]]. Por exemplo, um aplicativo pode ser notificado quando um recurso se torna indisponível ou quando o uso de memória atinge um limite crítico.

O JMX é amplamente utilizado em sistemas de gerenciamento de aplicativos empresariais para monitorar e gerenciar aplicativos Java em tempo de execução. O Spring Boot, por exemplo, usa o JMX para expor informações de monitoramento e gerenciamento do aplicativo através do [[Spring Actuator |Spring Boot Actuator]]. O JMX também é usado em outras tecnologias Java, como o [[Apache Tomcat]] e o [[Apache ActiveMQ]].

## Como o JMX notifica a aplicação de que houve mudanças em um MBean

O JMX fornece uma arquitetura de notificação baseada em eventos para permitir que os aplicativos sejam notificados de mudanças em um MBean. Existem dois tipos de notificação no JMX: notificação síncrona e notificação assíncrona.

A notificação síncrona é gerada imediatamente após uma operação ser executada em um [[beans JMX|MBean]]. A notificação é enviada para todos os clientes registrados para receber notificações do MBean. O cliente pode recuperar informações adicionais do MBean sobre a mudança que ocorreu.

A notificação assíncrona é gerada quando um [[beans JMX|MBean]] atinge um limite de disparo ou quando ocorre uma condição especificada. O MBean envia uma notificação para um ou mais ouvintes registrados. O ouvinte pode executar uma ação específica em resposta à notificação. As notificações assíncronas são úteis para monitorar recursos críticos e tomar ações proativas em resposta a eventos importantes.

Para receber notificações de um MBean, o aplicativo Java precisa se registrar como um ouvinte de notificação JMX. O registro pode ser feito por meio de uma chamada de API Java usando a interface JMXConnector ou por meio de um arquivo de configuração JMX.

Existem duas maneiras principais de se registrar para receber notificações JMX:

1.  Por meio de uma chamada de API Java usando a interface JMXConnector:

```java
JMXServiceURL url = new JMXServiceURL("service:jmx:rmi:///jndi/rmi://localhost:9999/server");
JMXConnector jmxc = JMXConnectorFactory.connect(url, null);
MBeanServerConnection mbsc = jmxc.getMBeanServerConnection();

ObjectName mbeanName = new ObjectName("com.example:type=MyMBean");
mbsc.addNotificationListener(mbeanName, listener, null, null);
```

Nesse exemplo, a aplicação se conecta a um servidor JMX remoto em "localhost" na porta 9999 e adiciona um ouvinte de notificação ao MBean com o nome "com.example:type=MyMBean". O ouvinte é uma instância de uma classe que implementa a interface NotificationListener.

2.  Por meio de um arquivo de configuração JMX:

O JMX também permite que a aplicação se registre como um ouvinte de notificação por meio de um arquivo de configuração JMX. O arquivo de configuração é um arquivo XML que especifica as propriedades do servidor JMX e os MBeans aos quais a aplicação deseja se conectar. O arquivo de configuração pode ser carregado pela aplicação usando a classe JMXConnectorFactory.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:jee="http://www.springframework.org/schema/jee"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
        http://www.springframework.org/schema/jee http://www.springframework.org/schema/jee/spring-jee-4.3.xsd">

    <bean id="jmxConnectorServer" class="org.springframework.jmx.support.ConnectorServerFactoryBean">
        <property name="serviceUrl" value="service:jmx:rmi:///jndi/rmi://localhost:9999/server"/>
        <property name="registerMBeans" value="true"/>
        <property name="objectName" value="com.example:type=MyMBean"/>
        <property name="notificationListenerMappings">
            <map>
                <entry key="javax.management.NotificationListener" value-ref="myNotificationListener"/>
            </map>
        </property>
    </bean>

    <bean id="myNotificationListener" class="com.example.MyNotificationListener"/>
</beans>

```

Nesse exemplo, a aplicação usa o Spring Framework para configurar um servidor JMX remoto em "localhost" na porta 9999 e registra um ouvinte de notificação para o MBean com o nome "com.example:type=MyMBean". O ouvinte é uma instância de uma classe que implementa a interface NotificationListener.

>[!tip]- Entendendo melhor
>MBean é uma interface que define um conjunto de atributos e operações que permitem monitorar e gerenciar objetos em tempo de execução. Para que sua aplicação receba notificações de um MBean, você precisa registrar um objeto que implemente uma interface chamada `NotificationListener`.
>
>O registro do objeto `NotificationListener` é feito através de uma classe chamada `MBeanServer`, que gerencia o registro e o acesso aos MBeans. Para obter uma instância da classe `MBeanServer`, você pode usar um método estático chamado `MBeanServerFactory.createMBeanServer()`.
>
>Depois de obter a instância do `MBeanServer`, você precisa registrar seu objeto `NotificationListener` usando o método `addNotificationListener()`. Este método recebe três argumentos: o nome do MBean, o objeto `NotificationListener` e um objeto `NotificationFilter` que pode ser usado para filtrar as notificações.
>
Por exemplo, o código a seguir mostra como registrar um objeto `NotificationListener` em um MBean chamado "MeuMBean":
>
A arquitetura de notificação do JMX é uma das características mais poderosas da tecnologia. Ela permite que os aplicativos Java sejam notificados de mudanças críticas em tempo real, para que possam tomar ações proativas e minimizar os riscos de interrupções no sistema.

>[!Example]- Exemplo
```java
MBeanServer mbs = MBeanServerFactory.createMBeanServer();
ObjectName mbeanName = new ObjectName("MeuMBean:type=MeuMBean");
NotificationListener listener = new MeuListener();
mbs.addNotificationListener(mbeanName, listener, null, null);
```

Neste exemplo, `MeuListener` é a classe que implementa a interface `NotificationListener`. Quando um evento é disparado no MBean registrado, o método `handleNotification()` da classe `MeuListener` será chamado.

