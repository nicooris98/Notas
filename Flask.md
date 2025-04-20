# Instalar Flask

En un entorno activado, usar el siguiente comando:

```bash
pip install Flask
```

# Aplicación sencilla

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello_world():
    return "<p>Hello, World!</p>"

if __name__ == "__main__":
    app.run(debug=True, port=5000)
```

1. Primero importamos la clase Flask. Una instancia de esta clase será nuestra aplicación WSGI.

2. A continuación creamos una instancia de esta clase. El primer argumento es el nombre del módulo o paquete de la aplicación. `__name__` es un atajo conveniente para esto que es apropiado para la mayoría de los casos. Esto es necesario para que Flask sepa dónde buscar recursos como plantillas y archivos estáticos.

3. A continuación, utilizamos el decorador route() para indicar a Flask qué URL debe activar nuestra función.

4. La función devuelve el mensaje que queremos mostrar en el navegador del usuario. El tipo de contenido por defecto es HTML, por lo que el HTML de la cadena será renderizado por el navegador.

5. `app.run` corre la aplicacion.

(Asegúrate de no llamar a tu aplicación flask.py porque esto entraría en conflicto con el propio Flask.).

# Registrar rutas

Aqui se explicara como funciona la registracion de rutas.

## Ejemplo

Usaremos el siguiente ejemplo:

```bash
mi_app/
├── app/
│   ├── __init__.py
│   ├── routes.py
├── run.py
```

Flask necesita saber explícitamente qué rutas manejar. Si no importas routes.py, las rutas no se registraran en la aplicación y no podras acceder a ellas desde tu navegador. Flask tiene un sistema en el que las rutas se registran en la instancia de la aplicación (app). Es por eso que necesitamos asegurarnos de que, al crear la aplicación en `__init__.py`, las rutas definidas en otro archivo (como routes.py) estén registradas en esa aplicación. Si no las importamos, las rutas no serán reconocidas por Flask.

### `app/__init__.py`

En Python, cualquier carpeta que tenga un archivo `__init__.py` se considera un paquete. Este archivo se ejecuta cuando importas ese paquete.En Flask, lo usamos para:

- Crear la instancia de la app Flask

- Cargar configuraciones

- Registrar rutas u otros componentes

Aquí solo creamos la app y registramos las rutas.

```python
from flask import Flask

app = Flask(__name__)  # Se crea la aplicación Flask

from app import routes  # Se importan las rutas para que se registren en la app
```

### `app/routes.py`

En este archivo definimos las rutas (funciones que se ejecutan al visitar ciertas URLs).

```python
from app import app #Desde __init__.py traigo el objeto app

@app.route("/")
def home():
    return "¡Hola, Mundo!"
```

### `run.py`

Aqui se inicia la aplicacion y para eso traigo el objeto app que fue creado en el init. En el archivo init tambien registre las rutas asi que ya no hace falta hacerlo de nuevo.

```python
from app import app #Desde __init__.py traigo el objeto app

if __name__ == "__main__":
    app.run(debug=True)
```

## ¿Cómo funciona esto en conjunto?

1. Cuando ejecutas run.py, se importa app desde app/__init__.py.

2. Dentro de __init__.py, creamos la instancia de Flask y luego importamos routes.py.

3. Al importar routes.py, las funciones como home() son registradas como rutas en la app (esto sucede automáticamente porque usas el decorador @app.route en routes.py).

4. Así, cuando Flask se ejecuta, sabe qué hacer cuando accedes a la ruta / (mostrar "¡Hola, Mundo!").

## Registrar más rutas

Si luego se crean otros archivos de rutas el funcionamiento es el mismo, solamente hay que importarlas en el archivo `__init__.py`.

```Python
from app import routes  # Importar las rutas principales
from app import auth_routes  # Importar las rutas de autenticación
```

# Blueprints

Los Blueprints son una forma de agrupar rutas relacionadas bajo un mismo “módulo” y luego registrarlas en tu aplicación con un prefijo opcional.Esto te permite:

- Separar tu app en partes organizadas (por ejemplo: auth, admin, api, etc).

- Añadir prefijos fácilmente (/auth, /admin, /api...).

- Reutilizar lógica o montar rutas dinámicamente.

Estructura:

```bash
mi_app/
├── app/
│   ├── __init__.py
│   ├── auth/
│   │   ├── __init__.py
│   │   └── routes.py
│   ├── main/
│   │   ├── __init__.py
│   │   └── routes.py
├── run.py
```

## `auth/routes.py`

```python
from flask import Blueprint, render_template

