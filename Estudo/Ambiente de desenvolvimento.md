
## WSL

1 - Vá até a loja da microsoft e baixe o window terminal.

2- Tente atualizar o terminal

```shell
wsl --update
```

3 - Instale o sistema linux no terminal.

```shell
wsl --install
```


Depois reinicie o computador e após o seu retorno a tela do ubuntu irá ser aberta automáticamente.

### Backup

Só pode ser executado no power shell ou prompt (DOS).

```shell
wsl --export Ubuntu C:\Users\jpereira\Documents\backup.tar
```

Para restaurar o backup

```shell
wsl --import Ubuntu C:\Users\jpereira\Documents\backupt.tar
```



### Instalação do zsh com oh-my-zsh

1. Instale o zsh

```shell
sudo apt install zsh
```

2. Instalação do oh-my-zsh

```shell
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

3. Para instalar plugins você deve clonar o repositório do plugin colocar sua pasta descompactada dentro da pasta /home/jabreu/.oh-my-zsh/custom/plugins.
	
	Após isso o arquivo .zshrc deve ser editado. Na linha que contém a lista de plugins adicione o nome do plugin.

	![[Pasted image 20230810212613.png]]

4. Para alterar o tema você deve clonar o tema e adiciona-lo na pasta /home/jabreu/.oh-my-zsh/custom/themes.

	Depois edite o arquivo .zshrc alterando a linha ZSH-THEME pelo nome do tema.


## VScode

### Font-family

Usar uma fonte adequada para desenvolvimento melhorará seu conforto ao programar e ler código, escolha uma fonte adequada.

Eu uso a [JetBrains Mono](https://www.jetbrains.com/lp/mono/) como fonte padrão.

Instale-a no seu computador e ativa-a no VSCode alterando a opção `editor.fontFamily`, exemplo:

```json
"editor.fontFamily": "\"JetBrains Mono\", 'Courier New', monospace",
```

Esta propriedade permite outras fontes como fallback se as primeiras não existirem.