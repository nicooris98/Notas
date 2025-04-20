# Entorno virtual

Un entorno virtual es un espacio aislado donde puedes instalar paquetes de Python sin afectar el resto de tu sistema o tus otros proyectos.[Entorno Virtual](https://www.youtube.com/watch?v=TNtrAvNNxTY)

## 🤔 ¿Por qué es importante usarlo?

1. Aislamiento de dependenciasSi tienes varios proyectos y cada uno necesita una versión diferente de una librería (por ejemplo, Django 3.2 en uno y Django 4.0 en otro), sin entornos virtuales habría conflictos.

2. Evitar ensuciar el sistemaInstalar paquetes globalmente puede generar desorden y errores. Con un entorno virtual, todo lo que instalas queda en una carpeta separada del sistema.

3. Fácil de replicar en otros equiposUsas un archivo requirements.txt que contiene los paquetes del entorno. Así, cualquiera puede crear el mismo entorno con `pip install -r requirements.txt`.

## Crear un entorno virtual

```bash
mkdir myproject
cd myproject
py -3 -m venv .venv
```

## Activar el entorno

Antes de trabajar en su proyecto, active el entorno correspondiente:

```bash
.venv\Scripts\activate
```

El prompt del shell cambiará para mostrar el nombre del entorno activado.

## Desactivar el entorno

En un entorno activado, ejecutar:

```bash
deactivate
```

# Paquetes

Directorios(carpetas) donde se almacenan modulos relacionados entre si.[Archivo init.py](https://www.youtube.com/watch?v=sgcTujbQhmA&list=PL_wRgp7nihybbJ2vZaVGI5TDdPaK_dFuC&index=28)

## ¿Para que sirven?

Para organizar mejor el codigo y poder reutilizarlo mejor.

## ¿Como se crea?

Crear una carpeta con un archivo dentro llamado `__init__.py`. Lo que hace `__init__.py` es "convertir" un directorio en un modulo(paquete) que contiene otros modulos, y esto lo hace para poder importarlos.

# Errores/Bugs
Errores comunes que fui descubriendo.
## Linea en blanco al final
A veces cuando da error y el archivo esta bien puede ser porque falte agregar una linea en blanco al final del codigo. Cuando el archivo no tiene una linea en blanco al final puede causar que el modulo no se cargue bien por lo que estes tratando de usar o importar "no exista" al momento de la importacion.