#git 


# O commit perfeito

Todo commit deve englobar somente um único tópico. Não deveríamos simplesmente englobar todas as alterações em um único commit.

![[Pasted image 20230602090130.png]]

O correto seria separar arquivos por tópicos específico para facilitar a legibilidade do código.

![[Pasted image 20230602090352.png]]

Para ajudar nisso é possível usar a stage área para incluir somente os arquivos a serem commitados e depois incluir os demais em futuros commits. O commit só salva os arquivos que estão na stage área.

Também é possível realizar commit somente de partes do código, adicionando essas partes a stage àrea. Para isso se faz uso do comando abaixo:

```bash
git add -p <nome do arquivo>
```

Ao realizar o comando acima será perguntado se o usuário quais partes ele deseja adicionar, para responder basta digitar y (sim) ou n (não).

# A mensagem de commit perfeita

O commit deve tem dois campos: **subject** (assunto) e **body** (corpo).

- **subject** - Resumo conciso do que aconteceu. Se tiver dificuldades em resumir o que está sendo feito é sinal que o commit está incluindo vários tópicos. Um commit deve abranger somente um tópico e envolver somente as alterações pertinentes a este tópico.

- **body** - Explicação mais detalhada. O que mudou? Qual a razão da mudança? Algo a ser destacado sobre a alteração?

Para estruturar a mensagem de commit acima basta digitar **git commit**, e um editor de texto será aberto para compor a mensagem. No editor você pode adicionar na primeira linha o subject e ao pular uma linha e deixa-la em branco na linha de baixo você pode compor o body.


# Estratégias para criação de branchs

O primeiro ato deve ser a criação de um documento de comum acordo com a equipe em que define como será o fluxo de trabalho. A definição do fluxo depende do tamanho do time, do projeto e de como é feito a criação de releases do projeto.

Há dois tipos de Branch:

- **Long-running Branchs** - Existe durante todo o ciclo de vida do projeto.

![[Pasted image 20230602160909.png]]

Na imagem acima existem duas branchs de longa duração (long-running), **develop** e **main**. Normalmente na Industria a branch main representa o código em produção e a develop é uma branch de integração. Se o seu ambiente de trabalho possui diferentes ambientes de desenvolvimento é uma boa prática criar branchs que obedeçam a mesma estrutura do seu ambiente de trabalho,
Normalmente commits não são adicionados a essa branch diretamente, por questões de qualidade e segurança do que está sendo enviado para os demais ambientes.

- **Shot-lived branchs** - Criadas para a adição de novas features ao projeto, correção de bugs, experimentos e refatorações. Normalmente são deletadas depois de integradas (merge/rebase).

![[Pasted image 20230602161934.png]]


Exemplos de estratégias de branch:

- **GitHub Flow**
 Possui apenas uma branch de longa duração. Correções de bugs, features e demais alterações são realizadas em branchs separadas.

![[Pasted image 20230602162807.png]]

- **Git Flow**
Possui mais branchs de longa duração. Uma branch representa o código em produção (main) e outra realiza o papel de branch de integração (develop).

![[Pasted image 20230602163305.png]]


# Pull Request

O pull request pode variar dependendo da plataforma em que estiver trabalhando, mas o principio é o mesmo, O objetivo é somente realizar merge de um código na branch principal somente após o ok de um revisor.

![[Pasted image 20230602164008.png]]

O pull request também é utilizando quando você deseja adicionar alguma alteração no código de um repositório publico o qual não possui acesso. Lembrando que isso é diferente do fork.

# Fork

O fork é uma cópia de um repositório publico para o seu repositório, o qual o permite alterar e modificar o código depois enviar estas alterações como pull request.

![[Pasted image 20230602172307.png]]

Ao clonar o repositório fork e inserir alterações e envia-las as mesmas para o repositório a plataforma pode perguntar se deseja criar uma pull request.

# Conflitos no merge

O merge pode ser abortado com os comandos abaixo, o primeiro aborta um merge e o segundo aborta o rebase, que são os dois comandos que pode gerar conflitos.

```shell

git merge --abort

git rebase --abort
```

Os conflitos são marcados no arquivo pelo simbolo <<< e pelas palavras HEAD, indicando o nosso commit local, e === para indicar o fim do código da branch local e inicio do código da branch remota.