auth_bp = Blueprint("auth", __name__, url_prefix="/auth")

@auth_bp.route("/login")
def login():
    return "Página de login"

@auth_bp.route("/registro")
def registro():
    return "Página de registro"
```

## `main/routes.py`

```python
from flask import Blueprint

main_bp = Blueprint("main", __name__)

@main_bp.route("/")
def index():
    return "Página principal"
```

## `app/__init__.py`

```python
from flask import Flask

def create_app():
    app = Flask(__name__)

    # Importar Blueprints
    from app.auth.routes import auth_bp
    from app.main.routes import main_bp

    # Registrar Blueprints con o sin prefijo
    app.register_blueprint(auth_bp)     # prefijo: /auth
    app.register_blueprint(main_bp)     # sin prefijo

    return app
```

## `run.py`

```python
from app import create_app

app = create_app()

if __name__ == "__main__":
    app.run(debug=True)
```

# Servidor visible desde el exterior

Si ejecutas el servidor notarás que el servidor sólo es accesible desde tu propio ordenador, no desde ningún otro de la red. Este es el valor por defecto porque en el modo de depuración un usuario de la aplicación puede ejecutar código Python arbitrario en su ordenador.

Si tienes el depurador desactivado o confías en los usuarios de tu red, puedes hacer que el servidor esté disponible públicamente simplemente añadiendo --host=0.0.0 a la línea de comandos:

```bash
flask run --host=0.0.0.0
```

Esto le dice a su sistema operativo que escuche en todas las IPs públicas.

# Modo de depuración

El depurador permite ejecutar código Python arbitrario desde el navegador. Está protegido por un pin, pero sigue representando un riesgo de seguridad importante. No ejecute el servidor de desarrollo o el depurador en un entorno de producción.Para activar el modo de depuración, utilice la opción --debug.

```bash
$ flask --app hello run --debug
 * Serving Flask app 'hello'
 * Debug mode: on
 * Running on http://127.0.0.1:5000 (Press CTRL+C to quit)
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: nnn-nnn-nnn
```

## Otra forma

Otra forma de entrar al modo de depuracion es por medio de la funcion run:

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def index():
    return "Hola mundo"

if __name__ == "__main__":
    app.run(debug=True, port=5000)
```

# Escape de HTML

Cuando se devuelve HTML (el tipo de respuesta por defecto en Flask), cualquier valor proporcionado por el usuario en la salida debe ser escapado para protegerlo de ataques de inyección. Las plantillas HTML renderizadas con Jinja, introducidas más tarde, harán esto automáticamente.

escape(), que se muestra aquí, puede utilizarse manualmente. Se omite en la mayoría de los ejemplos por razones de brevedad, pero siempre debes ser consciente de cómo estás utilizando datos no confiables.

```python
from markupsafe import escape

@app.route("/<name>")
def hello(name):
    return f"Hello, {escape(name)}!"
```

Si un usuario logró enviar el nombre `<script>alert("bad")</script>`, el escape hace que se renderice como texto, en lugar de ejecutar el script en el navegador del usuario.

La variable `<name>` en la ruta captura un valor de la URL y lo pasa a la función de la vista.

# Enrutamiento

Las aplicaciones web modernas utilizan URLs significativas para ayudar a los usuarios. Es más probable que a los usuarios les guste una página y vuelvan si la página utiliza una URL significativa que puedan recordar y utilizar para visitar directamente una página.

Utilice el decorador route() para vincular una función a una URL.

```python
@app.route('/')
def index():
    return 'Index Page'

@app.route('/hello')
def hello():
    return 'Hello, World'
```

Puedes hacer más. Puedes hacer que partes de la URL sean dinámicas y adjuntar múltiples reglas a una función.

