# ¿Qué es?
NestJS es un framework para el desearrollo de aplicaciones Node.js en el lado del servidor. Se programa en TypeScript y proporciona una arquitectura en la aplicación que permite el desarrollo de aplicaciones más fáciles de mantener. Su arquitectura está bastante inspirada en Angular lo que facilita el trabajo al equipo de desarrollo al no tener que usar dos formas diferentes de trabajo en el backend y en el frontend.

# Instalación

```bash
npm i -g @nestjs/cli
```

## Crear Proyecto

```bash
nest new tutorial-nest-js
```

## Correr Proyecto
```bash
npm run start:dev
```

# Servicios
Los servicios se encargan de abstraer la complejidad y la lógica del negocio a una clase aparte. El CLI de NestJS añade el decorador `@Injectable` a los servicios durante su creación. Estos servicios se podrán inyectar en controladores o en otros servicios.
Archivo `app.service.ts`

```typescript
import { Injectable } from '@nestjs/common';

@Injectable() 
export class AppService {
  getHello(): string { 
    return 'Hello World!';
  }
}
```

## Tip
Es buena práctica comenzar desarrollando todas las funciones que necesitemos ofreciendo inicialmente la funcionalidad de mostrar simplemente que han sido llamadas. Posteriormente, le iremos añadiendo su lógica real de forma progresiva. Esto nos permite tener inicialmente los componentes y las llamadas funcionando e interactuando sin adentrarnos en la complejidad del dominio.

## Crear servicio

```bash
nest g service books
```

El servicio creado está disponible en `books/books.service.ts`
```typescript
import { Injectable } from '@nestjs/common';

@Injectable()
export class BooksService {

    findAll(): any { 
        return 'findAll funcionando';
    }
}
```

# Controladores
El controlador se encarga por un lado de escuchar las peticiones que llegan a la aplicación. Por otro lado, se encarga de preparar las respuestas que proporciona la aplicación. El CLI de NestJS añade el decorador `@Controller` a los controladores durante su creación. NestJS permite el uso de rutas como parámetros del decorador @Controller
Archivo `app.controller.ts`

```typescript
import { Controller, Get } from '@nestjs/common';
import { AppService } from './app.service'; 

@Controller() 
export class AppController {
  constructor(private readonly appService: AppService) {} 

  @Get() 
  getHello(): string { 
    return this.appService.getHello(); 
  }
}
```

## Crear controlador
```bash
nest g controller books
```

Ahora vamos a `books/books.controller.ts`
```typescript
import { Controller, Get } from '@nestjs/common';
import { BooksService } from './books.service'; 

@Controller('books')
export class BooksController {
  constructor(private booksService: BooksService) {} 

  @Get() 
  findAll() { 
    return this.booksService.findAll(); 
  }
}
```

Si ahora llamamos a `http://localhost:3000/api/v1/books` el controlador interceptará la petición, usará el servicio y obtendremos "findAll funcionando".
# Parametros
Usaremos el decorador NestJS @Param para obtener el parámetro de la petición.
```typescript
import { Param } from '@nestjs/common';
...
@Controller('books')
export class BooksController {
...
  @Get(':bookId') 
  findBook(@Param('bookId') bookId: string) { 
    return this.booksService.findBook(bookId); 
  }
...
```

Si ahora llamamos a `http://localhost:3000/api/v1/books/1` el controlador interceptará la petición, asignará 1 al parámetro bookId y obtendremos "findBook funcionando con bookId: 1".

## ¿Nuevo endpoint o sólo parametros?
Puede surgir la duda de si la recuperación de libros de forma ordenada es un nuevo endpoint o se trata de añadir parámetros a un endpoint existente. Es decir, se trata de elegir entre estas dos alternativas:

1. http://localhost:3000/api/v1/books/sort/1
2. http://localhost:3000/api/v1/books?sort=1

