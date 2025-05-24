# Introducción
## ¿Qué es?
Bash es el interprete, o el lenguaje interprete de ordenes, para el sistema operativo gnu.

## Interprete
Basicamente, un interprete es simplemente un procesador de macros que ejecuta instrucciones. El termino procesador de macros significa funcionalidad donde texto y sımbolos son expandidos para crear expresiones mas grandes.
Un interprete de Unix es tanto un interprete de ordenes como un lenguaje de programacion. Como un interprete de instrucciones, el interprete proporciona la interfaz de usuario a un variado conjunto de utilidades de gnu. Las funcionalidades del lenguaje de programacion permiten que estas utilidades se combinen. Se pueden crear archivos que contienen instrucciones, y convertirse ellos mismos en instrucciones. Estas nuevas ordenes tienen la misma naturaleza que instrucciones del sistema en directorios como /bin, permitiendo que usuarios o grupos puedan establecer entornos personalizados para automatizar sus tareas comunes.

Bash es un acronimo para ‘Bourne-Again SHell’. El Bourne shell es el interprete tradicional de Unix escrito originalmente por Stephen Bourne. Todas las instrucciones integradas del Bourne shell estan disponibles en Bash. Las reglas para evaluacion y entrecomillado se toman de la especificacion posix para el interprete Unix "estandar".

# Creación
Debemos crear un archivo .sh.
# Permiso de ejecución
Para poder ejecutar un script primero hay que darle permiso de ejecucion.
```bash
chmod +x script.sh
```
Sabremos que el script puede ejecutarse si hacemos ls y vemos los permisos o simplemente veremos que el color cambia y eso indica que puede ejecutarse.

# Setear interprete
Va al principio del archivo.
```
#!/bin/bash
```
# Ejecución
Para ejecutarlo es con el ./nombre_script.sh.
```bash
./script.sh
```

# Guardar contenido de comandos
Podemos guardar el contenido de un comando.
```bash
comando=$(ls -l)

echo $comando
```

# Condicional
Se usan las palabras reservadas if, elif, else, then y fi.
```bash
if [ "$user" == "root" ]; then
        echo "Estas ejecutando el script como root"
elif [ "$user" == "nico" ]; then
        echo "Estas ejecutando el script como nico"
else
        echo "Ninguno de los 2 usuarios ejecuto el script"
fi
```

# Bucle
```bash
for variable in {1..5} do
	echo "$variable"
done
```

# While
```bash
count = 1

while [ "$count" -lt 5]; do
	echo "En esta vuelta la variable contador vale $contador"
	count--
done
```
# Input de usuario
Podemos asignar los valores que ingrese el usuario a una variable.
```bash
read -p "Escribe tu nombre: " name
```