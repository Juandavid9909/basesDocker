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


# Variables de entorno

Podemos utilizar variables con los tags cuando ejecutamos un comando en **Docker** donde podremos setear un valor específico que necesitamos y sabemos que usaremos varias veces. Estas también nos permiten renombrar el contenedor que estamos ejecutando, por ejemplo si tenemos una imagen de `postgres` y colocamos el comando `docker container run --name some-postgres -d postgres` esto le asignará el nombre `some-postgres` a nuestro contenedor. Las variables de entorno de las imágenes las podemos setear con la bandera `-e` como se puede ver a continuación:

```docker
docker container run `
	--name postgres-alpha `
	-e POSTGRES_PASSWORD=mypass1 `
	-dp 5432:5432
	postgres
```