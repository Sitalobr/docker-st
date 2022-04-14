# Desenvolvendo com Docker
Utilizando docker para desenvolver aplicações e disponibilizar no Docker Hub.

Este conteúdo foi traduzido a partir da documentação oficial contida na página do Docker.

[Ir para a documentação oficial](https://docs.docker.com/)

## Criando o arquivo dockerfile
Dockerfiles descrevem como configurar um sistema de arquivos privado para um determinado container, assim como podem conter metadados descrevendo como executar um container baseado nessa imagem.

### Exemplo de um dockerfile
```dockerfile
# Utiliza uma imagem pré-existente como a imagem pai.
FROM node:current-slim

# Define o diretório a ser utilizado para armazenamento dos arquivos daqui pra frente.
WORKDIR /usr/src/app

# Copia o arquivo package.json a partir do host para o diretório local definido acima.
COPY package.json .

# Executa o comando especificado no sistema de arquivos da imagem.
RUN npm install

# Adiciona metadados na imagem para descrever qual porta o container estará escutando no momento da execução.
EXPOSE 8080

# Executa o comando especificado dentro do container.
CMD [ "npm", "start" ]

# Copia o restante dos arquivos da sua aplicação do seu host para o sistema de arquivos da imagem.
COPY . .
```

### Buildando a imagem a partir do dockerfile
```shell
> docker build <path_to_file>
```

## Executando sua imagem como um container
```shell
> docker run --publish 8000:8080 --detach --name bb bulletinboard:1.0
```

Explicando os comandos:

* `--publish` solicita ao Docker para redirecionar o tráfego que chega pela porta 8000 do host para a porta 8080 do container;
* `--detach` solicita ao Docker para executar este container em background;
* `--name` especifica um nome o qual você pode se referir ao seu container em futuros comandos, neste caso "bb".

Ao visitar o endereço `localhost:8000`, será possível visualizar a sua aplicação sendo executada.

Caso queira parar a execução do container:
```shell
> docker stop bb
> docker rm bb
```

## Criando o arquivo docker-compose
Arquivos docker-compose descrevem como executar aplicações multi-container. Com ele, você usará um arquivo YAML para configurar os serviços de suas aplicações. Após isso, com um único comando, você cria e inicia todos os serviços a partir dessa configuração.

### Exemplo de um docker-compose
```yaml
version: '3'
services:
    redis:
        image: redis:alpine
        ports:
            - 6379:6379
    redis-insight:
        image: redislabs/redisinsight:latest
        ports:
            - 8001:8001
```

Neste arquivo, definimos dois serviços a serem executados como dois containers distintos. O primeiro chamado de `redis`, irá iniciar um container com uma instância do Redis a partir da imagem `redis:alpine` e mapeando a porta padrão 6379 do serviço para a porta de mesmo número no host. Enquanto o segundo serviço irá iniciar um outro container a partir da imagem `redislabs/redisinsight:latest` na porta 8001, subindo uma instância do gerenciador de cache do Redis.

### Executando o docker-compose
Para iniciar os serviços:
```shell
> docker-compose up -d
```

Para parar os serviços:
```shell
> docker-compose down
```
