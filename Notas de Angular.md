# ***Notas de Angular***	

## **Crear Proyecto**

```terminal
ng new my-app
```

## **Iniciar el servidor**

```terminal
cd my-app
ng serve -o
```

## **Componente**

Crear componentes(Forma manual):

1. Crear componente

   ```typescript
   import { Component } from "@angular/core";
   
   @Component({
   	selector: 'hola-mundo',
   	template: '<p>Hola Mundo Component</p>'
   })
   export class HolaMundoComponent{}
   ```

2. Registrar en modulo

   ```typescript
   import { NgModule } from '@angular/core';
   import { BrowserModule } from '@angular/platform-browser';
   
   import { AppRoutingModule } from './app-routing.module';
   import { AppComponent } from './app.component';
   import { HolaMundoComponent } from './hola-mundo.component';
   
   @NgModule({
     declarations: [
       AppComponent,
       HolaMundoComponent,
     ],
     imports: [
       BrowserModule,
       AppRoutingModule
     ],
     providers: [],
     bootstrap: [AppComponent]
   })
   export class AppModule { }
   ```

3. Etiqueta HTML

   ```typescript
   template: '<p>Hola Mundo Component</p>'
   ```

4. Añadir componente en app-component.html

   ```html
   <h1>HOLA MY APP</h1>
   <hola-mundo></hola-mundo>
   ```

5. Renderizarlo

   Ejecutarlo y ver que todo sale bien.

Crear Componentes(Comando):

1. Desde una terminal ir al directorio que contiene el proyecto

2. Ejecutar

   ```terminal
   ng generate component <component-name>
   ```

   Donde <component-name> es el nombre del nuevo componente.

   Por defecto este comando crea:

   - Una carpeta con el nombre del componente
   - Un archivo de componente, <component-name>.component.ts
   - Un archivo de plantilla, <component-name>.component.html
   - Un archivo css, <component-name>.component.css
   - Un archivo para los test, <component-name>.component.spec.ts

Crear Componentes(Extension VSCode - Angular Schematics).

## **String Interpolation**

```typescript
import { Component, OnInit } from '@angular/core';
import { MoviesServices } from '../movies.service';

@Component({
  selector: 'app-nuevo-componente',
  templateUrl: './nuevo-componente.component.html',
  styleUrls: ['./nuevo-componente.component.scss']
})
export class NuevoComponenteComponent implements OnInit {
  nombre = 'Siro Oris';
  peliculas = [
    'Star Wars',
    'Señor de los anillos',
    'Indiana Jones'
  ];
  movies = [] as any;

  constructor(service: MoviesServices) {
    //const service = new MoviesServices();
    this.movies = service.getMovies();
  }

  ngOnInit(): void {
  }

  getProfesion(){
    return 'Front End';
  }

}
```

Uso el nombre y accedo a la funcion en el html.

```html
<p>{{nombre.toUpperCase()}}</p>
<p>{{getProfesion()}}</p>
```

## **Directivas**

### ngFor

```html
<p *ngFor="let pelicula of peliculas">{{pelicula}}</p>
```

```html
<p *ngFor="let movie of movies">Nombre: {{movie.nombre}} - Puntuación: {{movie.puntuacion}}/10</p>
```

### ngIf

```html
<div *ngIf="selectedHero"><!-- Solo muestra si esta definido -->
  <h2>{{ selectedHero.name | uppercase }} Details</h2>
  <div><span>id: </span>{{ selectedHero.id }}</div>
  <div>
    <label for="hero-name">Hero name: </label>
    <input id="hero-name" [(ngModel)]="selectedHero.name" placeholder="name" />
  </div>
</div>
```

## Hacer cambios dinamicos

Definimos una funcion en el typscript

```typescript
nombreFuncion(){
	//hacer algo
}
//Ejemplo
updateEmpresa(){
    var inputEmp = document.getElementById(this.empleado.id.toString()) as HTMLInputElement;
    this.empleado.empresa = inputEmp.value;
  }
```

La agregamos en el html

```html
<input type="button" value="Presionar" (click)="nombreFuncion()">
<!--Ejemplo-->
<input type="text" placeholder="Ingresa Empresa" id={{empleado.id}}>
<input type="button" value="Setear Empresa" (click)="updateEmpresa()">
```

Listo

## ngModel

Sirve para bindear datos en 2 direcciones. O sea, nos permite asignarle un valor a una variable del componente de forma dinamica. Si lo ponemos en un input y usamos el valor de la variable veremos que a medida que introducimos valores la variable cambia y se muestra al instante. Podemos usarlo en una seleccion de valores por ej.

```html
<select [(ngModel)]="seleccion">
    <option value="Toyota">Toyota</option>
    <option value="Fiat">Fiat</option>
    <option value="Ford">Ford</option>
</select>
<p>Has seleccionado: {{ seleccion }}</p>
```

Ej hero.

```html
<div>
  <label for="name">Hero name: </label>
  <input id="name" [(ngModel)]="hero.name" placeholder="name">
</div>
```

