# ¿Qué es?
Docker es una herramienta diseñada para facilitar la creación, implementación y ejecución de aplicaciones mediante el uso de contenedores.

# Alpine-Linux
Alpine Linux es una distribución de Linux ligera y orientada a la seguridad basada en musl libc y busybox.

# Imagen
Es un archivo construido por capas, que contiene todas las dependencias para ejecutarse, tal como: las dependencias, configuraciones, scripts, archivos binarios, etc.

## Traer una imagen
Sirve para traer una imagen.
```bash
docker pull hello-world
```

## Listar imagenes
```bash
docker image ls
```

## Eliminar una o más imagenes
Se puede usar el id que da `docker image ls`, tambien sirven los primeros 3 caracteres o el nombre de la imagen.
```bash
docker image rm IMAGE_ID
docker image prune -a
```

# Contenedor
Es una instancia de una imagen ejecutándose en un ambiente aislado.

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

## Variables de entorno
Hay que agregar el -e. En este ejemplo le asigna a POSTGRESS_PASSWORD el valor mysecretpassword. El nombre del contenedor es con --name.
```bash
docker container run --name some-postgres -e POSTGRES_PASSWORD=mysecretpassword -d postgres
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

## Terminal Interactiva
Iniciar un comando shell dentro del contenedor. Si da error con Gitbash probar con cmd o powershell.
```bash
docker exec -it CONTAINER EXECUTABLE
docker exec -it web bash
docker exec -it web /bin/sh
docker exec -it fd2 /bin/sh
```

# Volumen
Proporcionan la capacidad de conectar rutas específicas del sistema de archivos del contenedor a la máquina host. Si se monta un directorio en el contenedor, los cambios en ese directorio también se ven en la máquina host.

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
### Problema con GitBash
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
El error `'C:/Program Files/Git/app' is invalid, it needs to be an absolute path` indica que la forma en que Git Bash está interpretando `$(pwd)` no está generando una ruta absoluta válida para Docker en Windows.
En Git Bash, `$(pwd)` retorna una ruta estilo Unix (por ejemplo: `/c/Users/tu-usuario/proyecto`), pero Docker en Windows (especialmente si usas Docker Desktop con WSL2 o Hyper-V) espera rutas estilo Windows (`C:\Users\tu-usuario\proyecto`) o rutas absolutas Unix montadas correctamente según tu backend.
Para que funcione el comando quedaria de la siguiente forma.
```bash
docker container run \
--name nest-app \
-w //app \
-p 80:3000 \
-v "$(pwd -W)":/app \
node:18-alpine3.16 \
sh -c "yarn install && yarn start:dev"
```
pwd -W: obtiene la ruta pero estilo windows. Esto hará que la ruta se vea como `C:\Users\tu-usuario\proyecto`, que sí es válida para Docker.

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

## postgres
```bash
docker container run \
-d \
--name postgres-db \
-e POSTGRES_PASSWORD=123456 \
-v postgres-db:/var/lib/postgresql/data \
postgres:15.1
```

## pgAdmin
```bash
docker container run \
--name pgAdmin \
-e PGADMIN_DEFAULT_PASSWORD=123456 \
-e PGADMIN_DEFAULT_EMAIL=superman@google.com \
-dp 8080:80 \
dpage/pgadmin4:6.17
```

# Container Networking
Si dos o más contenedores están en la misma red, podrán hablar entre sí. Si no lo están, no podrán.

### Conectarse a una red
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

# Docker Compose
Docker Compose es una herramienta que se desarrolló para ayudar a definir y compartir aplicaciones de varios contenedores.
En un directorio debemos crear un archivo llamado `docker-compose.yml`

## A tener en cuenta
Hay que ser cuidadosos con la tabulacion y los espacios porque eso significa diferentes cosas en los archivos .yml.
## Version
Primero hay que especificar la version de docker-compose. La version 2 es la legacy y la 3.X es la mas nueva. YA NO ES NECESARIO ESPECIFICAR LA VERSION.

## Red
No hace falta crear red, docker compose crea automaticamente una red asi que todos los servicios que creemos dentro del archivo estaran en la misma red.

## Volumen
Para usar volumenes tenemos 2 formas, hacer que docker compose lo cree automaticamente o asignar uno creado antes.
### Lo crea docker compose
Aqui le decimos a docker compose que debe crear ese volumen. Va al final del archivo.
```yml
volumes:
  postgres-db:
```

### Externo
Para usar un volumen externo hay que especificarlo en el archivo. Si antes teniamos uno que creo docker compose para que los cambios tengan efecto tendremos que recrear los contenedores. Va al final del archivo.
```bash
volumes:
  postgres-db:
    external: true
```

### Bind Volume
Cuando definimos el servicio le podemos decir que la informacion este en un directorio especifico de nuestra computadora.
```bash
services:
  db:
    container_name: postgres_database
    image: postgres:15.1
    volumes:
      - ./postgres-data:/var/lib/postgresql/data
    environment:
      - POSTGRES_PASSWORD=123456
```


## Levantar el archivo
```bash
docker compose up
```

## Bajar los contenedores
Esto borra los contenedores y la red.
```bash
docker compose down
```

## Logs
Para ver los logs es igual que con los contenedores.
```bash
docker compose logs
docker compose logs -f
```

## postgres-pgadmin
```yml
version: '3'

