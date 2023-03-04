#### Principios básicos
- Cenario - Inicialização das variáveis
- Ação - Execução do código em si
- Validação - verificar se o resultado é o esperado

#### FIRST
**F**ast - O teste deve ser de execução rápida
**I**ndependent - Os testes devem ser indepedentes um do outro, podem ser executado sem ordem
**R**epeatable - Os testes podem ser executados várias vezes
**S**elf-Verifying - Os testes devem ser capazes de verificar seus resultados
**T**imely - Tempo oportuno

*Para um código ser um teste o mesmo deve atender ao três principios e ao FIRST*

#### Padrão Unit
- TestRunner
- TestFixure - Inicialização
- TestSuites
- TestResultFormatter
- Assertions - Verificação do resultado

#### Organização
- Colocar todos os testes referentes a métodos de uma calsse em um classe de nome semelhante
- Colocar os testes dentro de pacotes com o mesmo nome do pacote da classe a ser testada.
	- As classes de teste poderão ter acessos a variáveis publicas, protected e default.
- Colocar os testes na pasta de testes.
-  Duas formas de trabalhar com os testes:
	- Um método para cada assertiva (O cenário e a ação serão repetidos)
	- Uso de @Rule para que o teste não pare no primeiro erro

#### Falha x Erro
Falha - Quando uma das assertivas apresenta resultado diferente do esperado
Erro - Quando ocorre um erro que impede dos testes prosseguirem

#### Testando exceções:
Três métodos:
- Solução elegante - @Teste possui a classe de erro lançada. Util quando o erro é único e apenas ele é lançado.
```java
//Forma elegante
	@Test(expected = FilmeSemEstoqueException.class)
	public void testeLocacaoFilmeSemEstoque() throws Exception {
		//cenario
		LocacaoService service = new LocacaoService();
		Usuario usuario = new Usuario("Usuario 1");
		Filme filme = new Filme("Filme 1", 0, 5.0);
		
		//ação
		Locacao locacao;
		//lança o erro no método alugarFilme
		locacao = service.alugarFilme(usuario, filme);
	}
```

- Solução robusta - O método que lança o erro é tratado por um try e catch. É necessário o uso do fail() no try. No catch usamos assertThat para pegar o erro com a mensagem que desejamos. Serve para verificar uma exceção generica que só muda a mensagem. Provem maior controle sobre o teste
```Java
@Test
	public void testeLocacaoUsuarioVazio() throws FilmeSemEstoqueException {
		
		//cenario
		LocacaoService service = new LocacaoService();
		Filme filme = new Filme("Filme 1", 1, 5.0);
		
		//ação
		try {
			Locacao locacao = service.alugarFilme(null, filme);
			fail();
		} catch (LocadoraException e) {
			assertThat(e.getMessage(), is("Usuario vazio"));
		}
	}
```

- Solução nova - Faz uso da @Rule ExpectedException. Espera um erro, e quando o mesmo ocorre dá como sucesso no teste. Semelhante ao uso da solução robusta, mas não permite muito controle.
```Java
@Rule
public ExpectedException expectedException = ExpectedException.none();

@Test
public void testeLocacaoFilmeVazio() throws FilmeSemEstoqueException, LocadoraException {

	//cenario
	LocacaoService service = new LocacaoService();
	Usuario usuario = new Usuario("Usuario 1");
	expectedException.expect(LocadoraException.class);
	expectedException.expectMessage("Filme vazio");
	
	//ação
	Locacao locacao = service.alugarFilme(usuario, null);
	
}

```

*Eu espero que ele lance uma exceção quando determinada entrada for fornecida*

#### @Before
Sempre executa o método antes de cada teste. Em cada Teste cria uma nova instancia. Para compartilhar a mesma instancia é necessário o uso de variáveis staticas.

#### @After
Sempre executa o método no final de cada teste.

#### Ordem de execução dos testes
- Tirar o @Test dos testes e deixar em apenas um método que chama os demais métodos na ordem desejada.
- Executar os testes em ordem alfabetica. @FixMethodOrder(MethodSorters.NAME_ASCENDING)


### Metodo para criar testes - TDD
TDD (Test Driven Development) - Desenvolvimento orientado a testes.
Ao criar uma funcionalidade desenvolver primeiro o teste da funcionalidade, depois desenvolver a funcionalidade. Desenvolver testes que verifiquem o comportamento da funcionalidade, como quando deve ser gerado a exceção e quando um valor incorreto for inserido.

Red - Green - Refactor
Testes - codigo - refatoração
1. Desenvolver o teste para cobrir uma funcionalidade do aplicação. Testar para dar erro
2. Desenvolver o codigo da funcionalidade que irá fazer uso do teste. Obter ok do teste
3. Refatorar o código, pois foi feito de momento e pode ser melhorado. Após testar de novo

#### Criando proprio matcher
Para melhorar a legibilidade do seu código ou centralizar uma regra especifica do seu projeto.
1. Criar pacote matchers
2. Criar classe matcher extendendo do TypeSafeMatcher (org.hamcrest)
3. Adicionar Generic na classe do matcher do tipo do primeiro parâmetro passado no assertThat.
4. O mesmo tipo do generic deve ser passado no método matchesSafely.
5. Criar um construtor que receberá o segundo parametro do assertThat, se necessários
6. No método matchesSafely é onde a comparação do match irá ser realizada.
7. Crie uma classe com métodos estaticos para concentrar todos os matchers.
8. Para alterar a mensagem de erro é necessário alterar o método describeTo.

```Java
public class DiaSemanaMatcher extends TypeSafeMatcher<Date> {

	private Integer diaSemana;
	
	public DiaSemanaMatcher(Integer diaSemana) {
		this.diaSemana = diaSemana;
	}
	
	public void describeTo(Description description) {
		Calendar data = Calendar.getInstance();
		data.set(Calendar.DAY_OF_WEEK, diaSemana);
		String dataExtenso = data.getDisplayName(Calendar.DAY_OF_WEEK, Calendar.LONG, new Locale("pt", "BR"));
		description.appendText(dataExtenso);
	}

	@Override
	protected boolean matchesSafely(Date data) {
		return DataUtils.verificarDiaSemana(data, diaSemana);
	}

}
```

#### Assume e @Ignore
Utilize @Ignore para ignorar um teste em específico
Utilize **Assume** quando quiser que um teste execute de acordo como uma condição. Se o teste deve ser executar quando uma condição for verdadeira, utilize **Assume.assumeTrue**. Se for quando uma condição der false **Assume.assumeFalse**.

#### Criando dados para Teste
- Criação de classes builder utilizando chain method e fluent interface.
```Java
package br.ce.wcaquino.builders;

import br.ce.wcaquino.entidades.Filme;

public class FilmeBuilder {
	private Filme filme;
	
	private FilmeBuilder() {}
	
	public static FilmeBuilder umFilme() {
		FilmeBuilder builder = new FilmeBuilder();
		builder.filme = new Filme();
		builder.filme.setEstoque(2);
		builder.filme.setNome("Filme 1");
		builder.filme.setPrecoLocacao(5.0);
		return builder;
	}
	
	public Filme agora() {
		return filme;
	}
}

```


### Metricas
#### Percentual de aceite
Quantidade de testes realizados dividido por testes de sucesso.

#### Cobertura de Teste
Quantidade de funções cobertas pelos testes no projeto.