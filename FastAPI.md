# ¿ Qué es?
Es un framework para la construcción de APIs con Python.

# Dependencias
Hay que instalar `fastapi` y `uvicorn`.

# Aplicación básica
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def home():
    return "hola mundo"
```

Para correr esta aplicación es con el comando `uvicorn` seguido de el nombre del archivo y luego el nombre de la variable que creamos con `FastAPI()`.
También podemos especificar el puerto, si no lo hacemos el puerto por defecto es el 8000.
El reload sirve para detectar cambios en los archivos, es útil en desarrollo.
El host 0.0.0.0 sirve para que cualquier dispositivo conectado a la misma red pueda acceder.
```bash
uvicorn main:app --port 5000 --reload --host 0.0.0.0
```

# Documentación automática
FastAPI viene con una documentación de endpoints automatica basada en Swagger.
Para acceder debemos ir a la ruta `/docs`.
Podemos cambiar el nombre y version usando el objeto app.
Tambien podemos agregar tags para que los endpoints se agrupen bajo el mismo tag.
```python
app = FastAPI()

app.title = "Mi primer aplicación con FastAPI"
app.version = "2.0.0"

@app.get("/", tags=["Home"])
def index():
    return "home"

@app.get("/hello", tags=["Home"])
def hello():
    return "hello"
```

Alternativamente podemos ir a otra documentación usando `redoc` pero esta no esta basada en Swagger.