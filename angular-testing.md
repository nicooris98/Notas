---
id: 5mr60u2lgf604i2aqvzezz7
title: Angular Testing
desc: ''
updated: 1697480288276
created: 1697223505357
---
# Testing
Escribir codigo que va a probar nuestro codigo.
# ¿Cuánto de nuestro código debe ser probado?
No tiene sentido probar todo. Por ej no tiene sentido probar código que esta hardcodeado. El código que debe probarse es aquel codigo que va a interactuar o repercutir de alguna manera con lo que estamos realizando, por ej probar variables que cambian y ve el usuario.
# ¿Qué probar?
Solo debemos probar las partes del código de las que tengamos control. Un servicio que hace una llamada http no deberia ser probada puesto que es algo externo qué puede llegar a cambiar y del cual no tenemos control. Por ejemplo, si probamos y descubrimos un error porque el servidor esta apagado seria un mal testing puesto que daria un falso positivo ya que el error es del servidor. En este caso hay que mockear(hardcodear) la respuesta.
# Comando
El comando para ejecutar las pruebas es el siguiente:
```bash
ng test
```
# Nombre de archivo
Por convencion los archivos de prueba se llaman igual que el archivo que queremos probar pero terminando en `.spec.ts` para asi ser reconocido por el paquete de herramientas de testing de angular.
# Archivo de pruebas
Todas las pruebas estan dentros de un bloque dentro de un `describe(par1, par2)`, es una función la cual recibe 2 parametros, el primero es una descripción(string) y el segundo una funcion que va a hacer las definciones.
## Parametros
La descripción hace referencia al nombre del test. Dentro de la función(2do parametro) van a estar todas las pruebas.
## Prueba
Las prueba es una función llamada `it(par1, par2)` que recibe 2 parametros.
### Parametros de la prueba
El primero es una descripción y el segundo una función. La descripción del it es lo que esperas que haga la prueba.
Dentro de la función haremos nuestra prueba.
### Función expect()
Es una función de testing que recibe como parametro lo que queremos evaluar. Luego hay que invocar algun metodo que nos sirva para evaluar como por ejemplo `toBe()`, `toBeFalse()`, etc. Una prueba puede tener más de un expect().
```typescript
expect(res).toBe(30)
```
### Objetos Globales
Para no crear una instancia de una clase en cada una de las pruebas podes tener objetos globales. Esto se define dentro de la función `beforeEach()` pero antes de las pruebas `it`.
```typescript
describe("Basic 2 - Classes", () => {

  let player: Player

  beforeEach(() => {
    player = new Player()
  })

  it("Should be return 2000LP if the player takes 2000 of damage", () => {
    const res = player.takeDamage(2000)
    expect(res).toBe(2000)
  })
})
```
En este ejemplo player el objeto `player` seria instanciado antes de cada una de las pruebas por lo que siempre seria el mismo objeto y no habria necesidad de definir más dentro de las pruebas.
### Silenciar Pruebas
Si queremos silenciar una prueba basta con cambiar la funcion `it` por `xit` y si queremos silenciar todo el archivo hay que cambiar `describe` por `xdescribe`
# Test de Componentes
Primero necesitamos tener un componente. Si ejecutamos el comando `ng g c nombre-componente` nos creara el componente y ademas el archivo de pruebas.
## Archivo de Pruebas
Dentro del beforeEach necesitamos tener la declaración del componente y hacer la instanciacion del componente para poder probarlo.
```typescript
  let component: QuestionComponent;
  let fixture: ComponentFixture<QuestionComponent>;

  beforeEach(() => {
    TestBed.configureTestingModule({
      declarations: [QuestionComponent]
    });
    fixture = TestBed.createComponent(QuestionComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });
```
TestBed un pseudo modulo que tendra la declaración del componente.
Fixture es el contenedor del componente.
### Standalone Components
Si se usan standalone components entonces no hace falta tener las declarations por lo que el beforeEach queda de la siguiente forma:
```typescript
  beforeEach(() => {
    fixture = TestBed.createComponent(QuestionComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });
```
### Prueba componente
El archivo que genera angular siempre tiene la prueba "should create" y evalua que el componente esta siendo ejecutado de manera correcta.
```typescript
it('should create', () => {
    expect(component).toBeTruthy();
  });
```
## Test de Componentes con Dependencias
Cuando tenemos componentes con dependencias lo que debemos hacer es hacer los imports necesarios y en los providers poner las dependencias que necesita el componente. No pondremos exactamente el servicio que usa sino que lo haremos de la siguiente forma:
```typescript
  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [HttpClientTestingModule],
      providers: providers: [
        { provide: GithubUserService, useClass: GithubUserServiceMock }
      ]
    }).compileComponents()
    fixture = TestBed.createComponent(GithubUserComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });
```
### Mockear Respuesta del Servicio
Tendremos que "falsear" la respuesta del servicio para probar nuestro componente. Para ello tendremos que crear un archivo llamado como el servicio pero terminado en `.mock.ts`. Dentro del archivo creado tendremos que crear una clase con el mismo nombre que el servicio pero agregando la terminación "Mock". Tambien hay que crear las funciones que se van a probar y retornar los datos mockeados.
```typescript
export class GithubUserServiceMock {
  getUser(username: string) {
    return of({
      "name": null,
      "login": "nicooris98",
      "photo": "https://avatars.githubusercontent.com/u/50457912?v=4",
      "location": null,
      "followers": 2
    })
  }
}
```

