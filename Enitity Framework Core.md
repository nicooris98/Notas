# Creación del Proyecto
Escribir web api en el buscador y elegir ASP.NET Core Web API ya que se prescindirá de interfaz de usuario. Se usara SQL Server para este proyecto pero se pueden usar otros motores de base de datos.
# Paquetes Nuget
Primero hay que agregar los siguientes paquetes `Microsoft.EntityFrameworkCore.Tools` y `Microsoft.EntityFrameworkCore.SqlServer`
# Contexto
Crearemos la clase del contexto la cual heredara de DbContext. El DbContext es la pieza central de Entity Framework Core ya que aqui se configura todo, por ejemplo se configura que tablas se crearan y a partir de que clases.
## Constructor
En el constructor tenemos que pasar como parámetro unas opciones que son del tipo DbContextOptions, en estas opciones se mandaran configuraciones como ser el `ConnectionStrings` para indicar a qué base de datos de qué servidor me voy a conectar.
```c#
using Microsoft.EntityFrameworkCore;

namespace IntroduccionEAFCore
{
    public class ApplicationDbContext: DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options) : base(options) { }
    }
}
```
## Inyectar Contexto
Hay que configurar el contexto en la inyección de dependencias de .net core. Para eso hay que ir a `Program.cs` y arriba de donde se defina app se puede configurar los distintos servicios de la aplicacion. Alli hay que agregar el contexto e indicar a las opciones que motor de base de datos se usara(SqlServer en este caso) y hay que pasarle como parametro el `ConnectionStrings` pero es mejor usar un proveedor de configuracion para pasar ese parametro y no hardcodearlo. El proveedor de configuracion que se usa en .net core es un archivo json que se crea con el proyecto. Existen 2 archivos: el `appsettings.json` y el `appsettings.Development.json`. Para desarrollo se usa el development y el otro para producción.
### Program.cs
```c#
builder.Services.AddDbContext<ApplicationDbContext>(opciones =>
    opciones.UseSqlServer("name=DefaultConnection"));

var app = builder.Build();
```
### appsettings.Development.json
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=DESKTOP-NDERDM3\\SQLEXPRESS;Database=TestEFCore;Integrated Security=True;TrustServerCertificate=True"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  }
}
```
# Entidades
Una entidad es una clase que representa una tabla en nuestra base de datos. Dentro de la clase hay que definir las propiedades que seran las columnas de la tabla. Para que esta clase sea una entidad hay que configurarla dentro del DbContext que creamos antes(`ApplicationDbContext`). 
## DbSet
Para eso se usa `DbSet` que es la clase que permite configurar otras clases como entidades. Luego en el tipo, que estara encerrado entre <>, ira la clase que se creo y al lado ira el nombre que tendra la tabla en la base de datos. Despues se pone una flecha => y Set con el mismo tipo encerrado entre <>. Y con eso configuro la clase como una entidad.
### Clase
El = null! es para indicar que si tendra un valor. Existen 3 formas de configurar entidades: Anotación de Datos, Convención y API Fluente. Cuando no puedo configurar por anotacion o convencion tengo que hacerlo por API Fluent dentro del Contexto.
#### Anotación de Datos
Si queremos ser explicitos podemos agregar una anotación de datos '[Key]' en la linea de arriba de la columna que queremos que sea nuestra PK. Si queremos definir los caracteres de una columna tipo string podemos usar '[StringLength]' .
#### Convención
Por convención, cuando detecta que un campo se llama Id se lo configura como PK. La configuracion por convención tiene sus limitantes, por ejemplo no puedo definir que el campo Name solo tenga 150 caracteres.
```c#
public class Gender
    {
	    //[Key]
        public int Id { get; set; }
        [StringLength(maximumLength: 150)]
        public string Name { get; set; } = null!;
    }
```

#### [[Enitity Framework Core#Fluent API|Fluent API]]
### Contexto
```c#
public class ApplicationDbContext: DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options) : base(options) { }

        public DbSet<Gender> Genders => Set<Gender>();
    }