# URLs dinamicas/Normas variables

Puedes añadir secciones variables a una URL marcando las secciones con `<nombre_de_variable>`. Su función recibe entonces el `<nombre_de_variable>` como argumento de palabra clave. Opcionalmente, puedes usar un convertidor para especificar el tipo de argumento como `<converter:nombre_de_variable>`.

```python
from markupsafe import escape

@app.route('/user/<username>')
def show_user_profile(username):
    # show the user profile for that user
    return f'User {escape(username)}'

@app.route('/post/<int:post_id>')
def show_post(post_id):
    # show the post with the given id, the id is an integer
    return f'Post {post_id}'

@app.route('/path/<path:subpath>')
def show_subpath(subpath):
    # show the subpath after /path/
    return f'Subpath {escape(subpath)}'
```

Tipos de convertidores:

- string: (Por defecto) acepta cualquier texto sin barra

- int: acepta números enteros positivos

- float: acepta valores positivos en coma flotante

- path: como string pero también acepta barras inclinadas

- uuid: acepta cadenas (string) UUID

# URLs únicas / Comportamiento de la redirección

Las dos reglas siguientes difieren en el uso de la barra diagonal final:

```python
@app.route('/projects/')
def projects():
    return 'The project page'

@app.route('/about')
def about():
    return 'The about page'
```

La URL canónica del endpoint projects tiene una barra al final. Es similar a una carpeta en un sistema de archivos. Si accedes a la URL sin la barra final (/projects), Flask te redirige a la URL canónica con la barra final (/projects/).

La URL canónica para el endpoint about no tiene una barra al final. Es similar a la ruta de un archivo. El acceso a la URL con una barra al final (/about/) produce un error 404 «Not Found». Esto ayuda a mantener las URLs únicas para estos recursos, lo que ayuda a los motores de búsqueda a evitar indexar la misma página dos veces.

# Query String

Podemos pasarle muchos parametros a la url y obtenerlos con el objeto request, por ej con la url `http://127.0.0.1:5000/query_string?param1=Jose`:

```python
def query_string():
    print(request)
    print(request.args)
    print(request.args.get("param1"))
    return "OK"
```

# Métodos HTTP

Por defecto, una ruta sólo responde a peticiones GET. Puedes utilizar el argumento methods del decorador route() para manejar diferentes métodos HTTP:

```python
from flask import request

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        return do_the_login()
    else:
        return show_the_login_form()
```

También puedes separar las vistas para diferentes métodos en diferentes funciones. Flask proporciona un atajo para decorar dichas rutas con get(), post(), etc. para cada método HTTP común.

```python
@app.get('/login')
def login_get():
    return show_the_login_form()

@app.post('/login')
def login_post():
    return do_the_login()
```

# Plantillas de renderizado

Generar HTML desde Python no es divertido, y en realidad es bastante engorroso porque tienes que hacer el escape de HTML por tu cuenta para mantener la aplicación segura. Por eso Flask configura el motor de plantillas Jinja2 por ti automáticamente.

Las plantillas pueden utilizarse para generar cualquier tipo de archivo de texto. En el caso de las aplicaciones web, generarás principalmente páginas HTML, pero también puedes generar markdown, texto sin formato para correos electrónicos y cualquier otra cosa.Para renderizar una plantilla puedes utilizar el método render_template(). Todo lo que tienes que hacer es proporcionar el nombre de la plantilla y las variables que quieres pasar al motor de plantillas como argumentos de palabra clave. Este es un ejemplo sencillo de cómo renderizar una plantilla:

```python
from flask import render_template

@app.route('/hello/')
@app.route('/hello/<name>')
def hello(name=None):
    return render_template('hello.html', person=name)
```

Cuando usamos `render_template(arg1, arg2, ...)` le podemos pasar varios argumentos, el primero es el archivo que debe renderizar y los siguientes son los objetos que le podemos pasar para que los renderice de forma dinamica y en este ejemplo el objeto person sera igual al name.Flask buscará las plantillas en la carpeta templates. Así que si su aplicación es un módulo, esta carpeta está al lado de ese módulo, si es un paquete está realmente dentro de su paquete:

