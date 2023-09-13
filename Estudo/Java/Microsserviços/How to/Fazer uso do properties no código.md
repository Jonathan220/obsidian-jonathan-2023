#microsserviços #springboot 

É Possível fazer uso de variáveis criadas no properties de um microsserviço no código da aplicação.
Existem duas maneiras:

1. Através do uso da anotação @Value

```java
@Value("${test.config}")
	private String testConfig;
```

2. Através da criação de uma classe de configuração com as anotações @Component e @ConfigurationProperties com o nome da propriedade desejada. A própria classe faz o bind das variáveis com as propriedades no properties.

```java
@Component
@ConfigurationProperties("greeting-service")
public class GreetingConfig {
	
	private String greeting;
	private String defaultValue;
	
	public GreetingConfig() {}

	public String getGreeting() {
		return greeting;
	}

	public void setGreeting(String greeting) {
		this.greeting = greeting;
	}

	public String getDefaultValue() {
		return defaultValue;
	}

	public void setDefaultValue(String defaultValue) {
		this.defaultValue = defaultValue;
	}	
}

```

Durante o resto do código, você pode usar a classe de configuração em seu código.

```java
@RestController
public class GreetingController {
    private static final String template = "%s, %s!";
    private final AtomicLong counter = new AtomicLong();
    
    @Autowired
    private GreetingConfig greetingConfig;

    @RequestMapping("/greeting")
    public Greeting greeting(@RequestParam(value = "name", defaultValue = "") String name){
    	if(name.isEmpty()) name = greetingConfig.getDefaultValue();
        return new Greeting(counter.incrementAndGet(), String.format(template, greetingConfig.getGreeting(), name));
    }
}
```

Pode ser feito utilizando o **properties.application**, mas o ideal é o uso do **properties.yaml**.

```yaml
spring:
  application:
    name: greeting-service
  cloud:
    config:
      enabled: false
management:
  endpoints:
    web:
      exposure:
        include:
        - '*'
greeting-service:
  greeting: "Olá"
  default-value: "Mundo"
```