```
# Migrations
Una migracion nos permite expresar en codigo los cambios que se van a hacer en nuestra base de datos. Las migraciones son clases que se van a crear cuando ejecutemos un comando. El conjunto de migraciones servira como un log para ver los cambios de la base de datos a lo largo del tiempo. Cada vez que se hace un cambio en las entidades hay que volver a generar una migración.
## Comandos
Para crear una migracion hay que ir al `Package Manager Console` y ejecutar el siguiente comando seguido del nombre que le queremos dar a la migración:
```bash
Add-Migration Inicial
```
Este comando crea una clase que tiene 2 metodos: `Up()` y `Down()`. En el metodo `Up()` se indica que es lo que hace la migracion cuando se aplique. El metodo `Down()` sirve para deshacer la migracion, lo que hace es deshacer lo que se hizo en el metodo `Up()`.
En la carpeta Migrations se guardan todas las migraciones.
Para actualizar la base de datos con las migraciones pendientes hay que ejecutar:
```bash
update-database
```
Para remover una migración el comando es:
```bash
remove-migration
```
## Base de Datos
Si vamos a la base de datos vemos que creo las tablas. Si la base de datos no existia entonces tambien la hubiera creado. Tambien crea una tabla llamada `__EFMigrationsHistory` que es un registro de las migraciones aplicadas.
# Fluent API
Son un conjunto de metodos que podemos utilizar para configurar las propiedades y relaciones de Entity Framework Core en un solo lugar fuera de la entidad. El API Fluente es la herramienta de configuracion mas poderosa de Entitiy Framework Core y hay cosas que solo se pueden configurar a traves de ella. Para hacer uso de API fluente debemos ir al contexto y sobreescribir los metodos pero hay que dejar que ejecute el metodo base porque sino puede dar error. 
## OnModelCreating
```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
	{
		base.OnModelCreating(modelBuilder);

		//modelBuilder.Entity<Gender>().HasKey(g =>  g.Id);
		modelBuilder.Entity<Gender>().Property(g => g.Name).HasMaxLength(150);
		modelBuilder.Entity<Actor>().Property(a => a.Name).HasMaxLength(150);
	            modelBuilder.Entity<Actor>().Property(a => a.BirthDate).HasColumnType("date");
	    modelBuilder.Entity<Actor>().Property(a => a.Fortune).HasPrecision(18, 2);
	
	    modelBuilder.Entity<Movie>().Property(a => a.Title).HasMaxLength(150);
	    modelBuilder.Entity<Movie>().Property(a => a.ReleaseDate).HasColumnType("date");
	}
```
## ConfigureConventions
Para no repetir un mismo valor podemos configurarlo y que quede como convención.
```c#
protected override void ConfigureConventions(ModelConfigurationBuilder configurationBuilder)
	{
		base.ConfigureConventions(configurationBuilder);

		configurationBuilder.Properties<string>().HaveMaxLength(150);
	}
```
# Configuración de Entidades
Para que el contexto no tenga tantas lineas se pueden crear clases que configuren las propiedades de las entidades. Hay que crear una carpeta dentro de Entities que se llamara Configurations.
```c#
public class ActorConfig : IEntityTypeConfiguration<Actor>
{
	public void Configure(EntityTypeBuilder<Actor> builder)
	{
		builder.Property(a => a.BirthDate).HasColumnType("date");
		builder.Property(a => a.Fortune).HasPrecision(18, 2);
	}
}
```
Ahora lo que hay que hacer es decir en el contexto como aplicar esas configuraciones.
```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
	{
		base.OnModelCreating(modelBuilder);

		modelBuilder.ApplyConfigurationsFromAssembly(Assembly.GetExecutingAssembly());
	}