- Caso 1: Un módulo:

  ```bash
  /application.py
  /templates
    /hello.html
  ```

- Caso 2: Un paquete:

  ```bash
  /application
    /__init__.py
    /templates
        /hello.html
  ```

Para las plantillas puedes utilizar toda la potencia de las plantillas de [Jinja2](https://jinja.palletsprojects.com/en/stable/templates/).

```html
  <!doctype html>
  <title>Hello from Flask</title>
  {% if person %}
  <h1>Hello {{ person }}!</h1>
  {% else %}
  <h1>Hello, World!</h1>
  {% endif %}
```

## Otro ejemplo:

layout.html:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>{% block title %}{% endblock %}</title>
  <link rel="stylesheet" href="{{ url_for('static', filename='css/layout.css') }}">
</head>
<body>
  {% block body%}
  {% endblock %}
  <script src="{{ url_for('static', filename='js/layout.js') }}"></script>
</body>
</html>
```

index.html:

```html
{% extends "./layout.html" %}

{% block title %}{{ data.titulo }}{% endblock %}

{% block body %}

<h1>{{data.bienvenida}}</h1>
    {% if data.cursosCount > 0 %}
    <ul>
        {% for c in data.cursos %}
        <li>{{ c }}</li>
        {% endfor %} 
    </ul>
    {% else %}
    <h2>No hay cursos</h2>
    {% endif %}
{% endblock %}
```

# El objeto Request

El objeto de solicitud utilizado por defecto en Flask. En primer lugar tienes que importarlo desde el módulo flask:

```python
from flask import request
```

El método de solicitud actual está disponible utilizando el atributo method. Para acceder a los datos del formulario (datos transmitidos en una petición POST o PUT) se puede utilizar el atributo form. He aquí un ejemplo completo de los dos atributos mencionados anteriormente:

```python
@app.route('/login', methods=['POST', 'GET'])
def login():
    error = None
    if request.method == 'POST':
        if valid_login(request.form['username'],
                       request.form['password']):
            return log_the_user_in(request.form['username'])
        else:
            error = 'Invalid username/password'
    # the code below is executed if the request method
    # was GET or the credentials were invalid
    return render_template('login.html', error=error)
```

# Carga de archivos

Puedes manejar los archivos subidos con Flask fácilmente. Sólo asegúrese de no olvidar establecer el atributo enctype="multipart/form-data" en su formulario HTML, de lo contrario el navegador no transmitirá sus archivos en absoluto.Los archivos subidos se almacenan en la memoria o en una ubicación temporal en el sistema de archivos. Puede acceder a esos archivos mirando el atributo files del objeto request. Cada archivo subido se almacena en ese diccionario. Se comporta como un objeto file estándar de Python, pero también tiene un método save() que permite almacenar ese fichero en el sistema de ficheros del servidor. Aquí hay un ejemplo sencillo que muestra cómo funciona:

```python
from flask import request

@app.route('/upload', methods=['GET', 'POST'])def upload_file():if request.method == 'POST':f = request.files['the_file']f.save('/var/www/uploads/uploaded_file.txt')...

```

Si quieres saber cómo se nombró el archivo en el cliente antes de subirlo a tu aplicación, puedes acceder al atributo filename. Sin embargo, ten en cuenta que este valor puede ser falsificado, así que nunca confíes en él. Si quieres utilizar el nombre de archivo del cliente para almacenar el archivo en el servidor, pásalo a través de la función secure_filename() que te proporciona Werkzeug:

```python
from werkzeug.utils import secure_filename

@app.route('/upload', methods=['GET', 'POST'])
def upload_file():
    if request.method == 'POST':
        file = request.files['the_file']
        file.save(f"/var/www/uploads/{secure_filename(file.filename)}")
    ...
```

# Cookies

Para acceder a las cookies se puede utilizar el atributo cookies. Para establecer las cookies se puede utilizar el método set_cookie de los objetos de respuesta. El atributo cookies de los objetos request es un diccionario con todas las cookies que transmite el cliente. Si quieres utilizar sesiones, no utilices las cookies directamente, sino que utiliza la Sesiones de Flask que añade algo de seguridad sobre las cookies por ti.Leyendo cookies:

```python
from flask import request

@app.route('/')
def index():
    username = request.cookies.get('username')
    # use cookies.get(key) instead of cookies[key] to not get a
    # KeyError if the cookie is missing.
```

Almacenando cookies:

```python
from flask import make_response

@app.route('/')
def index():
    resp = make_response(render_template(...))
    resp.set_cookie('username', 'the username')
    return resp
```

# Redirecciones y errores

Para redirigir a un usuario a otro endpoint, utilice la función redirect(); para abortar una solicitud antes de tiempo con un código de error, utilice la función abort():

```python
from flask import abort, redirect, url_for

@app.route('/')
def index():
    return redirect(url_for('login'))

@app.route('/login')
def login():
    abort(401)
    this_is_never_executed()
```

Este es un ejemplo bastante inútil porque un usuario será redirigido desde el índice a una página a la que no puede acceder (401 significa acceso denegado) pero muestra cómo funciona.

Por defecto se muestra una página de error en blanco y negro para cada código de error. Si quieres personalizar la página de error, puedes usar el decorador errorhandler():

```python
from flask import render_template

@app.errorhandler(404)
def page_not_found(error):
    return render_template('page_not_found.html'), 404
```

Observa el 404 después de la llamada render_template(). Esto le dice a Flask que el código de estado de esa página debe ser 404 que significa no encontrado. Por defecto se asume 200 que se traduce en: todo ha ido bien.

# APIs con JSON

Un formato de respuesta común al escribir una API es JSON. Es fácil empezar a escribir una API de este tipo con Flask.

```python
@app.route("/me")
def me_api():
    user = get_current_user()
    return {
        "username": user.username,
        "theme": user.theme,
        "image": url_for("user_image", filename=user.image),
    }

@app.route("/users")
def users_api():
    users = get_all_users()
    return [user.to_json() for user in users]
```

Este es un atajo para pasar los datos a la función jsonify(), que serializará cualquier tipo de datos JSON soportado. Esto significa que todos los datos en el dict o la lista deben ser JSON serializable.

# Objetos especiales

```python
import sqlite3
from datetime import datetime

import click
from flask import current_app, g


def get_db():
    if 'db' not in g:
        g.db = sqlite3.connect(
            current_app.config['DATABASE'],
            detect_types=sqlite3.PARSE_DECLTYPES
        )
        g.db.row_factory = sqlite3.Row

    return g.db


def close_db(e=None):
    db = g.pop('db', None)

    if db is not None:
        db.close()
```

## g

g es un objeto especial que es único para cada solicitud. Se utiliza para almacenar datos a los que podrían acceder varias funciones durante la petición. La conexión se almacena y se reutiliza en lugar de crear una nueva conexión si se llama a get_db por segunda vez en la misma petición. Basicamente se asegura de que solo haya una conexión activa por request.

## current_app

current_app es otro objeto especial que apunta a la aplicación Flask que maneja la solicitud. Como has utilizado una fábrica de aplicaciones, no hay ningún objeto de aplicación cuando escribes el resto de tu código. La llamada a get_db se realizará cuando la aplicación haya sido creada y esté gestionando una petición, por lo que se puede utilizar current_app.

# Static

Aqui van los archivos estaticos del proyecto como ser archivos css o javascript.

# 404 Not Found

Podemos renderizar una template para las paginas que no existen.

```python
def pagina_no_encontrada(error):
    return render_template("404.html"), 404

if __name__ == "__main__":
    app.register_error_handler(404, pagina_no_encontrada)
    app.run(debug=True, port=5000)
```

Tambien podemos redireccionar a otra vista:

```python
def pagina_no_encontrada(error):
    return redirect(url_for("index"))

if __name__ == "__main__":
    app.register_error_handler(404, pagina_no_encontrada)
    app.run(debug=True, port=5000)
```

# Before y After Request

Estos decoradores nos permiten ejecutar codigo antes y despues de una peticion.

```python
@app.before_request
def before_request():
    print("Antes de la peticion")

@app.after_request
def after_request(response):
    print("Despues de la peticion")
    return response 
```