Para lidar com conflitos sem ter que modificar o arquivo manualmente você pode fazer uso de ferramentas para isso. Algumas IDEs fornecem ferramentas gráficas para isso.

# Git Merge

Merge é o processo de adicionar código novo de volta a branch original. No caso quando você cria um nova branch a partir da principal do seu projeto para adicionar código novo e depois decide adicionar o código novo criado na nova branch para a branch original, que é a principal.

No cenário abaixo o merge é simples de ser feito. Pois as branchs compartilham o mesmo histórico e a adição da **branch B** na **Branch A** é feita simplesmente adicionando os commits posteriormente criados na **Branch B**.

![[Pasted image 20230603144633.png]]

![[Pasted image 20230603144735.png]]


Mas um cenário mais realista  é o que as adições novas não são somente realizadas na branch nova, mas também na branch principal.

Neste cenário é criado um commit de merge.

![[Pasted image 20230603145240.png]]

![[Pasted image 20230603145314.png]]


# Git rebase

Semelhante ao git merge, mas ao contrário do primeiro o rebase mantém um histórico limpo 

O comando "git rebase" é uma das principais ferramentas do Git que permite reorganizar e modificar o histórico de commits de um ramo em relação a outro. Ele é especialmente útil quando você deseja integrar as alterações de um ramo para outro de forma mais limpa e organizada.

Vamos supor que você tenha um ramo de desenvolvimento chamado "feature" que foi criado a partir do ramo "master". Durante o desenvolvimento, outros commits foram adicionados ao ramo "master". Agora, você deseja incorporar essas alterações do "master" para o seu ramo "feature". É aqui que o comando "git rebase" pode ser útil.

Aqui está uma explicação passo a passo do uso básico do "git rebase":

Vamos supor que você tenha o seguinte histórico de commits no seu repositório, onde o ramo "feature" divergiu do ramo "master":

```shell
A---B---C  (master)
	 \
	  D---E---F  (feature)
```

Agora, você deseja incorporar as alterações do ramo "master" para o ramo "feature" usando o `git rebase`. Aqui está como ficaria o processo:

Passo 1: Verifique o ramo de destino

```shell
git checkout feature
```

Isso muda para o ramo "feature".

Passo 2: Execute o git rebase

```shell
git rebase master
```

Isso inicia o processo de rebase, onde o Git tentará aplicar os commits do ramo "feature" no topo do último commit do ramo "master".

Passo 3: Resolva conflitos, se necessário Se houver conflitos, o Git pausará o processo de rebase e indicará os arquivos com conflitos. Você precisará resolvê-los manualmente, editando os arquivos afetados. Depois de resolver cada conflito, você pode usar o comando `git add` para sinalizar que os conflitos foram resolvidos.

Passo 4: Continue o processo de rebase

```shell
git rebase --continue
```


Isso instrui o Git a continuar o processo de rebase após a resolução dos conflitos. Se houver mais conflitos, o processo de resolução e continuação é repetido até que todos os commits do ramo "feature" sejam aplicados no topo do ramo "master".

Após concluir o `git rebase`, o histórico de commits será reorganizado de forma a parecer que você desenvolveu sua funcionalidade no topo das alterações mais recentes do ramo "master". O resultado seria algo como:

```shell
A---B---C---D'---E'---F'  (feature)
				 |
			   (master)

```

Nesse exemplo, os commits D, E e F do ramo "feature" foram reaplicados no topo do ramo "master" e renomeados para D', E' e F'. O ramo "feature" agora está atualizado com as alterações do ramo "master".

Lembre-se de que o `git rebase` reescreve o histórico de commits, portanto, é importante usá-lo com cuidado, especialmente se você já compartilhou o ramo "feature" com outras pessoas ou fez push dele para um repositório remoto.

![[Pasted image 20230603153650.png]]

# Git fetch

O comando `git fetch` é usado para recuperar as atualizações mais recentes de um repositório remoto, mas sem aplicá-las diretamente ao seu branch local. Ele permite que você veja as alterações feitas em um repositório remoto antes de decidir incorporá-las ao seu trabalho local.

Vamos entender o `git fetch` usando um exemplo visual. Suponha que você tenha um repositório remoto chamado "origin" e um branch local chamado "master". Inicialmente, o seu repositório local e o repositório remoto estão sincronizados, com o seguinte histórico de commits:

```shell
Repositório Remoto (origin):
        A---B---C---D---E  (master)

Repositório Local:
        A---B---C---D---E  (master)
```

