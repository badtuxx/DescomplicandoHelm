# Day-01

## Pre-Req

- Ter um cluster Kubernetes já configurado.
- Ter o conhecimento básico em Kubernetes e Linux

## O que é o Helm

O Helm é um gerenciador de pacotes criado para facilitar a instalação de aplicações e suas dependências no Kubernetes. 
Podemos comparar o Helm com o APT do Debian, pois com apenas um comando você consegue instalar aplicações e suas dependencias no Kubernetes e ainda, fazer o gerenciamento de suas versões, podendo fazer o upgrade ou downgrade sem maiores problemas e rapidamente.
O Helm não é somente utilizado para fazer a instalação de aplicativos de terceiros, você consegue criar `charts`, que são os pacotes que o Helm utiliza para a instalação e configuração do aplicativo no Kubernetes.
O chart é composto por arquivos que definem como e qual deve ser o comportamento da aplicação dentro do cluster. É no chart que você define o seu deployment, o service, ingress e qualquer outra coisa necessária para a instalação e configuração da app desejada, e para isso, utilizamos os templates, que serão abordados mais para frente.
Acho que já sabemos o que é o Helm, evidente, iremos entende-lo melhor e sua aplicação dentro da nossa realidade conforme avançamos no treinamento.

## Instalação

O Helm é um bínario Go, e sua instalação é bastante simples.
Hoje o Helm é suportado por diversos sistemas operacionais, como Linux, MacOS, BSD e Windows.

Para realizar a instalação do nosso Helm, faça:

### Linux
```bash
curl https://baltocdn.com/helm/signing.asc | sudo apt-key add -
sudo apt-get install apt-transport-https --yes
echo "deb https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```

### Windows
```bash
```

### MacOS
```bash
```

### Primeiros passos com o Helm


Vamos verificar a versão do Helm que instalamos:

```bash
helm version

version.BuildInfo{Version:"v3.5.3", GitCommit:"041ce5a2c17a58be0fcd5f5e16fb3e7e95fea622", GitTreeState:"dirty", GoVersion:"go1.15.8"}
```

Acessar o help e assim, todos os parametros que podemos utilizar com o Helm:

```bash
helm help
```

Adicionando um repositório de charts:

```bash
helm repo add stable https://charts.helm.sh/stable
```

Após adicionar um repo, você precisa executar um update para pegar as informações mais recentes do repositório:

```bash
helm repo update
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "ingress-nginx" chart repository
...Successfully got an update from the "jetstack" chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈Happy Helming!⎈
```

Para pesquisar os charts disponíveis em seu repositório `stable`:

```bash
helm search repo stable
```

Para procurar por determinado chart no https://artifacthub.io/

```bash
helm search hub nginx
```


Visualizar os detalhes de determinado chart:

```bash
helm show chart stable/mysql
```

Deploy do nosso primeiro chart:

```bash
helm install stable/mysql --generate-name
WARNING: This chart is deprecated
NAME: mysql-1617481683
LAST DEPLOYED: Sat Apr  3 22:28:06 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
MySQL can be accessed via port 3306 on the following DNS name from within your cluster:
mysql-1617481683.default.svc.cluster.local

To get your root password run:

    MYSQL_ROOT_PASSWORD=$(kubectl get secret --namespace default mysql-1617481683 -o jsonpath="{.data.mysql-root-password}" | base64 --decode; echo)

To connect to your database:

1. Run an Ubuntu pod that you can use as a client:

    kubectl run -i --tty ubuntu --image=ubuntu:16.04 --restart=Never -- bash -il

2. Install the mysql client:

    $ apt-get update && apt-get install mysql-client -y

3. Connect using the mysql cli, then provide your password:
    $ mysql -h mysql-1617481683 -p

To connect to your database directly from outside the K8s cluster:
    MYSQL_HOST=127.0.0.1
    MYSQL_PORT=3306

    # Execute the following command to route the connection:
    kubectl port-forward svc/mysql-1617481683 3306

    mysql -h ${MYSQL_HOST} -P${MYSQL_PORT} -u root -p${MYSQL_ROOT_PASSWORD}
```

Removendo o chart instalando anteriormente:

```bash
helm uninstall mysql-1617481683
```

Instalando um novo exemplo de chart:

```bash
helm install stable/minecraft --generate-name
```


Listando os charts instalados:

```bash
helm ls
NAME                	NAMESPACE	REVISION	UPDATED                                 	STATUS  	CHART          	APP VERSION
minecraft-1617482357	default  	2       	2021-04-03 22:39:47.146903918 +0200 CEST	deployed	minecraft-1.2.5	1.14.4     
```

Removendo o chart:

```bash
helm uninstall minecraft-1617482357
``` 

Criando a nossa estrutura de arquivos e diretórios para o nosso chart:

```bash
helm create giropops
```

Instalando o comando tree para melhor visualização da estrutura de arquivos:

```bash
sudo apt-get install tree -y
```

Estrutura do chart:

```
└── giropops
    ├── charts
    ├── Chart.yaml
    ├── templates
    │   ├── deployment.yaml
    │   ├── _helpers.tpl
    │   ├── hpa.yaml
    │   ├── ingress.yaml
    │   ├── NOTES.txt
    │   ├── serviceaccount.yaml
    │   ├── service.yaml
    │   └── tests
    │       └── test-connection.yaml
    └── values.yaml
```

Realizando o deploy do nosso primeiro chart:

```bash
helm install giropops giropops/ --values giropops/values.yaml 
```

Listando os charts:

```bash
helm list
```

Realizando o upgrade do nosso chart:

```bash
helm upgrade giropops giropops/ --values giropops/values.yaml
```

Realizando o rollback para a `revision 1`:

```bash
helm rollback giropops 1
```

Visualizando o histórico de ações de determinado chart deployado:

```bash
helm history giropops
```