Para resolver la duda nos debemos plantear si la estructura de los datos devueltos cambia de un caso a otro o es la misma en los dos casos. Si cambia estaríamos ante un nuevo endpoint. En cambio, si es la misma, estaríamos ante parámetros.

En este caso, la ordenación sigue presentando los datos siguiendo la misma estructura. Es decir, sigue siendo una lista de libros igualmente. Lo único es que se presenta ordenada. El servicio tendrá que capturar los parámetros y devolver los datos de acuerdo a la petición realizada.

Esta misma solución es aplicable si hay varios parámetros. Por ejemplo, ordenación, limitación de cantidad de resultados, offsets, filtrado por algún campo, etc. En todos estos casos se sigue devolviendo una lista de resultados con la misma estructura (p.e. libros).

Como la petición de recuperación de libros de forma ordenada sigue devolviendo una lista de libros con la misma estructura, optamos por implementar esta funcionalidad mediante parámetros, trasladando la lógica de su interptretación al servicio.

## Filtrado mediante parámetros
En la URL se pueden pasar parámetros en forma de una lista de pares clave valor. Por ejemplo: `http://localhost:3000/api/v1/books?sort=1`. Los parámetros son recogidos en NestJS con el decorador `@Query()`.

```typescript
import { Query } from '@nestjs/common';
...
  @Get()
  findAll(@Query('order') order: string) { 
    let params: string[] = []; 

    if (order !== undefined) {
       params.push(`'${order}'`); 
    }

    return this.booksService.findAll(params); 
  }
...
```

## Una solución más dinámica
La solución planteada para el uso de parámetros hace que ante nuevos parámetros en las peticiones se tenga que modificar tanto el controlador (añadiendo nuevos decoradores @Query para los nuevos parámetros) como el servicio, que es el que hace uso de ellos.

El decorador `@Req` nos permite acceder a todos los datos de una petición. En nuestro caso estamos interesados en acceder a query. Esta query contiene un JSON con los pares parámetro-valor pasados en la petición. La idea es pasar directamente este JSON al servicio y que sea el servicio en que se encargue de acceder a su contenido y actuar como corresponda. Esto nos sirve para agarrar cualquier parametro que existe en la petición.

```typescript
import { Req } from '@nestjs/common';
import { BooksService } from './books.service';
import { Request } from 'express';
...

@Controller('books')
export class BooksController {
  constructor(private booksService: BooksService) {}

  @Get()
  findAll(@Req() request: Request) { 
    return this.booksService.findAll(request.query); 
  }
...
}
```

Si hiciéramos la petición `http://localhost:3000/api/v1/books?order=1&limit=10`, request.query contendría lo siguiente: `{ order: '1', limit: '10' }`

## Body
El decorador @Body nos permite acceder al body enviado en una petición.

```typescript
import {
  Post,
  Body,
} from '@nestjs/common';
import { BooksService } from './books.service';
...

@Controller('books')
export class BooksController {
  constructor(private booksService: BooksService) {}
...
  @Post() 
  createBook(@Body() body) { 
    let newBook: any = body; 
    return this.booksService.createBook(newBook); 
  }
}
```

# Tipado de objetos
Hay que usar una clase y un DTO(Data Transfer Object) porque lo que se envia en el body no tiene por que tener la misma estrucura que la clase.

## Interface
`book.class.ts`
```typescript
export class Book {
  id: number;
  title: string;
  genre: string;
  description: string;
  author: string;
  publisher: string;
  pages: number;
  image_url: string;
}
```

## Clase DTO
`book.dto.ts`
```typescript
export class BookDto {
  readonly title: string;
  readonly genre: string;
  readonly description: string;
  readonly author: string;
  readonly publisher: string;
  readonly pages: number;
  readonly image_url: string;
}
```

