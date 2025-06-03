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

# Parámetros de ruta
Para los parámetros de ruta hay que añadirlos a la ruta con llaves{} y luego agregar la misma variable como argumento de la función especificando su tipo.
```python
@app.get("/movies/{id}", tags=["Home"])
def getm_movie(id: int):
    for movie in movies:
        if movie["id"] == id:
            return movie
    return []
```

# Parámetros query
Para los parámetros query solo hay que agregar la variable como argumento de la función.
```python
@app.get("/movies/", tags=["Home"])
def getm_movie_by_category(category: str, year: int):
    for movie in movies:
        if movie["category"] == category:
            return movie
    return []
```

# Request Body
```python
@app.post("/movies", tags=["Movies"])
def create_movie(id: int = Body(), title: str = Body(), description: str = Body(), year: int = Body(), category: str = Body()):
    new_movie = {
        "id": id,
        "title": title,
        "description": description,
        "year": year,
        "category": category
    }
    movies.append(new_movie)
    return new_movie
```

# Entidades
Para las entidades hay que usar `pydantic`. `Union` es para definir que una propiedad puede tener 2 valores y el = nos da el valor por defecto.
## Básico
```python
from typing import Union
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    price: float
    is_offer: Union[bool, None] = None
```

## Clase Base
Es recomendable tener una clase base con las columnas que son comunes.
```python
from datetime import datetime, timezone
from sqlmodel import SQLModel, Field
from typing import Optional

class TimestampMixin(SQLModel):
    created_at: datetime = Field(default_factory=lambda: datetime.now(timezone.utc))
    updated_at: datetime = Field(default_factory=lambda: datetime.now(timezone.utc))
    deleted_at: Optional[datetime] = None
```
Luego se usa en otra clase.
```python
from sqlmodel import SQLModel, Field
import uuid
from .base import TimestampMixin

class User(TimestampMixin, SQLModel, table=True):
    __tablename__ = 'users'
    id: uuid.UUID = Field(default_factory=uuid.uuid4, primary_key=True)
    name: str
    lastname: str
    email: str = Field(unique=True)
    password: str
```

## Relaciones
```python
from sqlmodel import SQLModel, Field
import uuid
from .base import TimestampMixin

class ProjectResource(TimestampMixin, SQLModel, table=True):
    __tablename__ = 'project_resources'
    id: uuid.UUID = Field(default_factory=uuid.uuid4, primary_key=True)
    project_id: uuid.UUID = Field(foreign_key="projects.id")
    link_id: uuid.UUID = Field(foreign_key="project_links.id")
```

## Enums
Para agregar enums debemos definir una clase y pasarle el tipo seguido de la clase Enum.
Luego en la clase donde se va a usar no basta con poner la columna del tipo del enum sino que hay que agregar `= Field(default=ProjectStatus.in_progress, sa_column_kwargs={"nullable": False})`.
Tambien es necesario agregar la parte del `model_config`.
```python
from datetime import datetime
from enum import Enum
from sqlmodel import SQLModel, Field
import uuid
from .base import TimestampMixin
from typing import Optional

class ProjectStatus(str, Enum):
    in_progress = "in_progress"
    finished = "finished"

class Project(TimestampMixin, SQLModel, table=True):
    __tablename__ = 'projects'
    id: uuid.UUID = Field(default_factory=uuid.uuid4, primary_key=True)
    title: str
    owner_id: uuid.UUID = Field(foreign_key="users.id")
    client_id: Optional[uuid.UUID] = Field(default=None, foreign_key="clients.id")
    start_date: Optional[datetime] = None
    end_date: Optional[datetime] = None
    status: ProjectStatus = Field(sa_column_kwargs={"nullable": False})

    model_config = {
        "arbitrary_types_allowed": True
    }
```