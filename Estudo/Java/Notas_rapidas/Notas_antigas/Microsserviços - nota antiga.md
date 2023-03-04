#### Definição:
![[Pasted image 20230221094223.png]]

![[Pasted image 20230221094304.png]]

![[Pasted image 20230221094334.png]]

![[Pasted image 20230221100136.png]]

![[Pasted image 20230221100216.png]]

![[Pasted image 20230221100551.png]]

![[Pasted image 20230221100615.png]]

#### Uso de monolitico:
https://www.etsi.org/newsroom/press-releases/1673-2019-10-etsi-introduces-a-new-end-to-end-architectural-framework-for-network-and-service-automation
https://www.etsy.com/
https://en.wikipedia.org/wiki/Etsy

#### Utilizando properties no código
1 - Crie uma classe com as anotações @Component e @ConfigurationProperties
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

O valor de @ConfigurationProperties deve ser o nome da propriedade. A Própria classe faz o bind dos atributos com as demais propriedades da configuração.

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