services:
  db:
    container_name: postgres_database
    image: postgres:15.1
    volumes:
      #- postgres-db:/var/lib/postgresql/data
      - ./postgres-data:/var/lib/postgresql/data
    environment:
      - POSTGRES_PASSWORD=123456

  pgAdmin:
    depends_on:
      - db
    image: dpage/pgadmin4:6.17
    ports:
      - "8080:80"
    volumes:
      - ./pgadmin:/var/lib/pgadmin
    environment:
      - PGADMIN_DEFAULT_PASSWORD=123456
      - PGADMIN_DEFAULT_EMAIL=superman@google.com

#volumes:
#  postgres-db:
#    external: true
```

### mongo_db-mongo_express-nest
```yml
services:
  mongo-db:
    container_name: ${MONGO_DB_NAME}
    image: mongo:6.0
    volumes:
      - poke-vol:/data/db
      - poke-config-vol:/data/configdb
    # ports:
    #   - 27017:27017
    restart: always
    environment:
      MONGO_INITDB_ROOT_USERNAME: ${MONGO_USERNAME}
      MONGO_INITDB_ROOT_PASSWORD: ${MONGO_PASSWORD}
    command: ['--auth']

  mongo-express:
    depends_on:
      - mongo-db
    image: mongo-express:1.0.0-alpha.4
    environment:
      - ME_CONFIG_MONGODB_ADMINUSERNAME=${MONGO_USERNAME}
      - ME_CONFIG_MONGODB_ADMINPASSWORD=${MONGO_PASSWORD}
      - ME_CONFIG_MONGODB_SERVER=${MONGO_DB_NAME}
    ports:
      - 8080:8081
    restart: always

  poke-app:
    depends_on:
      - mongo-db
      - mongo-express
    image: klerith/pokemon-nest-app:1.0.0
    ports:
      - 3000:3000
    environment:
      - MONGODB=mongodb://${MONGO_USERNAME}:${MONGO_PASSWORD}@${MONGO_DB_NAME}:27017
      - DB_NAME=${MONGO_DB_NAME}
    restart: always

volumes:
  poke-vol:
    external: false
  poke-config-vol:
    external: false
```


# Dockerfile
Un archivo de texto con instrucciones necesarias para crear una imagen. Se puede ver como un blueprint o plano para su construcción.


## Instrucciones
### FROM
Primero hay que especificar la imagen en la que nos basaremos, se recomienda que sean de alpine porque son mas livianas.

### WORKDIR
Por defecto las imagenes crean un directorio /app para que pongamos alli nuestra apliacion. Todo estara ejecutandose dentro de ese directorio o el que creemos.

### COPY
Es para copiar archivos y el ultimo argumento es el destino.

### RUN
Es para ejecutar comandos.

### CMD
Es para ejecutar comandos una vez esta corriendo la app. Estos comandos no se ejecutan a la hora de crear la imagen, se ejecutaran cuando se use esa imagen en un contenedor.

## Construir Imagen
Para construir la imagen es con el siguiente comando. El -t es para darle el nombre y el ultimo comando es para indicar donde esta el Dockerfile que en este caso lo corri en el directorio donde estaba por eso el ..
```bash
docker build --tag cron-ticker .
```

### Agregar tags
```bash
docker build --tag cron-ticker:1.0.0 .
```

### Cambiar tags
Con esto tanto la 1.0.0 como la bufalo apuntan a la misma imagen id. O sea se sobreescribe bufalo.
```bash
 docker image tag cron-ticker:1.0.0 cron-ticker:bufalo
```

## Subir imagen a DockerHub
Primero debemos crear un repositorio. Tambien hay que agregar el tag que nos da docker-hub a nuestro latest o a cualquier tag(version).
```bash
docker image tag cron-ticker noris98/cron-ticker
```

Despues debemos autenticarnos.
```bash
docker login
```

Finalmente debemos hacer un push de la imagen. En este caso se sube latest porque no se especifica el tag(version).
```bash
docker push noris98/cron-ticker
docker push noris98/cron-ticker:latest
docker push noris98/cron-ticker:pantera
docker push noris98/cron-ticker:tigre
docker push noris98/cron-ticker:1.2
```

## Dockerignore
Es como el .gitignore. Es para excluir archivos o carpetas que no necesitamos como por ej node_modules.

## Remover archivos o carpetas de la imagen
```Dockerfile
# Eliminar archivos y directorios innecesarios en prod
RUN rm -rf tests && rm -rf node_modules
```

## Especificar arquitectura
```Dockerfile
FROM --platform=linux/amd64 node:19.2-alpine3.16
```

## BuilderX
Con esto creamos un builder para crear imagenes multiplataforma.
```bash
docker buildx create --name mybuilder --driver docker-container --bootstrap
```

### Listar builders
El que sale con * es el que se esta utilizando actualmente.
```
docker buildx ls
```

### Cambiar de builder
```bash
docker buildx use mybuilder
```

### Especificar plataformas y hacer el push
```bash
docker buildx build --platform linux/amd64,linux/arm64 \
-t noris98/yt-dw --push .
```

