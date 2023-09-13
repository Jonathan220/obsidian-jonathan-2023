
# Necessidades

- Variáveis de ambiente
- Gerenciamento de senhas / secrets
- Escolher os recursos computacionais que a minha aplicação roda
- Health check
- Load Balancing
- SSL / TLS
- Domínio (www)
- Estratégias de deploy
- Storage
- Service discovery e resolução de nome (dns)


Pod é uma unidade do kubernete que mantém o container rodando. A unidade promove tudo que o container precisa, o container pensa que está rodando dentro de uma máquina.

Para fazer uso do kubernetes na sua máquina você pode baixar o kind, que executa o kubernetes utilizando o docker.

# Instalando o kubernates

kind é um ferramenta para executar kubernetes localmente usando docker

Você pode instalar o kind com o comando:

```go
go install sigs.k8s.io/kind@v0.20.0
```

Este comando irá colocar kind em `$(go env GOPATH)/bin`.  Você pode precisar adicionar o diretório ao seu $PATH caso encontre o erro `kind: command not found`.

Para usar o kind você precisa do go e [[Docker]] instalado na máquina.

Para criar um cluster no kind utilize o comando:

```shell
kind create cluster
```

O comando acima irá fazer uso da imagem padrão. Para especificar uma outra imagem utilize o comando abaixo com a flag `--image`.

```shell
kind create cluster --image=...
```

Para deletar um cluster utilize o comando abaixo:

```shell
kind delete cluster
```


# Criando um manifesto

Um manifesto é um arquivo com o passo a passo para a criar um pod, como uma receita de bolo.

1. Crie um arquivo pod.yaml.
2.  O arquivo deve conter os comandos abaixo:

```yml
apiVersion: v1       // versão da api que deseja criar
kind: Pod            // O tipo de objeto que deseja criar no kubernete
metadata: 
	name: nginx      // Nome do kubernete
	labels:
		name: nginx  // para caso deseje filtrar o kubernete
Spec:                // As especificações dos containers que irão rodar dentro do pod
 containers:
 - name: nginx
   image: nginx:latest
   resources:        // Especifica o quanto de recursos deseja destinar ao container (opcional)
	   limits:
		   memory: "120Mi"
		   cpu: "500m"
	ports:
		- containerPort: 80
```

Para aplicar o manifesto que você criou ao kubernetes digite o comando abaixo:

```shell
kubectl apply -f pod.yaml
```

Para listar os pods criados:

```shell
kubectl get pods
```

Para eliminar o pod:

```shell
kubectl delete pod nginx 
```


## Replica set

Gerencia e monitora os pods que estão rodando. Se os pods não estiverem no ar o replica set recria os pods.
No replica set você escolhe o número de pods que quiser rodar.

Para criar um replica set você precisa criar um manifesto.
O manifesto é parecido com o do pod.

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
	name: nginx // Nome do ReplicaSet
spec:
	selector:
		matchLabels: // Rótulo que será buscado pela replica para gerenciar os pods com esta label 
			app: nginx
		template:
			metadata: // Abaixo é colocado as informações do pod, o mesmo do manifesto acima. 
				labels:
					app: nginx  // para caso deseje filtrar o kubernete
		Spec:                // As especificações dos containers que irão rodar dentro do pod
			containers:
			 - name: nginx
			   image: nginx:latest
				ports:
				- containerPort: 80
```

Para criar o replica set execute o comando abaixo:

```shell
kubectl apply -f replicaset.yaml
```

Para listar os replicas set:

```shell
kubectl get rs
```

Ao executar o replica set ele irá levantar um pod. Este pod será gerenciado pelo replica, ou seja, se for derrubado o replica irá coloca-lo no lugar de novo.

Se quiser aumentar o número de pods, basta adicionar a propriedade `replicas` com o número de pods.

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
	name: nginx // Nome do ReplicaSet
spec:
	replicas: 10
	selector:
		matchLabels: // Rótulo que será buscado pela replica para gerenciar os pods com esta label 
			app: nginx
		template:
			metadata: // Abaixo é colocado as informações do pod, o mesmo do manifesto acima. 
				labels:
					app: nginx  // para caso deseje filtrar o kubernete
		Spec:                // As especificações dos containers que irão rodar dentro do pod
			containers:
			 - name: nginx
			   image: nginx:latest
				ports:
				- containerPort: 80
```

Ao criar novamente o Replica set você terá agora 10 pods com cada um rodando um container que está executando um nginx.

## Acessar um dos containers

Para acessar um dos container você precisa ter a porta do container apontando para uma porta do computador. Para fazer isso com um dos pods em execução:

```shell
kubectl port-forward pod/{nome do pod} 8080:80
```

No comando acima é realizado o mapeamento de uma das portas do computador, no caso a 8080 para apontar a porta 80 do container contido no pod informado. Assim ao acessar localhost:8080 será possível acessar a porta 80 do container.

## Deployment

O Deployment é uma camada acima do replica set. Ele cria um replica set de acordo com uma especificação, se a especificação for alterada ele esvazia o replica set anterior e cria novos pods para este replica set.

O levantamento do deployment funciona do mesmo modo que o Replica set, basta criar um arquivo deployment.yaml com a mesma estrutura que o Replica set.

Para listar os deployments:

```shell
kubectl get deployment
```

![[Pasted image 20230830193807.png]]


## Service

É o responsável por realizar o balanceamento de carga no acesso aos containers dentro dos pods.

![[Pasted image 20230830195809.png]]

Para criar um service é preciso criar o seu manifesto service.yaml.

```yaml
apiVersion: v1
kind: Service
metadata:
	name: nginx-service
spec:
	selector:
		app: nginx // Lebel do pod que deseja que o service procure
	ports:
	- port: 80 // Porta do service que você vai enviar requisição para o pod
	  targetPort: 80 // Porta do pod que deseja disponibilizar para o service
```

Para levantar o serviço execute:

```shell
kubectl apply -f service.yaml
```

Listar os serviços rodando:

```shell
kubectl get svc
```

Para acessar a porta do serviço em nossa máquina execute o port-forward:

```shell
kubectl port-forward svc/nginx-service 8080:80
```

Existem diversos tipos de Service, você pode determinar o seu service incluindo a opção type no manifesto.

```yaml
apiVersion: v1
kind: Service
metadata:
	name: nginx-service
spec:
	type: LoadBalancer
	selector:
		app: nginx // Lebel do pod que deseja que o service procure
	ports:
	- port: 80 // Porta do service que você vai enviar requisição para o pod
	  targetPort: 80 // Porta do pod que deseja disponibilizar para o service
```

