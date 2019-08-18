# Docker Networking
Utilizando docker para lidar com a conectividade entre containers e o mundo externo.

Os comandos a seguir consistem em iniciar um container a partir da imagem "postgres" contendo as variáveis de ambiente necessárias e mapeando uma porta externa para acessar a porta da base de dados interna ao container.

## Iniciando container server para ser o host do banco de dados
```shell
> docker run --name pg_server -e POSTGRES_PASSWORD=123456 -e POSTGRES_USER=admin -p 3333:5432 -itd postgres
```

## Descobrindo o IP definido para o container pg_server
```shell
> docker inspect pg_server | grep IP
```

## Iniciando outro container a partir da imagem "governmentpaas/psql" para ser o nosso client
```shell
> docker run -d --name pg_client governmentpaas/psql
```

## Acessando o bash do container, conectando-se e criando um database no pg_server
```shell
> docker exec -it pg_client bash
> psql -h 172.17.0.2 -U admin --password
> CREATE DATABASE teste;
```