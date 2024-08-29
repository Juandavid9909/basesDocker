# ¿Qué es Docker?

Antes cuando llevaba un nuevo dev le decíamos "oye, instala estas aplicaciones en estas versiones en específico". Las versiones, nuestros sistemas operativos e incluso tener 2 proyectos (cada uno con distintas versiones de aplicaciones que se usan) generaría muchos inconvenientes.

Antes para solucionar esto se usaban las máquinas virtuales, pero esto consume muchos recursos de nuestro computador. Con Docker podemos manejar imagenes (una fotografía de la versión de las tecnologías que usamos) ya que ahora puedo descargarlas y montarlas en un contenedor, no importa el sistema operativo, Docker se encarga de ejecutarlos de forma adecuada.

## Beneficios

- Cada contenedor está aislado de los demás.
- Es posible ejecutar varias instancias de la misma versión o diferentes versiones sin configuraciones adicionales.
- Con un comando, puedes descargar, levantar y correr todo lo que necesitas.
- Cada contenedor contiene todo lo que necesita para ejecutarse.
- Indiferente el sistema operativo HOST.

Si se desea descargar una imagen para **Docker** podemos buscarlas [aquí](https://hub.docker.com/).


# Imágenes

| Descripción | Comando | Ejemplo |
|--|--|--|
| Descargar una imagen | `docker pull IMAGE_NAME` | `docker pull postgres` |
| Descargar una versión específica de una imagen | `docker pull IMAGE_NAME:Versión` | `docker pull postgres:15.1` |
| Detener uno o varios contenedores | `docker container rm -f <container-id1 container-id2>` | `docker container rm -f 1dr fgv` |
| Listar las imágenes | `docker images` | `docker images` |
| Eliminar una imagen específica | `docker image rm <image-ID> o <ID1 ID2>` | `docker image rm getting-started` |
| Eliminar imágenes colgadas | `docker image prune` | `docker image prune` |
| Borrar todas las imágenes no usadas | `docker image prune -a` | `docker image prune -a` |


# Contenedores

| Descripción | Comando | Ejemplo |
|--|--|--|
| Listar los contenedores que están corriendo | `docker container ls` | `docker container ls` |
| Listar todos los contenedores | `docker container ls -a` | `docker container ls -a` |
| Correr un contenedor | `docker container run IMAGE_NAME` | `docker container run -d -p 80:80 docker/getting-started` siendo `-d` que corra la imagen desenlazada de la consola donde se ejecutó el comando y `-p 80:80` mapea el puerto 80 de nuestra computadora con el puerto 80 del contenedor |
| Detener un contenedor | `docker container stop <ID>` | `docker container stop 1fa` |
| Iniciar un contenedor ya existente | `docker container start <ID>` | `docker container start 1fa` |
| Eliminar todos los contenedores que no se están usando | `docker container prune` | `docker container prune` |
| Eliminar un contenedor | `docker container rm -f <ID>` | `docker container rm 1fa` |


## Logs

Se pueden seguir los logs en los contenedores desde **Docker Desktop** o desde nuestra terminal de comandos como se muestra a continuación:

| Descripción | Comando | Ejemplo |
|--|--|--|
| Ver logs en un contenedor | `docker container logs <ID>` | `docker container logs 58f` |


# Variables de entorno

Podemos utilizar variables con los tags cuando ejecutamos un comando en **Docker** donde podremos setear un valor específico que necesitamos y sabemos que usaremos varias veces. Estas también nos permiten renombrar el contenedor que estamos ejecutando, por ejemplo si tenemos una imagen de `postgres` y colocamos el comando `docker container run --name some-postgres -d postgres` esto le asignará el nombre `some-postgres` a nuestro contenedor. Las variables de entorno de las imágenes las podemos setear con la bandera `-e` como se puede ver a continuación:

```bash
// Windows
docker container run ^
	--name postgres-alpha ^
	-e POSTGRES_PASSWORD=mypass1 ^
	-dp 5432:5432 ^
	postgres

// Linux y Mac
docker container run \
	-dp 3306:3306 \
	--name world-db \
	--env MARIADB_USER=example-user \
	--env MARIADB_PASSWORD=user-password \
	--env MARIADB_ROOT_PASSWORD=root-secret-password \
	--env MARIADB_DATABASE=world-db \
	mariadb:jammy
```


# Volúmenes

Nos permiten guardar los datos de nuestras imágenes, ya que sin ellos los contenedores son volátiles, es decir que si borramos un contenedor los datos se pierden si no existe un volumen asociado a él.


## Tipos de volúmenes

- **Anonymous Volumes:** Docker le asigna un nombre único al volúmen para nuestro uso.

- **Named Volumes:** Nosotros asignamos el nombre al volúmen.

- **Bind Volumes:** Trabaja con paths absolutos, sirven cuando queremos vincular un file system de nuestro equipo con un file system de nuestro contenedor.

| Descripción | Comando | Ejemplo |
|--|--|--|
| Crear un volumen | `docker volume create <NOMBRE>` | `docker volume create world-db` |
| Listar volúmenes | `docker volume ls` | `docker volume ls` |
| Inspeccionar un volumen | `docker volume inspect <NAME>` | `docker volume inspect world-db` |

Si se desea asociar un contenedor a un volumen se puede hacer de la siguiente manera teniendo en cuenta que el volumen debe estar creado desde antes:

```bash
docker container run \
	-dp 3306:3306 \
	--name world-db \
	--env MARIADB_USER=example-user \
	--env MARIADB_PASSWORD=user-password \
	--env MARIADB_ROOT_PASSWORD=root-secret-password \
	--env MARIADB_DATABASE=world-db \
	--volume world-db:/var/lib/mysql:Z \
	mariadb:jammy

docker container run ^
	--name phpmyadmin ^
	-d ^
	-e PMA_ARBITRARY=1 ^
	-p 8080:80 ^
	phpmyadmin:5.2.0-apache
```


# Container Networking

Si 2 o más contenedores están en la misma red pueden comunicarse entre sí. Si no lo están no sabrán de su existencia, esto es importante configurarlo si un contenedor depende de otro.

| Descripción | Comando | Ejemplo |
|--|--|--|
| Crear una red | `docker network create <NOMBRE>` | `docker network create world-app` |
| Listar redes | `docker network ls` | `docker network ls` |
| Inspeccionar un volumen | `docker volume inspect <NAME>` | `docker volume inspect world-db` |
| Eliminar una red | `docker network rm -f <ID>` | `docker network rm 1fa` |
| Eliminar todas las redes que no se estén utilizando | `docker network prune` | `docker network prune` |
| Conector un contenedor a una red | `docker network connect <NOMBRE-RED o ID-RED> <ID-CONTENEDOR>` | `docker network connect world-app j56` |
| Inspeccionar una red | `docker network inspect <NAME>` | `docker network inspect world-app` |

Si se desea asignar la red desde la creación del contenedor podemos ejecutar:

```
docker container run \
	-dp 3306:3306 \
	--name world-db \
	--env MARIADB_USER=example-user \
	--env MARIADB_PASSWORD=user-password \
	--env MARIADB_ROOT_PASSWORD=root-secret-password \
	--env MARIADB_DATABASE=world-db \
	--volume world-db:/var/lib/mysql:Z \
	--network world-app \
	mariadb:jammy

docker container run ^
	--name phpmyadmin ^
	-d ^
	-e PMA_ARBITRARY=1 ^
	-p 8080:80 ^
	--network world-app ^
	phpmyadmin:5.2.0-apache
```

Si necesitamos correr una app de NestJS con Docker podemos usar Bind Volumes como a continuación:

```bash
docker container run ^
	--name nest-app ^
	-w /app ^
	-p 80:3000 ^
	-v ${pwd}:/app ^
	node:16-alpine3.16 ^
	sh -c "yarn install && yarn start:dev"
```


# Terminal interactiva

Nos permite ejecutar la terminal de comandos de nuestro contenedor para ejecutar comandos definidos en la terminal del sistema operativo (Linux Alpine) de nuestro contenedor.

| Descripción | Comando | Ejemplo |
|--|--|--|
| Abrir terminal de comandos de nuestro contenedor | `docker exec -it <ID> /bin/sh` | `docker exec -it 4as /bin/sh` |
| Editar archivo | `vi <NOMBRE-ARCHIVO>` | `vi todo.service.ts` y `:wq!` para guardar |
| Salir de la terminal de comandos | `exit` | `exit` |


# Docker Compose

Es un archivo YAML que nos permite generar la estructura a base de comandos para montar todo lo que necesitemos para correr nuestras aplicaciones sin inconvenientes por su versionamiento, también se pueden crear las redes, volúmenes, y todo lo necesario que nos provee Docker.

Siempre es importante iniciar con la versión de nuestro Docker Compose, esto le dirá a Docker si estamos en la versión Legacy, la 2, o si estamos trabajando con la última versión. Con esta versión tendremos más o menos comandos disponibles.

Luego de configurar la versión tenemos que colocar los servicios que serán las imágenes de los contenedores que deseamos integrar. Aquí colocaremos el nombre del contenedor, su imagen, volumen, variables de entorno.

Para ejecutar nuestros comandos escritos en el archivo YAML podemos ejecutar la siguiente instrucción:

```bash
// Bajar todo lo de nuestro Docker Compose
docker compose down

// Ejecutar las instrucciones de nuestro archivo YAML
docker compose up -d
```


# Crear nuestras propias imágenes

Para Dockerizar una aplicación nuestra debemos crear un archivo cuyo nombre es **Dockerfile** y aquí podremos colocar todas las instrucciones para la construcción de nuestra imagen. Para esto se deben seguir los siguientes pasos:

- Indicar la imagen base con el comando `FROM`.
- Indicar el directorio sobre el que trabajaremos en el servidor que se crea con Docker con el comando `WORKDIR` (es similar a hacer un `cd` a la carpeta especificada).
- Copiar los archivos de nuestro proyecto al servidor usando el comando `COPY`.
- Ejecutar los comandos requeridos para correr nuestro proyecto con el comando `RUN`.
- Por último podremos usar el comando `CMD` para indicarle a Docker cómo correr nuestro proyecto una vez ya empiece a correr el contenedor.

```docker
FROM node:19.2-alpine3.16

# alpine tiene la carpeta /app por defecto creada en la imagen
WORKDIR /app

# Como asignamos /app como nuestro Working Directory, al utilizar ./ apuntaremos a dicho directorio
COPY app.js package.json ./

RUN npm install

# Comando run de la imagen
CMD ["node", "app.js"]
```

Ya por último para ejecutar el comando de construcción podemos colocar la siguiente instrucción en la terminal:

```bash
docker build --tag nuestro-tag .

# Ejemplo
docker build --tag cron-ticker .
```

Docker cachea las ejecuciones de nuestras instrucciones en nuestro Dockerfile. Sin embargo, si encuentra cambios este automáticamente ejecutará las instrucciones del paso donde detectó dichos cambios y los pasos posteriores, por lo que podemos ajustar nuestro Dockerfile para evitar que ejecute cosas que sabemos que no cambiarán (o al menos muy poco):

```docker
FROM node:19.2-alpine3.16

# alpine tiene la carpeta /app por defecto creada en la imagen
WORKDIR /app

# Como asignamos /app como nuestro Working Directory, al utilizar ./ apuntaremos a dicho directorio
COPY package.json ./

RUN npm install

COPY app.js ./

# Comando run de la imagen
CMD ["node", "app.js"]
```

Si queremos asignar una versión a nuestra imagen podemos hacerlo así:

```bash
docker build --tag nuestro-tag:version .

# Ejemplo
docker build --tag cron-ticker:1.0.0 .
```

Y si queremos renombrar una imagen lo podemos hacer así:

```bash
docker image tag nuestro-tag:version nuestro-nuevo-tag:nuestra-nueva-version

# Ejemplo
docker image tag cron-ticker:1.0.0 cron-ticker:bufalo
```


## Subir imagen a Docker Hub

Debemos acceder con nuestra cuenta a Docker Hub, crear el repositorio (pasos muy similares que GitHub) y Docker Hub nos entregará un comando para hacer el push de nuestra imagen, en este comando encontraremos el nuevo nombre de la imagen por lo que tendremos que renombrar nuestra imagen local, y luego de esto sí hacer push:

```bash
docker image tag cron-ticker:latest username/cron-ticker:latest

# Para autenticarnos (tendremos que indicar nuestro username y nuestra contraseña)
docker login

docker push username/cron-ticker
```

## Pruebas automáticas al código

Primero se configuran los tests en nuestra aplicación, por ejemplo podemos usar Jest en JavaScript y TypeScript. A continuación un ejemplo de un archivo Dockerfile para ejecutar los tests:

```docker
FROM node:19.2-alpine3.16

# alpine tiene la carpeta /app por defecto creada en la imagen
WORKDIR /app

# Como asignamos /app como nuestro Working Directory, al utilizar ./ apuntaremos a dicho directorio
COPY package.json ./

RUN npm install

# El . significa que copie todo
COPY . .

# Realizar testing
RUN npm run  test

# Comando run de la imagen
CMD ["node", "app.js"]
```

Y por último ejecutamos el build de nuestro código para convertirlo en una imagen de Docker:

```docker
docker build -t username/cron-ticker:version .
```


## Dockerignore

El archivo **.dockerignore** es como el archivo **.gitignore**, ya que nos permite ignorar los archivos y carpetas que queramos cuando ejecutamos un build.

```docker
node_modules/

Dockerfile
.dockerignore

.git
.gitignore
```


## Remover archivos y carpetas de la imagen

```docker
FROM node:19.2-alpine3.16

# alpine tiene la carpeta /app por defecto creada en la imagen
WORKDIR /app

# Como asignamos /app como nuestro Working Directory, al utilizar ./ apuntaremos a dicho directorio
COPY package.json ./

RUN npm install

# El . significa que copie todo
COPY . .

# Realizar testing
RUN npm run  test

# Eliminar archivos y directorio no necesarios
RUN rm -rf  tests && rm -rf  node_modules

# Únicamente las dependencias de prod
RUN npm install  --prod

# Comando run de la imagen
CMD ["node", "app.js"]
```


## Forzar una plataforma en la construcción

Si requerimos que utilice una plataforma de forma obligatoria (sistema operativo) podemos obligarlo en el comando `FROM ` de nuestro **Dockerfile**.

```docker
FROM --platform=linux/amd64 node:19.2-alpine3.16

# alpine tiene la carpeta /app por defecto creada en la imagen
WORKDIR /app

# Como asignamos /app como nuestro Working Directory, al utilizar ./ apuntaremos a dicho directorio
COPY package.json ./

RUN npm install

# El . significa que copie todo
COPY . .

# Realizar testing
RUN npm run  test

# Eliminar archivos y directorio no necesarios
RUN rm -rf  tests && rm -rf  node_modules

# Únicamente las dependencias de prod
RUN npm install  --prod

# Comando run de la imagen
CMD ["node", "app.js"]
```


## Buildx

Son todos los builders que tenemos para compilar nuestras imágenes y hacer que estas tengan una mayor compatibilidad.

| Descripción | Comando | Ejemplo |
|--|--|--|
| Listar los builders | `docker buildx ls` | `docker buildx ls` |
| Crear builders | `docker buildx create --name <NOMBRE> --driver <DRIVER> --bootstrap` | `docker buildx create --name mybuilder --driver docker-container --bootstrap` |
| Usar builders creados | `docker buildx use <NOMBRE>` | `docker buildx use mybuilder` |
| Hacer build multiplataforma | `docker buildx build --platform <ARQUITECTURAS> -t username/cron-ticker:version --push .` | `docker buildx build --platform linux/amd64,linux/arm64,linux/arm/v7,linux/arm64/v8 -t username/cron-ticker:version --push .` |
| Utilizar builder default | `docker buildx use default` | `docker buildx use default` |
| Eliminar builder | `docker buildx rm <NOMBRE` | `docker buildx rm mybuilder` |

Ya teniendo creado nuestro builder podemos ajustar la plataforma en la que queremos que corra nuestra imagen de forma dinámica colocando lo siguiente en nuestro **Dockerfile**:

```docker
FROM --platform=$BUILDPLATFORM node:19.2-alpine3.16

# alpine tiene la carpeta /app por defecto creada en la imagen
WORKDIR /app

# Como asignamos /app como nuestro Working Directory, al utilizar ./ apuntaremos a dicho directorio
COPY package.json ./

RUN npm install

# El . significa que copie todo
COPY . .

# Realizar testing
RUN npm run  test

# Eliminar archivos y directorio no necesarios
RUN rm -rf  tests && rm -rf  node_modules

# Únicamente las dependencias de prod
RUN npm install  --prod

# Comando run de la imagen
CMD ["node", "app.js"]
```

Y para hacer el build tendremos que usar un comando como el siguiente especificando las arquitecturas en las que queremos compilar nuestra imagen:

```bash
docker buildx use <NOMBRE-BUILDER>

docker buildx build --platform linux/amd64,linux/arm64,linux/arm/v7,linux/arm64/v8 -t username/cron-ticker:version --push .
```

También podemos hacer la creación para múltiples arquitecturas utilizando el mismo comando bash de `buildx` sin necesidad de usar la variable `$BUILDPLATFORM` ni especificar la plataforma en nuestro **Dockerfile**.


# Multi-Stage Build

Esto nos permite hacer la construcción de nuestra imagen en varios pasos, además de que podremos evitar realizar tareas así hayan cambios en algunos archivos. En pocas palabras es un **Dockerfile** con muchas etapas de construcción.

```docker
# Dependencias de desarrollo
FROM node:19.2-alpine3.16  AS deps

WORKDIR /app

COPY package.json ./

RUN npm install


# Build y Tests
FROM node:19.2-alpine3.16  AS builder

WORKDIR /app

COPY --from=deps /app/node_modules ./node_modules

COPY . .

RUN npm run  test
  

# Dependencias de producción
FROM node:19.2-alpine3.16  AS prod-deps

WORKDIR /app

COPY package.json ./

RUN npm install  --prod
  

# Ejecutar la app
FROM node:19.2-alpine3.16  AS runner

WORKDIR /app

COPY --from=prod-deps /app/node_modules ./node_modules

COPY app.js ./

COPY tasks/ ./tasks

RUN npm run  build

CMD ["node", "dist/app.js"]
```


## Docker Compose con Dockerfile

Podemos utilizar un archivo **Dockerfile** en nuestro **docker-compose.yaml** de la siguiente forma:

```yaml
version: '3'

services:
	app:
		build:
			context: .
			target: ${STAGE}
			dockerfile: Dockerfile

		volumes:
			- .:/app/
			- /app/node_modules
		container_name: nest-app
		ports:
			- ${PORT}:${PORT}
		environment:
			APP_VERSION: ${APP_VERSION}
			STAGE: ${STAGE}
			DB_PASSWORD: ${DB_PASSWORD}
			DB_NAME: ${DB_NAME}
			DB_HOST: ${DB_HOST}
			DB_PORT: ${DB_PORT}
			DB_USERNAME: ${DB_USERNAME}
			PORT: ${PORT}
			HOST_API: ${HOST_API}
			JWT_SECRET: ${JWT_SECRET}

	db:
		image: postgres:14.3
		restart: always
		ports:
			- "5432:5432"
		environment:
			POSTGRES_PASSWORD: ${DB_PASSWORD}
			POSTGRES_DB: ${DB_NAME}
		container_name: ${DB_NAME}
		volumes:
			- postgres-db:/var/lib/postgresql/data

volumes:
	postgres-db:
		external: false
```


## Utilizar un docker-compose.yaml específico

Para utilizar un docker-compose.yaml específico (por si tenemos uno para producción y otro para desarrollo) podemos usar el siguiente comando en la terminal:

```bash
docker compose -f docker-compose.prod.yaml up -d

# Si queremos hacer el build en un servicio/imagen específico
docker compose -f docker-compose.prod.yaml build app
```


# Deployments y registros

Muchas veces necesitaremos desplegar nuestros contenedores en servicios como Azure, AWS, Google Cloud Platform o Digital Ocean, entre otras. Para esto lo que podemos hacer es desplegar nuestros contenedores en Docker Hub utilizando BuildX y luego desde Digital Ocean crear una app utilizando Docker Hub, diligenciar todos los datos de nuestro contenedor desplegado en Docker Hub y realizar toda la configuración de réplicas, variables de entorno y demás para realizar el despliegue.

Para hacer el despliegue utilizando el Container Registry de Digital Ocean, para esto creamos nuestro registro de contenedores privados y crear también un token de acceso a nuestro Container Registry, luego ejecutamos los siguientes comandos para autenticarnos:

```docker
docker logout

docker logout registry.digitalocean.com

docker login registry.digitalocean.com
```

Esto nos pedirá un usuario y contraseña, aquí podremos colocar los token en ambos campos. Una vez estamos logueados podremos hacer la publicación de la siguiente manera:

```docker
docker buildx build \

--platform linux/amd64,linux/arm64 \

-t registry.digitalocean.com/<nombre-container-registry>/<username>/<image>:1.1.0 --push .
```

Luego simplemente tenemos que crear la app utilizando el Container Registry de Digital Ocean, configurar todo lo necesario y ya nuestro contenedor estará listo.


# GitHub Actions

Para hacer despliegues automatizados con GitHub Actions podemos seguir los siguientes pasos:

- Subir nuestros archivos a un repositorio de Git.
- Ir a la sección "Settings" donde podremos crear nuestros Secrets para los Actions.
- Agregar en Repository Secrets nuestro nombre de usuario de Docker y un token de acceso para evitar usar la contraseña directamente.
- Crear el repositorio en Docker Hub.
- Ir a la opción "Actions" donde podremos realizar la creación de la acción que queremos realizar en nuestro repositorio.
- Seleccionar la opción que queramos utilizar, en este caso "Docker Image".

En este momento podremos configurar nuestro archivo YAML para indicar los triggers y las acciones a ejecutar en nuestro pipeline. A continuación un ejemplo:

```yaml
# Nombre del pipeline
name: Docker Image CI

#Triggers
on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

#Acciones
jobs:

  build:

    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      with:
        fetch-depth: 0

    - name: Git Semantic Version
      uses: PaulHatch/semantic-version@v4.0.3
      with:
        major_pattern: "major:"
        minor_pattern: "feat:"
        format: "${major}.${minor}.${patch}-prerelease${increment}"
      id: version

    - name: Docker login
      env:
        DOCKER_USER: ${{ secrets.DOCKER_USER }}
        DOCKER_PASSWORD: ${{ secrets.DOCKER_PASSWORD }}
      run: |
        docker login -u $DOCKER_USER -p $DOCKER_PASSWORD
        echo "New version: $NEW_VERSION!!!!!!"

    - name: Build Docker image
      env:
        NEW_VERSION: ${{ steps.version.outputs.version }}
      run: |
        docker build -t varelajuan860/docker-graphql:$NEW_VERSION .
        docker build -t varelajuan860/docker-graphql:latest .

    - name: Push Docker Image
      env:
        NEW_VERSION: ${{ steps.version.outputs.version }}
      run: |
        docker push varelajuan860/docker-graphql:$NEW_VERSION
        docker push varelajuan860/docker-graphql:latest
```


# Nginx

Nos sirve para realizar balanceos de carga, hosting, entre otras cosas. Una forma muy fácil de hacer uso de ello es:

```bash
# Correr la imagen
docker run --name some-nginx -d -p 8080:80 some-content-nginx

# Abrir la terminal de nuestro contenedor
docker exec it <id-contenedor> bash

# Ver configuración nginx
cd /etc/nginx/conf.d
cat default.conf
```

Copiamos todo lo que encontremos en el archivo `default.conf`, y ponemos la siguiente configuración en location:

```nginx
location / {
	root /usr/share/nginx/html;
	index  index.html index.htm;
	try_files  $uri  $uri/ /index.html;
}
```

Luego configuramos nuestro Dockerfile:

```docker
FROM node:19-alpine3.15  AS dev-deps
WORKDIR /app
COPY package.json package.json
RUN yarn install  --frozen-lockfile

FROM node:19-alpine3.15  AS builder
WORKDIR /app
COPY --from=dev-deps /app/node_modules ./node_modules
COPY . .
RUN yarn build

FROM nginx:1.23.3  AS prod
EXPOSE 80
COPY --from=builder /app/dist /usr/share/nginx/html
COPY assets/ /usr/share/nginx/html/assets
RUN rm /etc/nginx/conf.d/default.conf
COPY nginx/nginx.conf /etc/nginx/conf.d

CMD ["nginx", "-g", "daemon off;"]
```

Y después ejecutamos el comando para hacer la construcción de nuestro contenedor:

```bash
docker build -t <nombre-contenedor> . --no-cache
```