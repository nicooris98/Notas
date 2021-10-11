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

   ![image-20210903152055111](/home/oris/.config/Typora/typora-user-images/image-20210903152055111.png)

2. Registrar en modulo

   ![image-20210903152221729](/home/oris/.config/Typora/typora-user-images/image-20210903152221729.png)

3. Etiqueta HTML

   ![image-20210903152555176](/home/oris/.config/Typora/typora-user-images/image-20210903152555176.png)

4. Añadirlo en componente

   ![image-20210903152328609](/home/oris/.config/Typora/typora-user-images/image-20210903152328609.png)

5. Renderizarlo

   ![image-20210903152401268](/home/oris/.config/Typora/typora-user-images/image-20210903152401268.png)

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

![image-20210903153255472](/home/oris/.config/Typora/typora-user-images/image-20210903153255472.png)

`<p>{{nombre.toUpperCase()}}</p>`

`<p>{{getProfesion()}}</p>`

![image-20210903153336554](/home/oris/.config/Typora/typora-user-images/image-20210903153336554.png)

## **Directivas**

### ngFor

`<p *ngFor="let pelicula of peliculas">{{pelicula}}</p>`

![image-20210904140046337](/home/oris/.config/Typora/typora-user-images/image-20210904140046337.png)

`<p *ngFor="let movie of movies">Nombre: {{movie.nombre}} - Puntuación: {{movie.puntuacion}}/10</p>`

![image-20210904140416471](/home/oris/.config/Typora/typora-user-images/image-20210904140416471.png)

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

![image-20211004121237613](/home/oris/.config/Typora/typora-user-images/image-20211004121237613.png)

## ngModel

Sirve para bindear datos en 2 direcciones.

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

