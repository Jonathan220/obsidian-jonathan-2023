#git

![[Pasted image 20230520150810.png]]


# Configurando sua identificação no git

```shell
git config --global user.name "Seu nome"

git config --global user.email "Seu email de cadastro no github"
```

Para listar as informações de suas configurações:

```shell
git config --list
```


# Configurando para visualizar arquivos ocultos no windows

Iniciar -> Opções do explorador de arquivos
DESMARCAR: "Ocultar as extensões dos tipos de arquivos conhecidos"
MARCAR: "Mostrar arquivos, pastas e unidades ocultas"


# Configurar chave SSH para o GitHub

## Gerar uma chave SSH no seu computador

### Verificando se há chaves SSH

Verifique antes se já exitem chaves ssh em seu computador.

1. Abra o Git Bash
2. Insira `ls -al ~/.ssh` para ver se as chaves SSH existentes estão presentes.

```shell
$ ls -al ~/.ssh
# Lists the files in your .ssh directory, if they exist
```

3. Verifique a listagem do diretório para verificar se você já tem uma chave SSH pública. Por padrão, os nomes de arquivos de chaves públicas compatíveis para o GitHub são um dos mostrados a seguir.

-   _id_rsa.pub_
-   _id_ecdsa.pub_
-   _id_ed25519.pub_

4. Gere uma nova chave SSH ou faça o upload de uma chave existente.

-   Se você não tem um par de chave pública e privada compatível ou não deseja usar nenhum que esteja disponível, gere uma nova chave SSH.
    
-   Se um par de chaves pública e privada existente estiver listado (por exemplo, _id_rsa.pub_ e _id_rsa_) que você deseja usar para se conectar ao GitHub, você poderá adicionar a chave ao ssh-agent.

### Gerando uma nova chave SSH e adicionando-a ao agente SSH

Depois de verificar a existência de chaves SSH, é possível gerar uma nova chave SSH para autenticação e adicioná-la ao ssh-agent.

Ao gerar uma chave SSH, você pode adicionar uma frase secreta para proteger ainda mais a chave. Sempre que você usar a chave, deverá inserir a frase secreta. Se sua chave tiver uma frase secreta e você não quiser inserir a frase secreta sempre que usar a chave, poderá adicionar sua chave ao agente SSH. O agente SSH gerencia suas chaves SSH e lembra sua frase secreta.

Você pode gerar uma nova chave SSH no computador local. Após gerar a chave, você poderá adicionar a chave à sua conta em GitHub.com para habilitar a autenticação para operações Git por SSH.


1.  Abra Git Bash.
    
2.  Cole o texto abaixo, substituindo o endereço de e-mail pelo seu GitHub.
    
    ```shell
    $ ssh-keygen -t ed25519 -C "your_email@example.com"
    ```
    
    **Observação:** se estiver usando um sistema herdado que não dá suporte ao algoritmo Ed25519, use:
    
    ```shell
    $ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
    ```
    
    Isto cria uma nova chave SSH, usando o nome de e-mail fornecido como uma etiqueta.
    
    ```shell
    > Generating public/private ALGORITHM key pair.
    ```
    
    Quando for solicitado a inserir um arquivo para salvar a chave, pressione **Enter** para aceitar o local padrão do arquivo. Observe que, se você criou chaves SSH anteriormente, ssh-keygen pode pedir que você reescreva outra chave. Nesse caso, recomendamos criar uma chave SSH personalizada. Para fazer isso, digite o local do arquivo padrão e substitua id_ssh_keyname pelo nome da chave personalizada.

```shell
> Enter a file in which to save the key (/c/Users/YOU/.ssh/id_ALGORITHM):[Press enter]
```

3. (Opicional) No prompt, digite uma frase secreta segura. Ao fazer isso será cobrado a digitação da chave secreta sempre que for feito uso do github, uma maneira de evitar isso é com o uso do ssh-agent.

```shell
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
```