Here it binds the hero.name property to the HTML textbox so that data can flow in both directions: from the hero.name property to the textbox, and from the textbox back to the hero.name.

## The missing FormsModule

Es un error de cuando usamos [(ngModel)] 

```terminal
Template parse errors:
Can't bind to 'ngModel' since it isn't a known property of 'input'.
```

Lo que pasa es que ngModel no esta habilitada por defecto y pertenece a FormsModule por lo que hay que agregarla en app.module.ts

## AppModule

Algunos metadatos estan en los decoradores  @Component que agregas a los componentes de tus clases. Otros metadatos criticos estan en los decoradores @NgModule. El decorador @NgModule mas importante esta en la clase AppModule.

## Import FormsModule

Vamos a app.module.ts e importamos FormsModule

```typescript
import { FormsModule } from '@angular/forms'; // <-- NgModel lives here
```

Despues agregamos FormsModule en los imports de metadatos de @NgModule, el cual contiene una lista de modulos externos que la aplicacion necesita.

```typescript
imports: [
  BrowserModule,
  FormsModule
],
```

## ngModelChange

Nos sirve para devolver el valor del evento. Se pone el ngModelChange entre parentesis y el evento con signo $.

```html
<input type="text" [(ngModel)]="titulo" (ngModelChange)="log($event)" placeholder="Ingrese algo">
```

## Error de variable/elemento no definido a mostrar

```
HeroesComponent.html:3 ERROR TypeError: Cannot read property 'name' of undefined
```

## The fix - hide empty details with *ngIf

```html
<div *ngIf="selectedHero">

  <h2>{{selectedHero.name | uppercase}} Details</h2>
  <div><span>id: </span>{{selectedHero.id}}</div>
  <div>
    <label for="hero-name">Hero name: </label>
    <input id="hero-name" [(ngModel)]="selectedHero.name" placeholder="name">
  </div>

</div>
```

When selectedHero is undefined, the ngIf removes the hero detail from the DOM. There are no selectedHero bindings to consider.

When the user picks a hero, selectedHero has a value and ngIf puts the hero detail into the DOM.

## Plantillas

```html
<ng-container *ngIf="!isLogged; else logueado">
    <button (click)="isLogged = true">Iniciar Sesion</button>
    <button>Registrarse</button>
</ng-container>
<ng-template #logueado>
    <button>Ir a Administracion</button>
    <button>Ir a mi perfil</button>
    <button (click)="isLogged = false">Cerrar Sesion</button>
</ng-template>
```

Los ng container no salen en el html. La variable isLogged esta inicializada como false en el typescript por lo que se muestra todo el contenido del ng-container. Cuando se presiona el boton iniciar sesion pasa a tener valor true. Entonces el ng-container desaparece y se va por el else por lo que se muestra el ng-template. El ng-template se muestra porque en el else esta indicando que se muestre logueado que es como se llamo al ng-template.

## Interfaces

En angular las clases son interfaces. 

```typescript
export interface Usuario {
    ID: number;
    Nombre: string;
    Apellidos: string;
    Nick: string;
    Email: string;
    Contrasena: string;
}
```

Luego tenemos lo que son las enumeraciones, que son una lista de posibilidades. Se pone luego del export interface.

```typescript
export interface Usuario {
    ID: number;
    Nombre: string;
    Apellidos: string;
    Nick: string;
    Email: string;
    Contrasena: string;
    Tipo: UserType
}

export enum UserType {
    Administrador,
    Cliente,
    Tecnico
}
```

## Casteo

Para castear un tipo de dato usamos <> y dentro el tipo de dato.

```typescript
user: Usuario = <Usuario>{
    ID: 23,
    Nombre: 'Nicolas',
    Apellidos: 'Oris',
    Email: 'nick@gmail.com',
    Nick: 'Roman98',
    Contrasena: '1234',
    Tipo: UserType.Administrador
  }
```

## Atributos

Para cambiar una propiedad de un elemento hay que ponerla entre corchetes. Para asignarle el valor de una funcion a un evento simplemente se pone parentesis entre el evento y dentro de las comillas el nombre de la funcion que esta en el componente.

```html
<button (click)="unaFuncion()" [disabled]="desactivado">Titulo</button>
```



## **Servicios**

Creamos el servicio(Manual)

`nombreservicio.service.ts`

Creamos las funciones

```typescript
export class NombreServicio{
    algunaFuncion(){
        return 'algo'
}
```

En el constructor asignamos el valor con la funcion creada(tambien se importa el servicio)

```typescript
import { MoviesServices } from '../movies.service';
movies = [] as any;

  constructor(service: MoviesServices) {
    //const service = new MoviesServices();
    this.movies = service.getMovies();
  }
```

Luego vamos a app.module.ts y lo agregamos en el providers(tambine importamos) 

```typescript
providers: [MoviesServices]
```

Otra forma es con la extension y ademas del servicio crea el test. El servicio por defecto tiene

```typescript
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class CinesService {

  constructor() { }
}
```

## Nodejs

Iniciar servidor

`node server`

Ver en shopping list la carpeta backed

