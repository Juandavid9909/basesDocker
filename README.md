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