4. Antes de adicionar a sua chave ssh ao agente ssh verifique se o mesmo já está rodando e configurado para ser executado automáticamente. Para ver como configurar-lo veja na seção abaixo. Você pode iniciar o agente manualmente executando o comando abaixo no git bash.

```shell
# start the ssh-agent in the background
$ eval "$(ssh-agent -s)"
> Agent pid 59566
```

5. Adicione sua chave SSH privada ao ssh-agent. Se você criou sua chave com um nome diferente ou se estiver adicionando uma chave existente que tenha outro nome, substitua _id_ed25519_ no comando pelo nome do arquivo de chave privada.
    
    ```shell
    $ ssh-add ~/.ssh/id_ed25519
    ```

6. Adicione a chave SSH à sua conta em GitHub


### Trabalhar com frase secreta da chave SSH (configurando o agente ssh)

Você pode proteger suas chaves SSH e configurar um agente de autenticação para que não precise redigitar a senha toda vez que usar as chaves SSH.

Com as chaves SSH, se alguém conseguir acessar seu computador, o invasor terá acesso a todos os sistemas que usam essas chaves. Para incluir uma camada extra de segurança, adicione uma frase secreta à sua chave SSH. Para evitar inserir a frase secreta toda vez que você se conectar, você poderá salvar sua frase secreta com segurança no agente SSH.

Você pode executar `ssh-agent` automaticamente quando abre o shell do Bash ou do Git. Copie as seguintes linhas e cole-as no arquivo `~/.profile` ou `~/.bashrc` no shell do Git:

```bash
env=~/.ssh/agent.env

agent_load_env () { test -f "$env" && . "$env" >| /dev/null ; }

agent_start () {
    (umask 077; ssh-agent >| "$env")
    . "$env" >| /dev/null ; }

agent_load_env

# agent_run_state: 0=agent running w/ key; 1=agent w/o key; 2=agent not running
agent_run_state=$(ssh-add -l >| /dev/null 2>&1; echo $?)

if [ ! "$SSH_AUTH_SOCK" ] || [ $agent_run_state = 2 ]; then
    agent_start
    ssh-add
elif [ "$SSH_AUTH_SOCK" ] && [ $agent_run_state = 1 ]; then
    ssh-add
fi

unset env
```

Se a sua chave privada não estiver armazenada em um dos locais padrão (como `~/.ssh/id_rsa`), você precisará informar ao agente de autenticação SSH o local em que ela se encontra. Para adicionar sua chave ao ssh-agent, digite `ssh-add ~/path/to/my_key`.

**Dica:** caso você deseje que o `ssh-agent` esqueça sua chave depois de algum tempo, configure-o para fazer isso executando `ssh-add -t <seconds>`.

Agora, quando você executar o Git Bash pela primeira vez, sua frase secreta será solicitada:

```shell
> Initializing new SSH agent...
> succeeded
> Enter passphrase for /c/Users/YOU/.ssh/id_rsa:
> Identity added: /c/Users/YOU/.ssh/id_rsa (/c/Users/YOU/.ssh/id_rsa)
> Welcome to Git (version 1.6.0.2-preview20080923)
>
> Run 'git help git' to display the help index.
> Run 'git help ' to display help for specific commands.
```

O processo `ssh-agent` continuará sendo executado até que você faça logoff, desligue o computador ou encerre o processo.

### Adicionar ou alterar frase secreta

É possível alterar a frase secreta de uma chave privada sem gerar novamente o par de chaves. Basta digitar o seguinte comando:

```shell
$ ssh-keygen -p -f ~/.ssh/id_ed25519
> Enter old passphrase: [Type old passphrase]
> Key has comment 'your_email@example.com'
> Enter new passphrase (empty for no passphrase): [Type new passphrase]
> Enter same passphrase again: [Repeat the new passphrase]
> Your identification has been saved with the new passphrase.
```

Caso a sua chave já tenha uma frase secreta, você precisará digitá-la antes de poder alterar para uma nova frase secreta.

## Adicionar uma nova chave SSH à sua conta do GitHub