```
Con `ApplyConfigurationsFromAssembly` aplicamos las configuraciones. Assembly hace referencia al proyecto en el que estamos y esa linea de codigo busca todas las clases que implementen la interfaz `IEntityTypeConfiguration` y aplica las configuraciones que se definieron en esas clases.
# Relaciones entre Entidades
Para configurar relaciones podemos utilizar la configuracion por convencion.
## Propiedades de Navegación
Una propiedad de navegación nos permite obtener la información relacionada a una entidad de forma sencilla. Necesito 2 propiedades: una es la clave foranea y la otra es la propiedad de navegacion. Por convención, al tener una propiedad llamada igual que la clase y otra con el nombre de la clase pero terminado en Id se entiende que se configurara como una FK.
### Relación 1 a N
```c#
public class Comment
{
	public int Id { get; set; }
	public string? Content { get; set; }
	public bool Recommend { get; set; }
	public int MovieId { get; set; }//FK
	public Movie Movie { get; set; } = null!;//Propiedad de Navegacion
}
public class Movie
{
	public int Id { get; set; }
	public string Title { get; set; }
	public bool IsReleased { get; set; }
	public DateTime ReleaseDate { get; set; }
	public HashSet<Comment> Comments { get; set; } = new HashSet<Comment>();//Propiedad de Navegacion
}
```
El tipo de dato `HashSet` indica que es una coleccion. Utilizamos este tipo de dato porque es mas rapido para trabajar con colecciones. El inconveniente de este tipo de dato es que no ordena asi que si necesitas ordenar te conviene utilizar un listado y no `HashSet`.
### Relación N a N con Salto de Navegación
No se utiliza una entidad intermedia pero esto no quiere decir que no se relacionaran por medio de una tabla. La tabla intermedia sera creada en la base de datos pero no se creara ninguna entidad. La desventaja es que no podemos controlar, de forma sencilla, esa tabla y si queremos agregarle campos a esa tabla intermedia se complica.
```c#
public class Gender
{
	public int Id { get; set; }
	[StringLength(maximumLength: 150)]
	public string Name { get; set; } = null!;
	public HashSet<Movie> Movies { get; set; } = new HashSet<Movie>();//Propiedad de Navegacion
}
public class Movie
{
	public int Id { get; set; }
	public string Title { get; set; }
	public bool IsReleased { get; set; }
	public DateTime ReleaseDate { get; set; }
	public HashSet<Comment> Comments { get; set; } = new HashSet<Comment>();
	public HashSet<Gender> Genders{ get; set; } = new HashSet<Gender>();//Propiedad de Navegacion
}
```
Si ejecutamos el comando para crear una entidad vemos que la tabla se creara en la base de datos, lo que no habra es una entidad.
```c#
protected override void Up(MigrationBuilder migrationBuilder)
	{
		migrationBuilder.CreateTable(
			name: "GenderMovie",
			columns: table => new
			{
				GendersId = table.Column<int>(type: "int", nullable: false),
				MoviesId = table.Column<int>(type: "int", nullable: false)
			},
			constraints: table =>
			{
				table.PrimaryKey("PK_GenderMovie", x => new { x.GendersId, x.MoviesId });
				table.ForeignKey(
					name: "FK_GenderMovie_Genders_GendersId",
					column: x => x.GendersId,
					principalTable: "Genders",
					principalColumn: "Id",
					onDelete: ReferentialAction.Cascade);
				table.ForeignKey(
					name: "FK_GenderMovie_Movies_MoviesId",
					column: x => x.MoviesId,
					principalTable: "Movies",
					principalColumn: "Id",
					onDelete: ReferentialAction.Cascade);
			});

		migrationBuilder.CreateIndex(
			name: "IX_GenderMovie_MoviesId",
			table: "GenderMovie",
			column: "MoviesId");
	}
