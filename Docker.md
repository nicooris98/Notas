# ¿Qué es?
Docker es una herramienta diseñada para facilitar la creación, implementación y ejecución de aplicaciones mediante el uso de contenedores.

# Alpine-Linux
Alpine Linux es una distribución de Linux ligera y orientada a la seguridad basada en musl libc y busybox.

# Imagen
Es un archivo construido por capas, que contiene todas las dependencias para ejecutarse, tal como: las dependencias, configuraciones, scripts, archivos binarios, etc.

# Contenedor
Es una instancia de una imagen ejecutándose en un ambiente aislado.

# Volumen
Proporcionan la capacidad de conectar rutas específicas del sistema de archivos del contenedor a la máquina host. Si se monta un directorio en el contenedor, los cambios en ese directorio también se ven en la máquina host.

# Container Networking
Si dos o más contenedores están en la misma red, podrán hablar entre sí. Si no lo están, no podrán.

# Comandos
## Traer una imagen
Sirve para traer una imagen.
```bash
docker pull hello-world
```

## Correr un contenedor
Sirva para correr un contenedor y al final se le indica la imagen que debe montar.
```bash
docker container run hello-world
```

### Correr la imagen desenlazada(detached)
Hay que agregar el -d. Esto es para que la terminal pueda seguir usandose y no la bloquee.
```bash
docker container run -d docker/getting-started
```

### Especificar puerto(publish)
Hay que agregar el -p. El de la izquierda es el puerto de mi pc que quiero exponer(publicar) y el de la derecha es el puerto que expone el contenedor.
### Correr la imagen desenlazada
```bash
docker container run -d -p 80:80 docker/getting-started
```

## Detener uno o más contenedores
```bash
docker container stop CONTAINER_ID
```


## Iniciar uno o más contenedores detenidos
```bash
docker container start CONTAINER_ID
```

## Listar contenedores
Solo muestra los que estan corriendo.
```bash
docker container ls
```

#### Listar todos
```bash
docker container ls -a
```

## Borrar uno o más contenedores
Se puede usar el id que te da `docker container ls` o tambien solo los primeros 3 caracteres para acortar.
```bash
docker container rm CONTAINER_ID
```

### Borrar de forma forzada
Si el contenedor que quieres borrar esta corriendo entonces no te dejara borrarlo, primero deberias detenerlo o borrarlo de forma forzada con -f.
```bash
docker container rm -f CONTAINER_ID
```

## Borrar todos los contenedores detenidos
```bash
docker container prune
```

## Listar imagenes
```bash
docker image ls
```

## Eliminar una o más imagenes
Se puede usar el id que da `docker image ls`, tambien sirven los primeros 3 caracteres o el nombre de la imagen.
```bash
docker image IMAGE_ID
```

## Variables de entorno
Hay que agregar el -e. En este ejemplo le asigna a POSTGRESS_PASSWORD el valor mysecretpassword. El nombre del contenedor es con --name.
```bash
docker run --name some-postgres -e POSTGRES_PASSWORD=mysecretpassword -d postgres
```

### Barra invertida
Podemos usar la barra invertida para separar en varias lineas el comando, esto es util cuando el comando lleva muchas variables de entorno.
```bash
docker container run \
-dp 3306:3306 \
--name world-db \
-e MARIADB_USER=example-user \
-e MARIADB_PASSWORD=example-password \
-e MARIADB_ROOT_PASSWORD=secret-password \
-e MARIADB_DATABASE=world-db \
mariadb:jammy
```

## Tags
Cuando descargas una imagen puedes usar un tag en especifico para especificarle a docker que version queremos. En este caso estamos diciendole a docker que queremos una imagen de postgres 14 con el sistema operativo alpine en su version 3.17.
```bash
docker container run --name postgres-alpha -e POSTGRES_PASSWORD=mypass1 -dp 5433:5432 postgres:14-alpine3.17
```

## Logs
Para poder ver los logs del contenedor debemos usar el comando logs.
```bash
docker container logs CONTAINER_ID
```

### Seguir los nuevos logs
Hay que agregar --follow.
```bash
docker container logs --follow CONTAINER_ID
```

