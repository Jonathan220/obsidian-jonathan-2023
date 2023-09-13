
## Instalar docker


## Comandos

### Executar um container

```shell
docker run imagem
```

### Listar os containers em execução

```shell
docker ps
```

### Listar todos os containers em execução ou parados

```shell
docker ps -a
```

Quando um container é levantado, o mesmo permanece rodando somente até o comando que estiver executando estiver em andamento. Quando o comando é encerrado o container deixa de rodar.

### Customizar container por comando

Para criar um container que execute um comando que deseja faça como no código abaixo.

```shell
docker run -it ubuntu bash
```

O comando acima irá criar um container com a imagem **ubuntu** rodando o comando **bash** de forma interativa (**-it**), ou seja, você poderá interagir com o container.

### Configurando uma porta

As portas do container são diferentes das portas do seu computador. Para acessar um container a partir da porta 8080 do seu computador você precisará configurar uma porta de publicação no container.

```shell
docker run -p 8080:80 nginx
```

No comando acima estamos configurando no container uma porta 8080 do nosso computador para publicar o que vem da porta 80 do container.


### Executando um comando dentro de um container

Para executar comandos em um container utilize o comando abaixo.

```shell
docker exec nome_do_container comando
```

No exemplo acima o comando pode ser um ls para listar o conteúdo do sistema de arquivos do container em execução.

Para entre de fato em um container execute o comando:

```shell
docker exec -it nome_do_container bash
```

### Criando volume

Os dados alterados em um container pode ser perdidos uma vez que o mesmo é reiniciado. Isso acontece pois ao executar o `docker run` um novo container é criado a partir da imagem que você baixou.

Para evitar a perda de dados alterados em um container utilize volumes, que nada mais são do que pastas na máquina em que o container está rodando que salvam as configurações do mesmo.

```shell
docker run -p 8080:80 -v caminho_na_sua_maquina:caminho_no_container nginx
```

No comando acima toda alteração na pasta **caminho_da_sua_maquina** resultará em alterações no **caminho_no_container** e vice e versa.

Você pode também já criar um volume antes de levantar o container:

```shell
docker volume create VOL1
```
 
### Criando uma rede no docker

Para criar uma rede com o nome REDE1 digite o comando abaixo:

```shell
docker network create REDE1
```

Você pode no comando adicionar o driver do seu interesse.

```shell
docker network create REDE1 --driver bridge
```

Para lista as redes disponíveis:

```shell
docker network list
```

### Criando um container com banco de dados Postgres

Para criar um container com o banco de dados Postgres, que tenha suas informações persistidas na máquina e esteja numa rede de modo que possa se comunicar com outros containers, execute o comando abaixo:

```shell
docker run -d --name postgresql -e POSTGRES_USER=usuario -e POSTGRES_PASSWORD=senha1234 --network REDE1 --volume VOL1:/bitnami/postgresql bitnami/postgresql:latest
```

A opção `-d` (detach) executa o container em background e imprime o seu ID.

O comando `--name` refere-se ao nome do container.

Os comando `-e` definem as variáveis de ambientes que serão aplicadas na instalação. No comando acima criamos duas variáveis de ambiente:

-  **POSTGRES_USER**– Definimos o nome de usuário do PostgreSQL
-  **POSTGRES_PASSWORD**– Definimos a senha de acesso ao PostgreSQL

O comando `--network` refere-se a nossa rede.

O comando `--volume` monta o volume que criamos com o diretório em que os dados são persistido no container.

No final adicionamos o nome da imagem e a sua versão.

Para inspecionar o container criado execute o comando abaixo:

```shell
docker inspect postgresql
```

Após instalar o PostgreSQL no Docker, vamos agora instalar o **phpPgAdmin**. Ou seja, o serviço que servirá para gerenciarmos o nosso banco de dados de forma prática, rápida e visual!

Portanto, utilize o terminal e realize os seguintes comandos para criar o container do phpPgAdmin no docker:

```shell
docker run -d --name phppgadmin --network REDE1 -p 8080:80 -p 443:8443 bitnami/phppgadmin-archived
```

Observe que alguns comandos já foram explicados no passo anterior. Portanto, definimos como nome do nosso container “phppgadmin “. Também definimos a rede como REDE1.

O comando **-p** serve para definirmos a porta em que nosso serviço vai rodar. O phpPgAdmin contém um pequeno webserver que roda na porta 80. Portanto, atribuímos as portas externas (que será utilizada no nosso navegador) 8080 e 443.

Por fim referenciamos a imagem desejada bitnami/phppgadmin-archived.

Para acessar o pgAdmin digite https://localhos:443. Depois no menu do lado esquerdo selecione o servido desejado.
### Criando sua própria imagem

Crie um arquivo chamado [[Dockerfile]]. No arquivo coloque o passo a passo para executar sua aplicação.

```shell
FROM openjdk:11
VOLUME /tmp
EXPOSE 8761
ADD ./target/hr-eureka-server-0.0.1-SNAPSHOT.jar hr-eureka-server.jar
ENTRYPOINT ["java","-jar","/hr-eureka-server.jar"]
```

Depois do arquivo criado execute o comando abaixo para montar a imagem.

```shell
docker build -t wesleywillians/nginx-imarsao13:latest .
```

O código acima builda uma imagem com o nome **wesleywillians/nginx-imarsao13** como sendo a última versão (latest) e pega o **Dockerfile** presente na pasta local (representado pelo . ).

Com a imagem construída basta criar um container a partir dela como você faz com qualquer outra imagem.