Para configurar sua conta no GitHub.com para usar sua chave SSH nova (ou existente), você também precisará adicionar a chave à conta.

Copie a chave pública SSH para a sua área de transferência.

Se o seu arquivo de chave pública SSH tiver um nome diferente do código de exemplo, modifique o nome do arquivo para corresponder à sua configuração atual. Ao copiar sua chave, não adicione novas linhas nem espaços em branco.

```shell
$ clip < ~/.ssh/id_ed25519.pub
  # Copies the contents of the id_ed25519.pub file to your clipboard
```

**Dica:** com o ao WSL (Subsistema do Windows para Linux), você pode usar o `clip.exe`. Caso contrário, se `clip` não estiver funcionando, localize a pasta oculta `.ssh`, abra o arquivo no seu editor de texto favorito e copie-o para a área de transferência.

1. No canto superior direito de qualquer página, clique na foto do seu perfil e em **Configurações**.

2.  Na seção "Acesso" da barra lateral, clique em  **Chaves SSH e GPG**.
    
3.  Clique em **Nova chave SSH** ou **Adicionar chave SSH**.
    
4.  No campo "Title" (Título), adicione uma etiqueta descritiva para a nova chave. Por exemplo, se estiver usando um laptop pessoal, você poderá chamar essa chave de "Laptop pessoal".
    
5.  Selecione o tipo de chave: autenticação ou assinatura.

6.  No campo "Chave", cole sua chave pública.
    
7.  Clique em **Adicionar chave SSH**.
    
8.  Se solicitado, confirme acesso à sua conta em GitHub.


# Salvando projeto no github

```shell
git init \\Inicia o projeto no repositório
git add . \\Adiciona todos os projetos ao git
git commit -m "Mensagem explicativa" \\commitar alterações
git branch -M main \\Renomear a branch
git remote add origin git@github.com:seuUsuario/seuRepositorio.git \\Vincular o projeto local ao repositório. É necessário criar o repositório previamente
git push -u origin main \\Enviar alterações para o repositório. A opção -u é para registrar o endereço e deve ser executada somente na primeira vez.
```

# Salvando posteriores alterações

```shell
git status
git add .
git commit -m "Mensagem explicativa"
git push
```

# Clonar e modificar um projeto de um repositório

```shell
git clone git@github.com:seuUsuario/seuRepositorio.git
git add .
git commit -m "Mensagem explicativa"
git push
```

# Verificando o histórico de versões

```shell
git log
```

Listagem resumida:

```shell
git log --oneline
```

Retorna para um estado anterior, removendo alterações no stage.

```shell
git reset
```


# Git chekout

## código do commit

Cada commit possui um código, que pode ser utilizado para referenciar o commit:

![[Pasted image 20230522214144.png]]

## HEAD

O último commit do histórico do branch corrente. O commit em que você se encontra atualmente.

![[Pasted image 20230522214503.png]]

## Git checkout

Pode ser utilizado para acessar um commit especifico:

```shell
git checkout c63b9d85e731369894c7905b26e9b6b932c38483
```

Para trocar para o último commit de uma branch:

```shell
git checkout main
```

É possível referenciar um commit N versões antes de HEAD usando ~N, por exemplo:

```shell
git checkout HEAD~1
```

**Importante:** antes de fazer o checkout para voltar para HEAD, certifique-se de que não haja mudanças nos arquivos. Se você acidentalmente mudou alguma coisa, desfaça as modificações usando:

```shell
git reset \\ remove alterações do stage
git clean -df
git chekout -- .
```


# Arquivo .gitignore

É um arquivo que indica o que não deve ser salvo pelo git.

Geralmente o arquivo .gitignore fica salvo na pasta principal do repositório. Mas também é possível salvar outros arquivos .gitignore em subpastas do repositório, para indicar o que deve ser ignorado por cada subpasta.

Casos comuns de arquivos que não devem ser salvos pelo Git:

