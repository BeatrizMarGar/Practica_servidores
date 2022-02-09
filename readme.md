# Práctica servidores

versión 1.0 2022.02.06@22:37

## Requisitos previos

### Instalar docker

Para instalar bien docker hay que venir al sur (pon aquí tu texto, wapa), ejemplo válido para Ubuntu y Debian

```bash
sudo apt-get update && sudo apt-get upgrade && sudo apt-get install -y docker
```
### Instalar docker-compose

Para instalar bien docker hay que venir al sur (pon aquí tu texto, wapa)

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

## Los Fichero

### El dockerfile

```Dockerfile
### Standar dockerfile image setup (starting from an existing docker image)
# Starting from existing node docker container image
FROM node:16
ARG SECRET_JWT
ARG MONGDB_URL

### Start of application setup
# Change working directory to thef inal destination
WORKDIR /etc/beamg

### Start of package and pre-requisites install and setup
# Installing git onto THIS docker image
RUN apt update && apt install -y git

### Start of scpecific application download and configuration
# Clone the conf files into the docker container
RUN git clone https://github.com/BeatrizMarGar/07_NODE_AVANZADO.git

# Change working directory to app dir
WORKDIR /etc/beamg/07_NODE_AVANZADO/

# add .env
RUN echo "SECRET_JWT=${SECRET_JWT}" > ./.env
RUN echo "MONGDB_URL=${MONGDB_URL}" >> ./.env
RUN cat ./.env

# starting application setup
RUN npm install
#RUN mkdir /data
#RUN mkdir /data/db
# RUN mongod & node install_db.js 
#RUN mongod & node init-db.js

EXPOSE 3000
CMD [ "npm", "start" ]
```

### docker-compose.yml
```docker-compose
version: '3'
services:
  practica07:
    build:
      context: .
      dockerfile: dockerfile
      args:
        SECRET_JWT: "558746988"
        MONGDB_URL: "mongodb://10.1.1.3/nodepop"
    image: beamg/practica07
    container_name: practica07
    restart: unless-stopped
    ports:
      - 3000:3000
    depends_on:
      - mongo

  mongo:
    image: mongo
    container_name: practica07_mongodb
    restart: always
    ports:
      - 27017:27017
```

## Pasos:

1. Instalar docker
2. Instalad docker-compose
3. Descargar dockerfile + docker-compose.yml
4. ejecutar (desde el directorio donde esté dockerfile + docker-compose.yml)
```bash
docker-compose up -d
```
5. Inicializar base de datos:
```bash
# inicializar la base de datos
docker run --rm beamg/practica07 node init-db.js
``` 
6. Ejecución opcional:
```bash
# Confirmar que .env está ok
docker run --rm beamg/practica07 cat .env
``` 