# Bases de datos
Para trabajar localmente con persistencia necesitamos una base de datos a la que conectarnos. Para no tener que complicarnos con instalaciones y no acoplar el desarrollo a nuestro equipo utilizaremos una imagen Docker de MySQL 5.7. Crearemos una base de datos denominada tutorial. Usaremos las cuenta root con el password secret

```bash
docker run --name tutorial_mysql -e MYSQL_ROOT_PASSWORD=secret -p 3306:3306 -d mysql:5.7 
```

Tras unos instantes (algo más si la imagen de MySQL 5.7 no está descargada en el equipo) habrá un contenedor en ejecución con el nombre tutorial_mysql. Iniciaremos una sesión interactiva para crear una base de datos, a la que denominaremos tutorial

```bash
$ docker exec -it tutorial_mysql bash
root@d0512407a21d:/# mysql -u root -p
Enter password: 
...
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
mysql> create database tutorial; 
Query OK, 1 row affected (0.00 sec)
```
## ORM y el patrón de repositorio
Un ORM nos abstrae del acceso a un gestor de bases de datos específico. Esto nos aisla del gestor de base de datos elegido y hace que podamos cambiar de gestor de bases de datos de forma muy sencilla. TypeORM es un ORM para TypeScript y JavaScript que facilita la interacción con la base de datos. El uso de TypeORM acelera el proceso de desarrollo modelando entidades en el código y sincronizando estos modelos con la base de datos. Actualmente TypeORM ofrece soporte para varias bases de datos relacionales, como PostgreSQL, Oracle, Microsoft SQL Server, SQLite, e incluso para bases de datos NoSQL, como MongoDB.

Por otro lado, el patrón de repositorio nos abstrae de los detalles de la persistencia proporcionando métodos abstractos para las operaciones comunes (crear, guardar, buscar, buscar una, actualizar, eliminar, …​).

Resumiendo, el ORM trabaja con objetos de la base de datos y el repositorio trabaja con objetos del dominio.

Instalaremos los paquetes de TypeORM en el proyecto con
```bash
npm install --save @nestjs/typeorm typeorm mysql
```

## Configuración de la conexión a la base de datos(NO FUNCIONA)
Haremos la configuración de la base de datos en el archivo `app.module.ts` mediante `TypeOrmModule.forRoot()`. Se le pueden pasar los parámetros de configuración directamente. Sin embargo, existe otra opción que consiste en definir la configuración en un archivo ormconfig.json, que es el que de forma predeterminada busca TypeORM.
```typescript
import { TypeOrmModule } from '@nestjs/typeorm';
...
@Module({
  imports: [
    TypeOrmModule.forRoot(), 
    ...
  ],
  ....
})
export class AppModule {}
```

A continuación se muestra el archivo `ormconfig.json`. Este archivo se almacena en la raíz del proyecto, junto al `package.json`.

Archivo `ormconfig.json`
```json
{
  "type": "mysql",
  "host": "localhost",
  "port": 3306,
  "username": "root",
  "password": "secret",
  "database": "tutorial",
  "entities": ["dist/**/*.entity.js"], 
  "synchronize": true 
}
```
### Configuración de los datos de conexión en el propio código
También se puede encontrar que los parámetros de conexión son colocados directamente como argumentos de TypeOrmModule.forRoot().

```typescript
...
    TypeOrmModule.forRoot(
      {
      type: 'mysql',
      host: 'localhost',
      port: 3306,
      username: 'root',
      password: 'example',
      database: 'my_nestjs_project',
      entities: ['dist/**/*.entity.js'],
      synchronize: true,
      }
...
```
El problema de este enfoque está en que las credenciales se adjuntarán en los commits que se hagan de este archivo. En cambio, si almacenamos las credenciales en un archivo ormconfig.json y lo incluimos en el archivo .gitignore, los datos sensibles almacenados en ormconfig.json no serán expuestos al hacer commit.

