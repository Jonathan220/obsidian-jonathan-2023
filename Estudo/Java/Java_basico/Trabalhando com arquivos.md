
## Lendo arquivo de texto com as classes File e Scanner

- **File** - Representação abstrata de um arquivo e seu caminho
- **Scanner** - Leitor de texto
- **IOException** - Exceção

```java
public static void main(String[] args){
	File file = new File("C:\\temp\\in.txt");
	Scanner sc = null;
	try{
		sc = new Scanner(file);
		while(sc.hasNextLine()){
			System.out.println(sc.nextLine);
		}
	}catch (IOException e){
		System.out.println("Error: " + e.getMessage());
	}finally{
		if(sc != null) {
			sc.close();
		}
	}
}
```


## FileReader e BufferedReader

- **FileReader** é stream (sequencia) de leitura de caracteres a partir de arquivos.
- **BufferedReader** é instanciado a partir do FileReader e por fazer uso de buffs de memória é mais rápido.

```java
public static void main(String[] args){
	String path = "c:\\temp\\in.txt";
	FileReader fr = null;
	BufferedReader br = null;
	
	try {
		fr = new FileReader(path);
		br = new BufferedReader(fr);

		String line = br.readLine();

		while(line != null){
			system.out.println(line);
			line = br.readLine();
		}
	}catch (IOException e){
		System.out.println("Error: " + e.getMessage());
	}
	finally{
		try {
			if(br != null){
				br.close();
			}
			if(fr != null){
				fr.close();
			}
		}catch (IOException e){
			e.printStackTrace();
		}
	}
}
```


## Bloco try-with-resources

Uma melhor maneira de trabalhar com streams de dados.

É um bloco try que declara um ou mais recursos, e garante que esses recursos serão fechados ao final do bloco.

```java
public static void main(String[] args){
	String path = "c:\\temp\\in.txt";
	
	try (BufferedReader br = new BufferedReader(new FileReader(path))){
		String line = br.readLine();

		while(line != null){
			system.out.println(line);
			line = br.readLine();
		}
	}catch (IOException e){
		System.out.println("Error: " + e.getMessage());
	}
}
```

O método acima realiza a leitura de arquivos através de stream de dados com menos código.


## FileWriter e BufferedWriter

- **FileWriter** stream de escrita de caracteres em arquivos. Pode ser instanciado de duas maneiras:
  - Criar/Recriar o arquivo

```java
new FileWriter(path);
```

 - Acrescentar dados a um arquivo existente

```java
new FileWriter(path, true);
```

As função podem ser usadas como abaixo:

```java
public static void main(String[] args){
	String[] lines = {"Good morning", "Good afternoon", "Good evening"}
	String path = "c:\\temp\\out.txt";
	
	try (BufferedWriter bw = new BufferedWriter(new FileWriter(path))){
		for(String line : lines){
			bw.write(line);
			bw.newLine()
		}
	}catch (IOException e){
		System.out.println("Error: " + e.getMessage());
	}
}
```


## Manipulando pastas com File

Para listar os diretórios e arquivos a partir de um endereço fornecido pelo usuário:

```java
public static void main(String[] args){
	Scanner sc = new Scanner(System.in);

	System.out.println("Enter a folder path: ");
	String strPath = sc.nextLine();

	File path = new File(strPath);

	File[] folders = path.listFiles(File::isDirectory);
	System.out.println("FOLDERS");
	for(File folder : folders){
		System.out.println(folder);
	}

	File[] files = path.listFiles(File::isFile);
	System.out.println("FILES");
	for(File file: files){
		System.out.println(file);
	}

	sc.close();
}
```


Para criar um subdiretório:

```java
public static void main(String[] args){
	Scanner sc = new Scanner(System.in);

	System.out.println("Enter a folder path: ");
	String strPath = sc.nextLine();

	boolean success = new File(strPath + "\\subdiretorio").mkdir();
	system.out.println("Directory created succesfully: "+success);

	sc.close();
}
```


## Informações do caminho do arquivo

O objeto File permite recuperar diversas informações do arquivo, como nome, caminho absoluto, entre outros.

```java
public static void main(String[] args){
	Scanner sc = new Scanner(System.in);

	System.out.println("Enter a file path: ");
	String strPath = sc.nextLine();

	File path = new File(strPath);

	System.out.println("getName: " + path.getName); //Pega somento o nome do arquivo
	System.out.println("getParent:" + path.getParent); //pega somente o caminho do arquivo, sem seu nome
	System.out.println("getPath: " + path.getPath); //Pega o caminho absoluto do arquivo
	
	sc.close();
}
```