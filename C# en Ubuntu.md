# Nota de C# en Ubuntu

## Crear proyecto

```terminal
cd ~
mkdir MyApplication
cd MyApplication
dotnet new console
```

A veces hace falta poner sudo en el `dotnet`

`dotnet-runtime-50.dotnet como dotnet50`

## Ejecutar proyecto

```terminal
dotnet run
```

## Crear Clase

```c#
using System;

namespace MyApplication//Se cambia el nombre
{
    public class Persona//Se cambia el nombre
    {
        //El codigo aqui
    }
}
```

## Herencia

Se agrega : a la clase y se pone el nombre de la clase padre. En el constructor se agrega `:base` seguido de los parametros que necesita el constructor padre.

```c#
using System;

namespace MyApplication
{
    public class Alumno : Persona//Hereda de Persona
    {
        int numeroMatricula;
		
        //:base para mandarle los parametros que necesita Persona
        public Alumno(string nombre, int edad, int num) : base(nombre, edad){
            this.numeroMatricula = num;
        }
    }
}
```