```shell
docker run -p 8080:80 wesleywillians/nginx-imarsao13:latest
```

Se quiser disponibiliza-la no docker hub basta executar o comando:

```shell
docker push wesleywillians/nginx-imarsao13:latest
```

A partir de agora qualquer pessoa que executar o comando acima poderá baixar a imagem somente executando o comando de execução.

#### FROM

Instrução obrigatória no dockerfile. Define qual o ponto de partida para a criação da imagem.

Caso seja necessário criar uma imagem do zero absoluto, sem fazer uso de qualquer imagem utilize o instrução abaixo:

```Dockerfile
FROM scratch
```

#### RUN

Pode ser executada uma ou mais vezes. Com essa instrução podemos definir quais serão os comandos executados durante a etapa de criação da imagem. Esses comandos só serão executados na criação da imagem e não na subida do container.

```Dockerfile
FROM ubuntu:18.04 
RUN apt-get update 
RUN apt-get install openjdk-8-jdk -y
```

Quando executo o comando `docker build .`, além de baixar a imagem do Ubuntu 18.04 para colocar na minha imagem, o processo de criação também executará os comandos para atualizar os repositórios do Ubuntu através do `apt-get update`, e para instalar o Java 8 utilizará o `apt-get install openjdk-8-jdk-y`. Ele executará uma série de downloads.

Qualquer container criado a partir dessa imagem terá o repositório do Ubuntu na mesma versão e o Java 8 instalado.

O RUN aceita parâmetros de dois jeitos:

`RUN apt-get install openjdk-8-jdk -y` ou `RUN ["apt-get", "install" "openjdk-8-jdk" ,"-y"]`

Ou seja, podemos passar os comandos separadamente entre aspas dentro dos colchetes. O resultado é o mesmo.

#### CMD e ENTRYPOINT

A instrução **CMD** é parecida com a **RUN**, no entanto a sua execução só ocorre quando o container é criado.

```Dockerfile
FROM ubuntu:18.04 
RUN apt-get update 
RUN apt-get install openjdk-8-jdk -y 
CMD touch arquivo-de-boas-vindas
```

No **Dockerfile** acima o comando **CMD** só será executado quando o container for criado e não durante a criação da imagem.

Se houver algum parâmetro de execução de comando durante a criação do container, este parâmetro sobrescreverá o **CMD**.

Se houver mais de um **CMD**, somente o último será executado.

O **Entrypoint** faz o mesmo que o **CMD**, porém seus parâmetros não são sobrescritos igual ao **CMD**.

#### ADD e COPY

O papel do ADD é fazer a cópia de um arquivo, diretório ou até mesmo fazer o download de uma URL de nossa máquina host e colocar dentro da imagem.

```Dockerfile
FROM ubuntu:18.04 
RUN apt-get update 
RUN ["apt-get", "install", "openjdk-8-jdk" ,"-y"] 
ADD arquivo-host arquivo-host-transferido
```

No exemplo acima o arquivo-host é copiado para a imagem com o nome arquivo-host-transferido.

Se levantarmos um container com esta imagem, dentro do container haverá o arquivo.

Caso o arquivo que esteja sendo passado seja um arquivo de extensão tar, ele fará a descompressão automaticamente.

#### EXPOSE

Há uma certa dúvida quanto ao uso dessa instrução. Muitas pessoas pensam que o **EXPOSE** serve para definir em qual porta nossa aplicação rodará dentro do container, mas na verdade o propósito é servir apenas para documentação.

Essa instrução não publica a porta efetivamente, já que o propósito dela é fazer uma comunicação entre quem escreveu o Dockerfile e quem rodará o container.

```Dockerfile
FROM ubuntu:18.04 
RUN apt-get update 
RUN apt-get install openjdk-8-jdk -y 
EXPOSE 8080
```

#### VOLUME

Essa instrução cria uma pasta em nosso container que será compartilhada entre o container e o host, funcionando do seguinte modo:

```Dockerfile
FROM ubuntu:18.04 
RUN apt-get update 
RUN apt-get install openjdk-8-jdk -y 
VOLUME /foo
```

Quando criarmos um container dessa imagem, ele criará uma pasta chamada foo.

Todo arquivo criado dentro dessa pasta será acessível a partir da máquina host no caminho `/var/lib/docker/volumes`.

#### WORKDIR

```Dockerfile
FROM ubuntu:18.04 
RUN apt-get update 
RUN ["apt-get", "install", "openjdk-8-jdk" ,"-y"] 
WORKDIR /pasta-qualquer COPY arquivo-host arquivo-host-transferido
```

Ao acessarmos o container gerado por essa imagem, teremos o seguinte resultado:

![[Pasted image 20230822215414.png]]

Ou seja, além de inicializarmos o container nessa pasta, o COPY colocou o arquivo dentro da pasta definida.
## Docker compose

Caso necessite de um ambiente complexo que faça uso de mais de uma aplicação, e as mesmas puderem ser rodadas em container, faça uso do [[docker compose]].

1. Crie o arquivo **docker-compose.yaml**.
2. Dentro do arquivo configure os serviços que deseja instalar.
3. Execute o arquivo com o comando abaixo. O comando deve ser executado dentro da pasta em que o arquivo do [[docker compose]] foi criado.

```shell
docker compose up
```

4. Para derrubar os containers execute o comando abaixo:

```shell
docker compose down
```

>[!Attention]+ Atenção
> O docker hub é um container registrer, ou seja, um site para registro e salvamento de imagens de containers. Existem diversos outros no mercado providos por ambientes na nuvem.
> 