## Creación de entidades
Las entidades son clases que se corresponden con tablas de la base de datos (colecciones si se trata de MongoDB). En las entidades se definen las columnas y relaciones. Una de esas columnas debe ser la clave primaria.
```typescript
import { Entity, Column, PrimaryGeneratedColumn } from 'typeorm';

@Entity()
export class Book {
  @PrimaryGeneratedColumn() 
  id: number;

  @Column()
  title: string;

  @Column()
  genre: string;

  @Column('text') 
  description: string;

  @Column()
  author: string;

  @Column()
  publisher: string;

  @Column()
  pages: number;

  @Column()
  image_url: string;
}
```
## Servicio
El servicio implementa las funciones habituales para operaciones CRUD (find, findOne, create, delete y update). Se usa el patrón repositorio para trabajar directamente sobre objetos del dominio (libros en nuestro caso) y olvidarnos de los detalles de la persistencia. Como todas las funciones interactúan con bases de datos, todas se programan de forma asíncrona y devuelven una promesa, por lo que habrá que llamarlas con await.

```typescript
import { Injectable, HttpStatus, HttpException } from '@nestjs/common';
import { BookDto } from './book.dto'; 
import { Book } from './book.entity'; 
import { InjectRepository } from '@nestjs/typeorm'; 
import { Repository } from 'typeorm'; 

@Injectable()
export class BooksService {

  constructor(
    @InjectRepository(Book) private booksRepository: Repository<Book>, 
  ) {}

  async findAll(params): Promise<Book[]> { 
    return await this.booksRepository.find(); 
  }

  async findBook(bookId: string): Promise<Book> {
    return await this.booksRepository.findOne({ where: { id: bookId } }); 
  }

  createBook(newBook: BookDto): Promise<Book> {
    return this.booksRepository.save(newBook);
  }

  async deleteBook(bookId: string): Promise<any> {
    return await this.booksRepository.delete({ id: parseInt(bookId) });
  }

  async updateBook(bookId: string, newBook: BookDto): Promise<Book> { 
    let toUpdate = await this.booksRepository.findOne(bookId); 

    let updated = Object.assign(toUpdate, newBook); 

    return this.booksRepository.save(updated); 
  }
}
```

## Controlador
Básicamente, el controlador es el mismo que teníamos para el mockup salvo que ahora devuelve promesas, ya que las funciones del servicio ahora devuelven promesas. Además, se cambia el tipo del objeto libro. Dejamos de usar la interface para pasar a usar la entity del ORM.

```typescript
import {
  Controller,
  Get,
  Param,
  Req,
  Post,
  Body,
  Delete,
  Put,
} from '@nestjs/common';
import { BooksService } from './books.service';
import { Request } from 'express';
import { BookDto } from './book.dto';
import { Book } from './book.entity'; 

@Controller('books')
export class BooksController {

  constructor(private booksService: BooksService) {}

  @Get()
  findAll(@Req() request: Request): Promise<Book[]> { 
    console.log(request.query);
    return this.booksService.findAll(request.query);
  }

  @Get(':bookId')
  findBook(@Param('bookId') bookId: string): Promise<Book> {
    return this.booksService.findBook(bookId);
  }

  @Post()
  createBook(@Body() newBook: BookDto): Promise<Book> { 
    return this.booksService.createBook(newBook);
  }

  @Delete(':bookId')
  deleteBook(@Param('bookId') bookId: string): Promise<Book> {
    return this.booksService.deleteBook(bookId);
  }


  @Put(':bookId')
  updateBook(
    @Param('bookId') bookId: string,
    @Body() newBook: BookDto, 
  ): Promise<Book> {
    return this.booksService.updateBook(bookId, newBook);
  }
}
```

## Módulo para una mejor organización
Es buena práctica que en lugar de añadir cada uno de los providers y los controllers a app.module.ts, los agrupemos cada uno en un módulo con los providers y controllers. Posteriormente, ese módulo se importa en el array imports de app.module.ts. Además, las entidades se colocan en el módulo en un array, como argumento de TypeOrmModule.forFeature().

