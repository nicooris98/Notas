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

## Clase 12/10/21

Patron Singleton.  En este ejercicio es mejor tener 5 pistones en motor que un array de pistones, si usamos array da la posibilidad a quitar o agregar pistones en tiempo de ejecucion. Piston es clase abstracta. El metodo arrancar en motor es virtual. Seteo las propiedades en el constructor de a y b, no seteo en la clase abstracta. El metodo arrancar tambien es abstracto. En la interfaz de motor paso parametros. El hilo en piston.

Singleton me garantiza una sola instancia de un objeto. Una propiedad estatica es la que no necesita el objeto para que este "viva". Tenes que tener una propiedad estatica del tipo de la clase, el constructor debe ser privado y un metodo estatico que me devuelva algo del mismo objeto. Los metodos estaticos solo pueden acceder a propiedades estaticas. Patron state.

Ejercicio:

Auto tiene un radiador que cuando la temp del motor llega a 100 se dispara el radiador y le baja un 60% temp y lo enfria.

## Clase 14/10/21

Radiador es de auto. Auto debe avisar al radiador para enfriar. El que avisa al auto es el motor porque motor tiene la temperatura. Motor disminuye temperatura. Agregar metodo en interfaz iauto para bajar termperatura. En motor metodo enfriar con un porcentaje de enfriamiento(parametro). Radiador tiene propiedad double cuanto enfria. En constructor se pone 0.6. Agregar imotor en radiador y agregar el metodo enfriar en imotor. Interfaz es como un filtro. El radiador no hace calculos sobre temperatura, lo hace el motor porque el tiene la temperatura.

Ejercicio:

Corregir ejercicio anterior.

## Clase 26/10/21

Patron state: el objeto contexto implementa una interfaz estado y de esta heredan los estados especificos que realizara el objeto dependiendo del estado en que se encuentre.

Semi builder. Wizard es como un instalador. Builder: la creacion del objeto se la dejo a otro, no se encarga el wizard. 

Problematica new orden y pasar por parametro. Agrego OrderBuilder. El builder es una bolsa que recolecta muchos datos. Hay que usar singleton en orderbuilder. Al final de todas las pantallas creo Order y le asigno el valor de orderbuild con build. Uso orderbuild para la permanencia de datos entre pantallas.

```
public class Order {
	public string NombreCliente { get; set; }
	public string Direccion { get; set; }
	public List<Producto> Productos { get; set; }
	public DateTime Fecha { get; set; }
}

public class Producto {
	public string Nombre { get; set;}
	public int Cantidad { get; set; }
}

public class OrderBuilder {
	private string NombreCliente { get; private set; }//set privado de esta forma
	private string Direccion { get; set; }
	private List<Producto> Productos { get; set; }
	private DateTime Fecha { get; set; }
	
	//metodos de setear nombre y direccion
	
	public Order Build() {
		return new Order
		{
			NombreCliente = this.NombreCliente;
			//idem con otras prop
		};
	}
}
```



## DUDAS

- Duda de Angular: En el proyecto de prueba de angular del heroe se crea una interfaz heroe en lugar de una clase, por que?  Rta: En angular las clases se definen como interfaces.

- Diferencia entre definir un metodo con void y sin void . Es lo mismo, por defecto lo hace void.

  ```typescript
  onSelect(hero: Hero): void{
      this.selectedHero = hero;
    }
  onSelect(hero: Hero){
      this.selectedHero = hero;
    }
  ```

- No me deja declarar una variable sin inicializarla a menos que le ponga el ?. Siempre las inicializo con '' en caso de string por ej, estaria bien?. Depende de lo que necesitemos, mejor instanciarlo.

  ```typescript
  email: string;
  email?: string;
  ```

- No necesito una instancia de Radiador para bajar la temperatura pero el Radiador es parte del motor. Se crea o no?

- Necesitaria singleton para hacer todas las consultas creo. Los servicios de angular ya utilizan singleton por defecto

- Problema con la imagen de las CEs. Resuleto
- Agregar un favotito a servants, ce y command codes.
- Problema al volver a buscar despues de favoritos en vez de salirme 2866 coincidencias me salen 1433.