```
### Relación N a N sin Salto de Navegación
En este caso si se crea la entidad intermedia que relaciona las entidades.
```c#
public class ActorMovie
{
	public int MovieId { get; set; }
	public Movie Movie { get; set; } = null!;
	public int ActorId { get; set; }
	public Actor Actor { get; set; } = null!;
	public string Personaje { get; set; } = null!;
	public int Order { get; set; }

}
public class Actor
{
	public int Id { get; set; }
	public string Name { get; set; } = null!;
	public decimal Fortune { get; set; }
	public DateTime BirthDate { get; set; }
	public List<ActorMovie> ActorMovies = new List<ActorMovie>();//Relacion
}
public class Movie
{
	public int Id { get; set; }
	public string Title { get; set; }
	public bool IsReleased { get; set; }
	public DateTime ReleaseDate { get; set; }
	public HashSet<Comment> Comments { get; set; } = new HashSet<Comment>();
	public HashSet<Gender> Genders{ get; set; } = new HashSet<Gender>();
	public List<ActorMovie> ActorMovies = new List<ActorMovie>();//Relacion
}
```
En este caso se crea una llave compuesta, no es necesario hacerlo de esa forma pero se puede.
```c#
public class ActorMovieConfig : IEntityTypeConfiguration<ActorMovie>
{
	public void Configure(EntityTypeBuilder<ActorMovie> builder)
	{
		builder.HasKey(am => new { am.ActorId, am.MovieId });
	}
}
```
# Insertar Registros
Hay que crear los controladores para las entidades dentro de la carpeta Controllers. Los controladores son clases que se encargan de recibir peticiones HTTP. Debemos tener como propiedad readonly el contexto e instanciarlo en el constructor. Cuando trabajamos con peticiones es buena practica hacerlo de forma asincrona. `context.Add(object)` no agrega el objecto a la base de datos sino que marca el objeto con la intencion de agregarlo, la insercion se realizara cuando guardemos los cambios y el guardado se realiza con `context.SaveChangesAsync()`.
```c#
[HttpPost]
public async Task<ActionResult> Post(Gender gender)
{
	this.context.Add(gender);
	await this.context.SaveChangesAsync();
	return Ok();
}
```
Si corremos la aplicación vemos que se abre el navegador con Swagger y podemos probar el api. Cuando le damos a try out y vemos muchos campos es porque Swagger trata de predecir que es lo que necesitamos colocar, basicamente representa el grafo de objetos. Si quieres que no salga toda esa información puedes cambiar el parametro del metodo en el controlador por un DTO. DTO es Data Transfer Object u Objeto de Transferencia de Datos, son clases que sirven para guardar la informacion que vamos a pasar de un lado a otro y nos permite controlar la estructura de lo que vamos a enviar y recibir de nuestros metodos, ademas de esconder la entidad del mundo exterior. Si usamos un DTO no podemos pasar el objeto directamente al método `Add()` puesto que no es una entidad por lo que hay que mapearlo antes.
```c#
[HttpPost]
public async Task<ActionResult> Post(GenderDTO genderDTO)
{
	var gender = new Gender
	{
		Name = genderDTO.Name
	};

	this.context.Add(gender);
	await this.context.SaveChangesAsync();
	return Ok();
}
```
El mapeo manual a veces no es tan facil por la cantidad de datos que hay que setear. Se puede utilizar un paquete llamado `AutoMapper.Extensions.Microsoft.DependencyInjection`. Luego creamos una carpeta Utils y dentro agregamos una clase llamada `AutoMapperProfiles` que exitenda de `Profile`.
```c#
public class AutoMapperProfiles: Profile
{
	public AutoMapperProfiles()
	{
		CreateMap<GenderDTO, Gender>();
	}
}
```
Despues hay que ir a la clase `Program.cs` para configurar el auto mapper. Esa linea le dice que queremos tomar las configuraciones de auto mapper que coloquemos en este proyecto.
```c#
builder.Services.AddAutoMapper(typeof(Program));

var app = builder.Build();
```
Luego hay que agregar la propiedad y asignarla en el constructor del controlador.
```c#
[ApiController]
[Route("api/genders")]
public class GenderController: ControllerBase
{
	private readonly ApplicationDbContext context;
	private readonly IMapper mapper;

	public GenderController(ApplicationDbContext context, IMapper mapper)
	{
		this.context = context;
		this.mapper = mapper;
	}

	[HttpPost]
	public async Task<ActionResult> Post(GenderDTO genderDTO)
	{
		var gender = this.mapper.Map<Gender>(genderDTO);

		this.context.Add(gender);
		await this.context.SaveChangesAsync();
		return Ok();
	}
}
```
## Múltiples Registros
```c#
[HttpPost("multiple")]
public async Task<ActionResult> Post(GenderDTO[] genderDTO)
{
	var genders = this.mapper.Map<Gender[]>(genderDTO);

	this.context.AddRange(genders);
	await this.context.SaveChangesAsync();
	return Ok();
}
```