## Crear Volumen
```bash
docker volume create VOLUME_NAME
```

## Listar volumenes
```
docker volume ls
```

## Inspeccionar volumen
```bash
docker volume inspect VOLUME_NAME
```

## mariadb
```bash
docker container run \
-dp 3306:3306 \
--name world-db \
-e MARIADB_USER=example-user \
-e MARIADB_PASSWORD=example-password \
-e MARIADB_ROOT_PASSWORD=secret-password \
-e MARIADB_DATABASE=world-db \
--volume world-db:/var/lib/mysql \
mariadb:jammy
```

## phpMyAdmin
```bash
docker container run \
--name phpmyadmin \
-dp 8080:80 \
-e PMA_ARBITRARY=1 \
phpmyadmin:5.2.0-apache
```

### Conectarse
Luego para conectarse a una base de datos dentro de un contenedor hay que poner el contenedor de phpmyadmin y el de la base de datos en la misma red. Una vez que esten en la misma red el nombre del server sera el del contenedor de la base de datos.

## Crear una red
```
docker network create world-app
```

## Listar redes
```bash
docker network ls
```

## Conectar contenedor a una red
Hay que usar el comando connect. Primero va el nombre de la red(world-app en el ej) tambien se puede usar el network id que te da `docker network ls`. Luego el nombre o id de los contenedores
```bash
docker network connect world-app d45
```

## Inspeccionar una red
```bash
docker network inspect world-app
```

## Asignar red desde inicializacion
Cuando creas un contenedor puedes asignarle una red desde el inicio con el --network seguido del nombre de la red.
```bash
docker container run \
-dp 3306:3306 \
--name world-db \
-e MARIADB_USER=example-user \
-e MARIADB_PASSWORD=example-password \
-e MARIADB_ROOT_PASSWORD=secret-password \
-e MARIADB_DATABASE=world-db \
--volume world-db:/var/lib/mysql \
--network world-app \
mariadb:jammy
```

```bash
docker container run \
--name phpmyadmin \
-dp 8080:80 \
-e PMA_ARBITRARY=1 \
--network world-app \
phpmyadmin:5.2.0-apache
```

## Bind Volumes
Bind volumes trabaja con paths absolutos.
-w: working directory. Es como si hiciera un cd app dentro del contenedor.
pwd: es el comando de linux para saber en que ruta estamos.
sh: es un comando de shell. Lo que esta haciendo es que cuando termine de montar el contenedor ejecute el comando dentro de las comillas.

```bash
docker container run \
--name nest-app \
-w //app \
-p 80:3000 \
-v "/$[PWD]":/app \
node:16-alpine3.16 \
sh -c "yarn install && yarn start:dev"
```
## Problema con GitBash
Cuando ejecutamos el siguiente comando da el siguiente error 'C:/Program Files/Git/app' is invalid, it needs to be an absolute path'.
```bash
docker container run \
--name nest-app \
-w /app \
-p 80:3000 \
-v "$(pwd)":/app \
node:18-alpine3.16 \
sh -c "yarn install && yarn start:dev"
```
Para ejecutar comandos de Docker que involucran rutas de archivos Git Bash intenta convertir las rutas de estilo POSIX a rutas de Windows, lo que puede causar problemas.
Para que funcione el comando quedaria de la siguiente forma.
```bash
docker container run \
--name nest-app \
-w //app \
-p 80:3000 \
-v "/[PWD]/":/app \
node:18-alpine3.16 \
sh -c "yarn install && yarn start:dev"
```
${PWD}: obtiene el directorio actual y el / previo a  ${PWD} evita el problema de las rutas POSIX.

Tambien se podria usar una ruta absoluta en lugar de una ruta relativa aunque ya queda mas largo y si alguien usa el mismo comando deberia cambiar siempre esa parte.Hay que usar dobles barras inclinadas (//) al principio de tu ruta por el problema de git bash anterior.
```bash
docker container run \
--name nest-app \
-w //app \
-p 80:3000 \
-v //d/Proyectos/docker/nest-graphql/:/app \
node:18-alpine3.16 \
sh -c "yarn install && yarn start:dev"
```
