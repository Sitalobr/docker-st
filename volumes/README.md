# Docker Volumes
Utilizando docker para lidar com os volumes de armazenamento.

Os comandos a seguir consistem em mostrar os benefícios de se manter dados persistidos externamente ao ciclo de vida dos containers. Aplicando em um cenário onde 3 containers acessam volume compartilhado e rotina de backup definida.

## Método Host Volume
Método consiste em mapear um diretório do host com um diretório do container, ou seja, tudo o que for salvo nesse caminho específico do container, será compartilhado também no host.

### Pré-condições
Supondo que existe um arquivo index.html no diretório mapeado do host contendo, por exemplo:
```html
<h1>Minha página web </h1>
```

### Mapeamento
```shell
> docker run -d --name web_pasta -v ~/docker/site:/usr/local/apache2/htdocs -p 9090:80 httpd
```

É possível acessar o navegador web no endereço http://localhost:9090 e visualizar o arquivo index.html mencionado.

## Método Docker Volume
Método consiste em criar um volume de dados no host, porém gerenciado pelo CLI do docker, e mapeá-lo a um diretório específico do container.

### Criando um volume
```shell
> docker volume create --name datastore
```

### Iniciando um container web utilizando o volume criado
```shell
> docker run -d --name containervolume -v datastore:/usr/local/apache2/htdocs -p 9091:80 httpd
```

Ao acessar http://localhost:9091 no host, é apresentada a página padrão do apache: "It works!", pois como não há nenhum conteúdo inical no nosso volume, o docker dá preferência ao conteúdo existente no diretório mapeado do container.

### Copiando informações para o volume criado
```shell
> docker cp ~/docker/site/index.html containervolume:/usr/local/apache2/htdocs
```

Agora, ao acessar http://localhost:9091 no host, é apresentada a página index.html com o conteúdo adicionado anteriormente.

É possível copiar todos os arquivos de um diretório (ex.: ~/docker/site) através do uso do "." (ponto) ao final da barra. Exemplo:

```shell
> docker cp ~/docker/site/. containervolume:/usr/local/apache2/htdocs
```

Caso seja iniciado um outro container web utilizando o mesmo volume, será mantido todos os arquivos inseridos no volume anteriormente, uma vez que o ciclo de vida do volume é independente do ciclo de vida do container.

### Listagem e remoção de volumes
Lembrando que antes de remover um volume é necessário remover todos os containers que o utilizam.

```shell
> docker volume ls
> docker stop containervolume
> docker rm containervolume
> docker volume rm datastore
```

### Bônus
Caso seja necessário realizar backup das informações contidas em um volume específico, pode-se iniciar um outro container que contém funcionalidades de compressão (nesse exemplo foi utilizado o busybox) utilizando os dois métodos (host e volume):

```shell
> docker run -it -v datastore:/usr/site -v ~/docker-study/backup:/usr/backup --rm busybox tar cvf /usr/backup/arquivobkp.tar /usr/site
```

Repare a utilização do comando `--rm`, indicando que após executar o comando de compressão, o container será removido.