Archivo books/books.module.ts
```typescript
import { Module } from '@nestjs/common';
import { Book } from './book.entity';
import { BooksService } from './books.service';
import { BooksController } from './books.controller';
import { TypeOrmModule } from '@nestjs/typeorm';

@Module({
  imports: [TypeOrmModule.forFeature([Book])], 
  providers: [BooksService], 
  controllers: [BooksController], 
})
export class BooksModule {}
```

Este archivo ya está preparado para ser colocado en el array imports de app.module.ts.

## Configuracion de ORM
Otra mejora que podríamos realizar para la configuración del uso del ORM podría ser el uso de variables de entorno. Esto evita la introducción de valores sensibles en el código, como contraseñas, usuarios de la base de datos, y demás.

La mejora que haremos se basará en lo siguiente:

1. Inicialización de un archivo de variables de entorno.
2. Creación de un servicio de configuración del ORM a partir de los valores de las variables de entorno.
3. Modificación del archivo app.module.ts para usar la configuración anterior y cargar los módulos correspondientes (p.e. el de BooksModule creado antes).

### Inicialización de un archivo de variables de entorno
Archivo `.env`
```env
TUTORIAL_HOST=localhost
TUTORIAL_PORT=3306
TUTORIAL_USER=root
TUTORIAL_PASSWORD=secret
TUTORIAL_DATABASE=tutorial
```

### Creacion de servicio
Definiremos un servicio de configuración que acceda a las variables de entorno, especifique las variables de entorno que hay que configurar y una función que las configure.
```typescript
import { TypeOrmModuleOptions } from '@nestjs/typeorm'; 

require('dotenv').config();

class ConfigService {
  constructor(private env: { [k: string]: string | undefined }) {}

  private getValue(key: string, throwOnMissing = true): string {
    const value = this.env[key];
    if (!value && throwOnMissing) {
      throw new Error(`config error - missing env.${key}`);
    }

    return value;
  }

  public ensureValues(keys: string[]) {
    keys.forEach(k => this.getValue(k, true));
    return this;
  }

  public getTypeOrmConfig(): TypeOrmModuleOptions { 
    return {
      type: 'mysql', 

      host: this.getValue('TUTORIAL_HOST'), 
      port: parseInt(this.getValue('TUTORIAL_PORT')),
      username: this.getValue('TUTORIAL_USER'),
      password: this.getValue('TUTORIAL_PASSWORD'),
      database: this.getValue('TUTORIAL_DATABASE'),

      entities: ['dist/**/*.entity.js'], 
      synchronize: true, 
    };
  }
}

const configService = new ConfigService(process.env).ensureValues([
  'TUTORIAL_HOST',
  'TUTORIAL_PORT',
  'TUTORIAL_USER',
  'TUTORIAL_PASSWORD',
  'TUTORIAL_DATABASE',
]);

export { configService };
```

### Actualización de app.module.ts

```typescript
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { BooksModule } from './books/books.module';
import { TypeOrmModule } from '@nestjs/typeorm';
import { configService } from './config/config.service';

@Module({
  imports: [
    BooksModule, 
    TypeOrmModule.forRoot( 
      configService.getTypeOrmConfig(),
    ),
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

# Definir un prefijo para la API
Archivo `main.ts`

```typescript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.setGlobalPrefix('api/v1'); 
  await app.listen(3000);
}
bootstrap();
```
La aplicación ahora deberá ser llamada incluyendo el prefijo:

```bash
http://localhost:3000/api/v1
```

Si no incluimos el prefijo y seguimos invocando a http://localhost:3000 obtenendremos el siguiente error. Este error indica que la aplicación no tiene nada que responda en esa ruta a ese tipo de petición HTTP.

```json
{
  "statusCode": 404,
  "message": "Cannot GET /",
  "error": "Not Found"
}
```

# Microservicios
asd