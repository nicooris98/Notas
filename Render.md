# Proyecto
Podemos usar render para desplegar la base de datos y aplicacion. Primero hay que crear un proyecto para tener todo organizado
# Base de Datos
Podemos crear una base de datos y asignarla al proyecto. Es recomendable crear un usuario nuevo. El host sera todo lo que este despues de un @ hasta el .com por ej:
`postgresql://teslo:uTi9Ls2EqE8ojfNbXUjxNhEUYNlSnmqk@dpg-d0oi2uumcj7s73d6s3cg-a.oregon-postgres.render.com/teslodb_zzi6` aqui el host seria `dpg-d0oi2uumcj7s73d6s3cg-a.oregon-postgres.render.com`.

# Subida(Docker)
Para desplegar un backend que tengamos en un repositorio de publico de docker hub.
1. Ir a nuevo servicio.
2. Elegir Web Service.
3. Elegir Existing Image.
4. Ponemos el path de la imagen por ej `noris98/teslo-shop:1.1.0`.
5. Elegimos el nombre.
6. Lo ponemos dentro del proyecto creado.
7. Elegimos la region que mas nos conviene.
8. Elegimos el plan por ej el gratis.
9. Agregamos las variables de entorno(El puerto te lo da render asi que no va).
10. Le damos a desplegar.

# Limpieza
Hay que borrar todo lo que ya no se usa para que no te cobren o no consumir mas de lo necesario.
Hay que borrar el web service, la base de datos y el proyecto.