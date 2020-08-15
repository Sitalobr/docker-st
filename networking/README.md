# Docker Networking
Utilizando docker para lidar com a conectividade entre containers e o mundo externo.

## Exemplo BD
Os comandos a seguir consistem em iniciar um container a partir da imagem "postgres" contendo as variáveis de ambiente necessárias e mapeando uma porta externa para acessar a porta da base de dados interna ao container.

### Iniciando container server para ser o host do banco de dados
```shell
> docker run --name pg_server -e POSTGRES_PASSWORD=123456 -e POSTGRES_USER=admin -p 3333:5432 -itd postgres
```

### Descobrindo o IP definido para o container pg_server
```shell
> docker inspect pg_server | grep IP
```

### Iniciando outro container a partir da imagem "governmentpaas/psql" para ser o nosso client
```shell
> docker run -d --name pg_client governmentpaas/psql
```

### Acessando o bash do container, conectando-se e criando um database no pg_server
```shell
> docker exec -it pg_client bash
> psql -h 172.17.0.2 -U admin --password
> CREATE DATABASE teste;
```

## Exemplo de conectividade entre containers
Os comandos a seguir consistem em exemplificar como containers podem se comunicar entre si.

### Método 'linking'
O método 'linking' consiste em conectar dois containers de forma origem-destino, não vice-versa.

#### Criando o primeiro container
```shell
> docker run -itd --name ubuntu1 ubuntu:18.04
```

#### Criando o segundo container fazendo 'linking' com o primeiro
Observe que o container de destino terá o alias cont1.

```shell
> docker run -itd --name ubuntu2 --link ubuntu1:cont1 ubuntu:18.04
```

#### Testando com sucesso a conectividade entre o container 1 e 2
```shell
> docker exec -it ubuntu2 bash
# ping cont1
# ping ubuntu1
# exit
```

#### Testando sem sucesso a conectividade entre o container 2 e 1
O comando 'ping' não tem resposta para o segundo container, pois 'link' é um meio de comunicação de origem e destino, não vice-versa.

```shell
> docker exec -it ubuntu1 bash
# ping ubuntu2
# exit
```

### Método 'bridge'
O método 'bridge' consiste em criar uma rede bridge própria e todos os containers inseridos nessa rede terão conectividade entre si. Outros containers fora dela não poderão ser alcançados, nem vice-versa.

#### Criando uma rede bridge própria
Ao criar esse rede você poderá colocar todos os containers que desejar manter conexões

```shell
> docker network create minharede
```

#### Visualizando e inspecionando a rede criada

```shell
> docker network ls
> docker network inspect minharede
```

#### Criando containers e inserindo na rede bridge criada

```shell
> docker run -itd --name debian1 --net minharede debian
> docker run -itd --name debian2 --net minharede debian
```

Desta forma, os containers 'debian1' e 'debian2' terão conectividade entre si.

#### Removendo a rede bridge
Lembrando que somente é possível removê-la caso nenhum container a esteja utilizando.

```shell
> docker stop debian1
> docker stop debian2
> docker rm debian1
> docker rm debian2
> docker network rm minharede
```
