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