* Arquivos compilados
	Linguagens compiladas (C, C++, Java, C#, etc.) geram arquivos de código compilado para executar o programa localmente.

* Arquivos de bibliotecas externas usadas no projeto
	Projetos reais utilizam bibliotecas externas (programas prontos disponíveis na Internet). Por exemplo, projetos JavaScript com NPM tipicamente salvam uma subpasta "node_modules" na pasta do seu projeto.

* Arquivos de configuração da sua IDE
	IDE's podem salvar uma subpasta com arquivos de configuração na pasta do projeto (exemplo: .vscode).

* Arquivos de configuração do seu sistema
Por exemplo, sistemas Mac podem gravar uma subpasta .ds_store na pasta do projeto.

## Criando uma branch

```shell
git branch <nome da branch>

git checkout <nome da branch> //Para trocar para a branch recem criada
```

# Correções no git

## Removendo um arquivo da área de stage

```shell
git reset
```

## Desfazendo modificações não salvas

Comandos para desfazer tudo que foi feito e retornar para o estado do último commit.

```shell
git status
git reset //retirar modificações da área stage
git clean -df
git checkout -- .
```

## Como desfazer o último commit

É possível remover o último commit realizado sem desfazer as alterações no código.

```shell
git reset --soft HEAD~1
```

## Deletar commits e remover modificações nos arquivos

Voltar o projeto ao estado de um dado commit (deletar commits e alterações posteriores a este commit).

```shell
git status
git reset --hard <codigo do commit>
```

Voltar o projeto ao estado do penúltimo commit:
```shell
git status
git reset --hard HEAD~1
```

**Atenção:** Ação destrutiva

## Atualizar o repositório local em relação ao remoto

```shell
git pull <nome do remoto> <nome da branch>

//Exemplo
git pull origin main
```


## Como resolver o push rejeitado

Não é permitido enviar um push se seu repositório local está atrasado em relação ao histórico do repositório remoto! Por exemplo:

![[Pasted image 20230530170914.png]]

Você tem que atualizar o repositório local:

```shell
git pull
```

Ao atualizar o repositório os commits tanto local como remoto são colocados em ordem cronológica e um commit de Merge pode ter que ser feito.

![[Pasted image 20230603145314.png]]

## Resolvendo conflitos

Ocorre quando você realiza o merge de dois históricos diferentes e ambos mexeram no mesmo arquivo. Quando isso ocorre você deve verificar qual a modificação válida deve prevalecer no código. do primeiro histórico ou do segundo.

Quando isso ocorre o processo deve ser:
1. Enviar atualização para o repositório, sendo que no repositório existe um histórico diferente do seu em que o mesmo arquivo em que você mexeu outra pessoa mexeu.
```shell
git push
```

Como existe histórico diferente o git informa que não é possível realizar o push sem antes atualizar o repositório local.

2. Então atualizamos o repositório local.

```shell
git pull
```

3. Com a atualização do repositório local surgiu um conflito. A branch fica com o nome **Merging** e os conflitos são listados no código. Você terá que definir o que manter e o que remover. Para este processo ser bem sucedido é necessário saber o que o código faz e o que as novas adições irão fazer.

4. Após resolver os conflitos realize o commit.

```shell
git commit -m "Conflitos resolvidos"
```

5. Depois prossiga com o push.

```shell
git push origin <nome da branch>
```

Os commits realizados são colocados em ordem cronológica e um commit de merge é criado pois ambos tinha histórico diferente.

![[Pasted image 20230603145314.png]]

## Sobrescrever um histórico no github

```shell
git push -f <nome do remoto>
```

Ao realizar a ação acima somente o histórico no servidor remoto será apagado e substituído pelo histórico local.

**Atenção:** é uma ação destrutiva

## visualizar repositórios remotos apontados pelo projeto

```shell
git remote -v
```

## Apontar o projeto para outro repositório remoto

Ou seja, transferir o histórico do projeto para outro repositório.

```shell
git remote set-url origin <novo repositório do git>
```

