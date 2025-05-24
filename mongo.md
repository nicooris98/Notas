# Equivalente Relacional
Coleccion = Tabla
Documentos = Registros(Rows)

# Docker
Podemos correrlo con docker.
## docker-compose.yml
```yml
services:
  mongo-db:
    image: mongo:8.0.10-rc0-noble
    ports:
      - "27017:27017"
    volumes:
      - mongodb_data:/data/db
      - mongodb_config:/data/configdb

volumes:
  mongodb_data:
  mongodb_config:
```

## Levantar contenedor
```bash
docker-compose up -d
```

## Entrar a la terminal interactiva
```bash
docker-compose exec mongo-db mongosh
```
# Mostrar BD
Solo muestra las colecciones que tienen datos.
```
show databases;
```

# Mostrar BD donde estamos
```
db;
```

# Crear BD
```
use nico-db;
```

# Mostrar colecciones
```
show collections;
```

# Objeto
Para crear un objeto simplemente lo definimos como en javascript.
```js
user1 = {
	'username': 'user1',
	'age': 27,
	'email': 'user1@example.com'
}
```

# Insertar documento
No importa que no exista la coleccion, al insertar el objeto se crea la coleccion.
```
db.users.insert(user1);
```

# Insertar un documento
```
db.users.insertOne(user2);
```

# Insertar documentos
```
db.users.insertMany([
	{
	'username': 'user3',
	'age': 20,
	'email': 'user3@example.com'
},
{
	'username': 'user4',
	'age': 32,
	'email': 'user4@example.com'
},
{
	'username': 'user5',
	'age': 22,
	'email': 'user5@example.com'
}
]);
```
# Listar documentos en una coleccion
Esto retorna un cursor.
```bash
db.users.find();
```

# Save(No sirve en versiones nuevas)
Si el objeto no existe entonces lo crea y si el objeto si existe entonces lo actualiza.
```
db.users.save(user6);
```

# Filtros de busqueda
```
db.users.find({
	username: 'user7'
});
```

```js
db.users.insertMany([{
	'username': 'user11',
	'age': 64,
	'email': 'user11@example.com',
	'status': 'active'
},
{
	'username': 'user12',
	'age': 35,
	'email': 'user12@example.com',
	'status': 'active'
},
{
	'username': 'user13',
	'age': 15,
	'email': 'user13@example.com',
	'status': 'inactive'
},
{
	'username': 'user14',
	'age': 10,
	'email': 'user14@example.com',
	'status': 'inactive'
}]);

```