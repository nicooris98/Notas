# Notas Martin Valdez

## Clase 08/09/21:

modificador acceso tipo dato y nombre

modificador de acceso

public; private(solo la clase); protected(es de la familia, solo los hijos); internal(solo en el proyecto);

### Metodo:

mod-acc void nombre

### Constructor:

nombreClase(){}

### propiedad:

int nombre {get;set;}

### Tarea:

Concesionara de autos

Leer interfaz

### Concesionaria

Clases:

- Auto
- Persona
- Vendedor
- Mecanico

## Clase 09/09/21

Faltaba la clase concesionaria y cliente.

Castear es convertir un tipo en otro

Las clases se heredan y las interfaces se implementan. Interfaz es un contrato

Ejercicio:

minicomponente de 3 botones: boton play, boton set mp3, boton set cd

no usar if

## Clase 14/09/21

Herencia simple: Heredo para simplificar codigo.

Clase abstracta es una clase que sirve para abstraer(generar polimorfismo). Funciona como una clase y genera metodos que pueden ser sobrescribidos lo que permite metodos que puede o no tener logica y le da la posibilidad a los hijos que lo sobreescriban.

virtual en el metodo es para indicar que se va a sobreescribir.

override es el metodo para sobreescribir.

Minicomponente pero con clase abstracta.

static pertenecen a clase. Pueden haber clases estaticas por ej Math. Las constantes son estaticas por defecto.

## Clase 17/09/21

Responsabilidad se encapsula en clases

Ejercicio:

Un automovil tiene un motor, motor tiene piston. Cuando arranque el auto que arranque piston.

## Clase 21/09/21

El ejercicio estaba mal pero no tan mal. La interfaz no es un ayuda memoria sino que se usa para abstraer. Hay que sacar la interfaz.

Ejercicio:

Agregar al auto tanque de nafta. Cada explosion del piston va a consumir 1 punto de nafta. Un for que recorra 100 veces. Cada vuelta de ese for es una explosion del piston. Empiezo con 100lt(o 150).

Cada explosion del piston me aumenta en 1 grado la temperatura del motor. 

## Clase 23/09/21

El ejercicio estaba mal. Tanque es una clase. Aumentar temperatura va en motor y no en piston. Hay que hacer el camino inverso del ejercicio anterior, piston se comunica con motor y motor con auto.

## Patrones

Formalizacion de buenas practicas para resolver problemas comunes. No hay que forzar patrones. Patron de Diseño != Patron de Arquitectura(MVC). Existen 3 clasificaciones: creacionales, estructurales y de comportamiento. Creacional: como debo crear una clase, componente. Estructurales: como definimos las estructuras, como estructuramos todo de forma correcta. Comportamiento: de que forma deberia comportarse. 

## Clase 30/09/21

Empezar por el eslabon mas chico, enfocarse en programarlo bien. En auto un metodo de cargar nafta. Se usa la palabra delegado. No va aumentar temp, tiene que ser un aviso. Motor es un atributo en piston y en el constructor le paso, no en el arrancar de piston. Esta mal la solucion porque motor no debe tener un auto y poder hacer uso de sus metodos(idem con piston con el motor).  

Para resolver se hace mediante interfaz. Imotor con metodo avisoexplotopisto(). En piston ya no tengo motor, tengo Imotor para solo acceder al metodo ese de la interfaz. Creo otra interfaz Iauto y hago lo mismo para que el motor no tenga un atributo auto sino un atributo Iauto. Patron delegate. 

Ejercicio:

Corregir el auto con lo de hoy. Ahora el auto tiene 5 pistones. Tengo 2 marcas de piston(A y B). Algunos seran del A y otros del B(3A y 2B). El piston A consume 1 de nafta y aumenta 1 temperatura. El piston B aumenta 0.5 temp y consume 0.5 de nafta. 500 explosiones en total. No hay if ni switch. 

## DUDAS

- Duda de Angular: En el proyecto de prueba de angular del heroe se crea una interfaz heroe en lugar de una clase, por que?  Rta: En angular las clases se definen como interfaces.

- Diferencia entre definir un metodo con void y sin void 

  ```typescript
  onSelect(hero: Hero): void{
      this.selectedHero = hero;
    }
  onSelect(hero: Hero){
      this.selectedHero = hero;
    }
  ```

- 
