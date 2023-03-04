## Webservices
Aplicação interoperável que é hospedada e pode ser acessada através do protocolo http. Você pode enviar requisições através do browse, de um client ou através do terminal como o uso do comando curl.

São pedaços de software que posso disponibilizar via http e que podem ser consumidos através de diversos tipos de clients.

## SOAP x REST
O SOAP utiliza o envelope SOAP, enviando requisições pelo protocolo http para fazer chamadas RPC trafegando xml. O SOAP permite o uso de outros protocolos. É mais pesado que o REST.

O REST por sua vez faz chamadas através do protocolo http enviando diversos tipos de formatos.

## REST - Representational State Transfer
Modelo arquitetural de software para sistemas de hipermídia.

REST é um padrão arquitetural basicamente leve por natureza.

### Restrições do REST
1. Cliente e servidor separados
2. Stateless server - O servidor não deve guardar o estado do cliente. Cada requisição contém todas as informações necessárias para atende-la.
3. Cacheable - O cliente deve ser informado sobre as propriedades de cache de um recurso.
4. Interface uniforme - existe uma interface uniforme entre cliente e servidor.
 - Identificação de recursos (URI)
 - Manipulação de recursos a partir de suas representações.
 - Mensagens auto descritivas.
 - HATEOAS
 5. Sistema em camadas - Deve suportar conceitos como balanceamento de carga, proxies e firewalls.

## Request e Response

![[Pasted image 20220921081134.png]]

![[Pasted image 20220921081223.png]]

![[Pasted image 20220921081330.png]]

## Parâmetros

### Path params
Parâmetros passados na URL. São obrigatórios.
![[Pasted image 20220921081824.png]]

### Query params
São passados na URL como os Path params, mas são opcionais.
![[Pasted image 20220921082025.png]]

### Header params
São passados no cabeçalho da requisição.

### Body params
São enviados no corpo da requisição.


#### Obs:
Os path params e query params são utilizados para buscas. O body param é utilizado para operações de post, put e patch, para gravação e atualização.

### Http status
Como a API responde aos clientes oque ocorreu duranteo o processamento.
1xx - Informacionais
2xx - Sucesso
3xx - Redirecionamento
4xx - Erro de cliente
5xx - Erro de Servidor

Os mais usados:

200 - OK - Request de criação ou deleção executada com sucesso
201 - Created - Criação de uma fila, tópico ou session com sucesso
202 - Acepted
204 - No Content - Deleção de uma fila, tópico ou sessão bem sucedida, mas sem retorno de conteúdo

400 - Bad request - Path informado está em um formato incorreto, um parâmetro ou valor do corpo da requisição não está formatado corretamente ou um parâmetro obrigatório não foi informado
401 - Unauthorized - O criente não tem autorização
403 - Forbbiden - O criente não tem autorização
404 - Not found
405 - Method Not Alowed - O usuário não tem permissão acesso ao Path
409 - Conflict - Um objeto já foi criado com as mesma informações

500 - Internal Server Error - Ocorreu uma falha no servidor, na sua API.

## Verbos HTTP
![[Pasted image 20220923074014.png]]

### O verbo Patch
O verbo patch é utilizado para updates parciais, ous seja, quando você deseja atualizar somente um campo de um registro, enviar o objeto inteiro pode ser pesado.

### O verbo Head
Similar ao GET, porém retorna somente a response line e o header.

### O verbo Trace
Usado para debug. Usado para recuperar o conteúdo de uma requisição HTTP de volta.

### O verbo options
Utilizado pelo cliente para encontrar operações HTTP e outras opções suportadas pelo servidor.

### O verbo Connection
O verbo connect é usado pelo cliente para estabelecer uma conexão de rede com um servidor via http.

## Níveis de maturidade de Richardson
*RESTful* é o estado mais elevado de uma API REST.
Level 0: um endpoint para tudo
Level 1: um endpoint para cada recurso
Level 2: verbos http
Level 3: Hypermidia (HATEOAS)

![[Pasted image 20220923080626.png]]

## Swagger
Framework utilizado para documentar API REST.

https://swagger.io/specification/

## Autenticação

![[Pasted image 20220924094655.png]]

A autenticação faz uso de json web token.

![[Pasted image 20220924095057.png]]


## Versionamento
Criar versionamento da API. Para versionar uma API utilizamos a URL. Existem três formas de fazer isso.

- Versionamento por subdomninio. Onde especificamos a versão no ínicio da URL.

![[Pasted image 20220924095738.png]]

- Versionamento por Path. A versão é informada após a base URL. Atualmente é o modo mais utilizado.

![[Pasted image 20220924095902.png]]

- Versionamento por query param. Antigamente era utilizado, mas caiu em desuso. Legibilidade ruim quando a API tem muitos parâmetros.

![[Pasted image 20220924100147.png]]

- Versionamento por Header. Na especificação este é o modo correto. Pode utilizar o content-type. A URL é clean e não é DEV friendly.

![[Pasted image 20220924100333.png]]

- Versionamento por Hearder customizado.

![[Pasted image 20220924100745.png]]

## Boas práticas
- Paginação - performance
- Filtros
- Definir recursos logicos
- Tolerancia a falhas - A aplicação se recuperar de erros e responder ao usuário.
- Cache - guardar informações muito requisistadas - performance
- Conectividade - Sempre buscar meios de facilitar a sua conexão com a API
- Timeouts - evitar que o cliente fique pendurado na aplicação
- Documentação - HATEOAS e Swagger
- Utilizar SSL
- Versionamento
- testes e validação - Automatizar testes
- Self-service - Os próprios clientes conseguem escolher o que precisam consumir na sua API - Swagger e HATEOAS
- Marketing - Campanha de marketing
- Oferecer exportações em algum formato de arquivo
- I18n/Globalização (o que é?)
- Notificações - Para processamentos longos não manter o cliente pendurado na API. Enviar resposta informando que o processamento está ocorrendo (201). Quando o processamento estiver pronto envie a resposta por e-mail ou outro meio de comunicação.
- Monitore sua API - verificar gargalos
- Selecione a tecnologia adequada. Nem sempre a melhor tecnologia é a mais moderna.