Nesse ponto, você decide executar o comando `git fetch` para verificar se há atualizações no repositório remoto. O comando seria assim:

```shell
git fetch origin
```

O Git busca as alterações mais recentes no repositório remoto "origin" e as traz para o seu repositório local. No entanto, as alterações não são aplicadas automaticamente ao seu branch local. O histórico de commits ficaria assim:

```shell
Repositório Remoto (origin):
        A---B---C---D---E  (master)

Repositório Local:
        A---B---C---D---E  (master)
                         ^
                         |
                   (origin/master)
```

Observe que o seu branch local "master" permanece o mesmo, mas um novo ponteiro chamado "origin/master" é criado para rastrear o último commit no branch "master" do repositório remoto.

Agora você pode visualizar as diferenças entre o seu branch local e o branch remoto usando comandos como `git diff` ou ferramentas de visualização. Isso permite que você veja quaisquer alterações feitas no repositório remoto desde a sua última sincronização.

Para incorporar as atualizações do repositório remoto ao seu branch local, você pode usar outros comandos, como `git merge` ou `git rebase`, dependendo da sua estratégia de integração.

Em resumo, o `git fetch` é usado para buscar as atualizações mais recentes de um repositório remoto, permitindo que você veja as alterações antes de aplicá-las ao seu branch local. Ele ajuda a manter seu repositório local atualizado e a evitar conflitos durante a integração das alterações.

# Listando branchs remotas

Para listar as branches remotas em um repositório Git, você pode usar o comando `git branch` com a opção `-r`. Veja como fazer:

```shell
git branch -r
```

Esse comando irá listar todas as branches remotas disponíveis no repositório. Cada branch será exibida no formato `origin/<nome da branch>`, onde "origin" é o nome do repositório remoto padrão (geralmente chamado de "origin") seguido pelo nome da branch remota.

Por exemplo, se você executar o comando `git branch -r` e tiver as seguintes branches remotas no seu repositório:

```shell
origin/master
origin/feature
origin/bugfix
```

Você verá a lista de branches remotas como resultado.

Além disso, você também pode adicionar a opção `--list` ou `-l` para obter uma lista mais detalhada das branches remotas, mostrando o commit de destino de cada uma delas:

```shell
git branch -r --list
```

Isso exibirá as branches remotas junto com os commits aos quais estão apontando.

Lembre-se de que o comando `git branch -r` lista apenas as branches remotas e não as branches locais. Para listar todas as branches (locais e remotas), você pode usar o comando `git branch -a`.

O comando `git cherry-pick` é usado para copiar um único commit de um ramo para outro. Ele permite que você selecione um commit específico e o aplique em outro ramo, independentemente da diferença entre eles.

Vamos entender o `git cherry-pick` usando um exemplo visual. Suponha que você tenha o seguinte histórico de commits em um repositório:

```shell
        A---B---C---D  (master)
             \
              E---F---G  (feature)
```

Nesse caso, você deseja aplicar o commit "F" do ramo "feature" no ramo "master". Você pode fazer isso usando o comando `git cherry-pick` da seguinte maneira:

```shell
git checkout master
git cherry-pick F
```

Isso criará uma cópia do commit "F" e a aplicará ao ramo atual, neste caso, o ramo "master". O histórico de commits ficará assim:

```shell
        A---B---C---D---F'  (master)
             \
              E---F---G  (feature)
```

Observe que o commit "F'" é uma nova cópia do commit "F" e é aplicado diretamente no ramo "master". Agora, o ramo "master" contém o mesmo commit que o ramo "feature".

É importante mencionar que o `git cherry-pick` não apenas copia o conteúdo do commit, mas também o autor e a mensagem do commit original. No entanto, o hash do commit será diferente, já que é uma cópia separada.

Além de especificar um único commit, você também pode usar o `git cherry-pick` para copiar vários commits em sequência, especificando um intervalo de commits. Por exemplo:

```shell
git cherry-pick C..G
```

Nesse caso, todos os commits desde "C" até "G" (inclusive) seriam aplicados ao ramo atual.

Lembre-se de que, ao usar o `git cherry-pick`, você está introduzindo um novo commit ao ramo de destino. Isso significa que, se você já compartilhou o ramo "master" com outras pessoas ou fez push dele para um repositório remoto, pode haver implicações ao adicionar novos commits dessa maneira.