# Test de Servicios
Es parecido a testear componentes pero cuando se trate de cosas externas, como peticiones, hay que usar otros modulos.
El objetivo de probar un servicio no es probar si el servidor esta devolviendo las respuestas o no, sino probar lo que sucede despues. El test no probara al servidor sino la logica que se haga dentro del servicio, por eso se mockea la respuesta del servidor.
## Variable Goblal
Hay que agregar una variable para el http e iniciarlo en el beforeEach. En el afterEach hacemos el verify por si existen peticiones asi las liberamos.
```typescript
describe('GithubUserService', () => {
  let service: GithubUserService;
  let httpMock: HttpTestingController

  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [HttpClientTestingModule]
    });
    service = TestBed.inject(GithubUserService);
    httpMock = TestBed.inject(HttpTestingController)
  });

  afterEach(() => {
    httpMock.verify()
  })

  it('should be created', () => {
    expect(service).toBeTruthy();
  });
});
```
## HttpClientTestingModule
Cuando se trabaja con peticiones http lo que se debe hacer es importar el `HttpClientTestingModule` para mockear las peticiones.
```typescript
  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [HttpClientTestingModule]
    });
    service = TestBed.inject(GithubUserService);
  });
```
## Prueba de un servicio
Para probar el servicio debemos "falsear" la respuesta del servidor. Lo que se debe hacer es llamar al metodo que queremos probar del servicio. Para que el subscribe se ejecute y obtengamos una respuesta debemos crear la request falsa con `httpMock.expectOne` y luego con el metodo `flush` pasar la respuesta falsa del servidor al subscribe. El servicio trabajara con la respuesta falsa y dentro del subscribe podemos comparar los objetos con la función `expect`.
```typescript
  it("should be return Github User infor of @nicooris98", () => {
    const expectedResult = {
      "name": null,
      "login": "nicooris98",
      "photo": "https://avatars.githubusercontent.com/u/50457912?v=4",
      "location": null,
      "followers": 2
    }

    const mockServerResponse = {
      "login": "nicooris98",
      "id": 50457912,
      "node_id": "MDQ6VXNlcjUwNDU3OTEy",
      "avatar_url": "https://avatars.githubusercontent.com/u/50457912?v=4",
      "gravatar_id": "",
      "url": "https://api.github.com/users/nicooris98",
      "html_url": "https://github.com/nicooris98",
      "followers_url": "https://api.github.com/users/nicooris98/followers",
      "following_url": "https://api.github.com/users/nicooris98/following{/other_user}",
      "gists_url": "https://api.github.com/users/nicooris98/gists{/gist_id}",
      "starred_url": "https://api.github.com/users/nicooris98/starred{/owner}{/repo}",
      "subscriptions_url": "https://api.github.com/users/nicooris98/subscriptions",
      "organizations_url": "https://api.github.com/users/nicooris98/orgs",
      "repos_url": "https://api.github.com/users/nicooris98/repos",
      "events_url": "https://api.github.com/users/nicooris98/events{/privacy}",
      "received_events_url": "https://api.github.com/users/nicooris98/received_events",
      "type": "User",
      "site_admin": false,
      "name": null,
      "company": null,
      "blog": "",
      "location": null,
      "email": null,
      "hireable": null,
      "bio": null,
      "twitter_username": null,
      "public_repos": 16,
      "public_gists": 0,
      "followers": 2,
      "following": 1,
      "created_at": "2019-05-09T16:05:14Z",
      "updated_at": "2023-10-10T16:24:10Z"
    }

    service.getUser("nicooris98").subscribe((res) => {
      expect(res).toEqual(expectedResult)
    })

    const req = httpMock.expectOne("https://api.github.com/users/nicooris98")

    req.flush(mockServerResponse)
  })
```