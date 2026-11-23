# Módulo 7: Uso de Entity Framework Core en ASP.NET Core

# Laboratorio: Uso de Entity Framework Core en ASP.NET Core

## Escenario

Su empresa planea escribir una aplicación web para administrar cupcakes y pastelerías. Para conectar la aplicación a una base de datos, su equipo de desarrollo ha decidido usar Entity Framework Core. Se le ha pedido crear una clase que derive de `DbContext` y, después, usarla para recuperar datos de la base de datos y almacenarlos en ella. La aplicación permitirá a los usuarios almacenar cupcakes cargados, editar sus propiedades, ver sus detalles y eliminarlos.

## Ejercicio 1: Agregar Entity Framework Core

### Escenario

En este ejercicio, primero agregará el modelo `Cupcake` y el modelo `Bakery` a la aplicación web Cupcake. Después agregará a la aplicación web una clase de contexto de Entity Framework llamada `CupcakeContext`. A continuación, configurará la clase `CupcakeContext` para conectarse a una base de datos SQLite. Por último, usará la inicialización de datos para llenar la base de datos con datos de ejemplo cuando se cree.

Las tareas principales de este ejercicio son las siguientes:

- Crear clases de modelo.
- Crear una clase que derive de `DbContext`.
- Configurar Entity Framework Core para usar SQLite.
- Usar `OnModelCreating` para llenar la base de datos.

### Tarea 1: Crear clases de modelo

1. Vaya a `D:\Allfiles\Mod07\Labfiles\01_Cupcakes_begin` y haga doble clic en `Cupcakes.sln`.

   **Nota:** Si aparece el cuadro de diálogo `Security Warning for Cupcakes`, compruebe que la casilla `Ask me for every project in this solution` esté desmarcada y haga clic en `OK`.

2. En la ventana `Cupcakes - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Models`, abra `Cupcake.cs`.
3. En el bloque de código de `Cupcake.cs`, reemplace el código entre la llave de apertura `{` y la llave de cierre `}` por el siguiente:

   ```csharp
   [Key]
   public int CupcakeId { get; set; }

   [Required(ErrorMessage = "Please select a cupcake type")]
   [Display(Name = "Cupcake Type:")]
   public CupcakeType? CupcakeType { get; set; }

   [Required(ErrorMessage = "Please enter a cupcake description")]
   [Display(Name = "Description:")]
   public string Description { get; set; }

   [Display(Name = "Gluten Free:")]
   public bool GlutenFree { get; set; }

   [Range(1, 15)]
   [Required(ErrorMessage = "Please enter a cupcake price")]
   [DataType(DataType.Currency)]
   [Display(Name = "Price:")]
   public double? Price { get; set; }

   [NotMapped]
   [Display(Name = "Cupcake Picture:")]
   public IFormFile PhotoAvatar { get; set; }

   public string ImageName { get; set; }
   public byte[] PhotoFile { get; set; }
   public string ImageMimeType { get; set; }

   [Required(ErrorMessage = "Please select a bakery")]
   public int? BakeryId { get; set; }

   public virtual Bakery Bakery { get; set; }
   ```

4. En la ventana `Cupcakes - Microsoft Visual Studio`, en `Solution Explorer`, haga clic con el botón derecho en `Models` y, en el menú, elija `Add` y después `Class…`.
5. En el cuadro de diálogo `Add New Item`, establezca el campo `Name` en `Bakery.cs`.
6. En el bloque de código de `Bakery.cs`, reemplace el código entre la llave de apertura `{` y la llave de cierre `}` por el siguiente:

   ```csharp
   [Key]
   public int BakeryId { get; set; }

   [StringLength(50, MinimumLength = 4)]
   public string BakeryName { get; set; }

   [Range(1, 40)]
   public int Quantity { get; set; }

   [StringLength(50, MinimumLength = 4)]
   public string Address { get; set; }

   public virtual ICollection<Cupcake> Cupcakes { get; set; }
   ```

### Tarea 2: Crear una clase que derive de DbContext

1. En `Solution Explorer`, haga clic con el botón derecho en el proyecto `Cupcakes`, señale `Add` y haga clic en `New Folder`.
2. En el cuadro `NewFolder`, escriba `Data` y presione Entrar.
3. En `Solution Explorer`, haga clic con el botón derecho en `Data`, señale `Add` y haga clic en `Class…`.
4. En el cuadro de diálogo `Add New Item - Cupcakes`, en el cuadro `Name:`, escriba `CupcakeContext` y haga clic en `Add`.
5. En la ventana de código de `CupcakeContext.cs`, localice el siguiente código:

   ```csharp
   public class CupcakeContext
   ```

6. Agregue la clase base `DbContext` para que el código se vea de la siguiente manera:

   ```csharp
   public class CupcakeContext : DbContext
   ```

7. En el bloque de código de `CupcakeContext.cs`, coloque el cursor después de la segunda llave de apertura `{`, presione Entrar y escriba el siguiente código:

   ```csharp
   public CupcakeContext(DbContextOptions<CupcakeContext> options) : base(options)
   {
   }

   public DbSet<Cupcake> Cupcakes { get; set; }
   public DbSet<Bakery> Bakeries { get; set; }
   ```

   **Nota:** Visual Studio debería agregar automáticamente las instrucciones `using` de `Cupcakes.Models` y `Microsoft.EntityFrameworkCore`.

### Tarea 3: Configurar Entity Framework Core para usar SQLite

1. En la ventana `Cupcakes - Microsoft Visual Studio`, en `Solution Explorer`, haga clic con el botón derecho en `Cupcakes` y seleccione `Manage NuGet Packages`.
2. En la ventana `NuGet: Cupcakes`, haga clic en `Browse`.
3. En el cuadro `Search`, escriba `Microsoft.EntityFrameworkCore.Sqlite` y presione Entrar.
4. En la lista de resultados, elija `Microsoft.EntityFrameworkCore.Sqlite`; en el panel derecho, seleccione la última versión estable y haga clic en `Install`.
5. Si aparece el cuadro de diálogo `Preview Changes`, haga clic en `OK`.
6. Si aparece el cuadro de diálogo `License Acceptance`, haga clic en `I Accept`.
7. Cierre la ventana `NuGet: Cupcakes`.
8. En la ventana `Cupcakes - Microsoft Visual Studio`, en `Solution Explorer`, haga doble clic en `Program.cs`.
9. En la ventana de código de `Program.cs`, busque la línea:

   ```csharp
   var app = builder.Build();
   ```

10. Antes de esta línea, agregue el siguiente código:

    ```csharp
    builder.Services.AddDbContext<CupcakeContext>(options =>
        options.UseSqlite("Data Source=cupcake.db"));
    ```

11. En la ventana `Cupcakes - Microsoft Visual Studio`, en `Solution Explorer`, dentro de la carpeta `Data`, haga clic en `CupcakeContext.cs`.
12. En `CupcakeContext.cs`, dentro del cuerpo del constructor, agregue la instrucción `Database.EnsureCreated();` para que el constructor se vea de la siguiente manera:

    ```csharp
    public CupcakeContext(DbContextOptions<CupcakeContext> options) : base(options)
    {
        Database.EnsureCreated();
    }
    ```

    Esto creará automáticamente un archivo de base de datos la primera vez que se ejecute la aplicación.

### Tarea 4: Usar OnModelCreating para llenar la base de datos

1. En la ventana `Cupcakes - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Data`, haga clic en `CupcakeContext.cs`.
2. En la ventana de código de `CupcakeContext.cs`, localice el siguiente código:

   ```csharp
   public DbSet<Cupcake> Cupcakes { get; set; }
   public DbSet<Bakery> Bakeries { get; set; }
   ```

3. Coloque el cursor inmediatamente después de la última llave de cierre `}`, presione Entrar dos veces y escriba el siguiente código:

   ```csharp
   protected override void OnModelCreating(ModelBuilder modelBuilder)
   {
   }
   ```

4. En el bloque de código del método `OnModelCreating`, escriba el siguiente código:

   ```csharp
   modelBuilder.Entity<Bakery>().HasData(
   new Bakery
   {
       BakeryId = 1,
       BakeryName = "Gluteus Free",
       Address = "635 Brighton Circle Road",
       Quantity = 8
   },
   new Bakery
   {
       BakeryId = 2,
       BakeryName = "Cupcakes Break",
       Address = "4323 Jerome Avenue",
       Quantity = 22
   },
   new Bakery
   {
       BakeryId = 3,
       BakeryName = "Cupcakes Ahead",
       Address = "2553 Pin Oak Drive",
       Quantity = 18
   },
   new Bakery
   {
       BakeryId = 4,
       BakeryName = "Sugar",
       Address = "1608 Charles Street",
       Quantity = 30
   }
   );
   ```

5. En el bloque de código del método `OnModelCreating`, inmediatamente después del código que acaba de agregar, presione Entrar y escriba el siguiente código:

   ```csharp
   modelBuilder.Entity<Cupcake>().HasData(
       new Cupcake
       {
           CupcakeId = 1,
           CupcakeType = CupcakeType.Birthday,
           Description = "Vanilla cupcake with coconut cream",
           GlutenFree = true,
           Price = 2.5,
           BakeryId = 1,
           ImageMimeType = "image/jpeg",
           ImageName = "birthday-cupcake.jpg"
       },
       new Cupcake
       {
           CupcakeId = 2,
           CupcakeType = CupcakeType.Chocolate,
           Description = "Chocolate cupcake with caramel filling and chocolate butter cream",
           GlutenFree = false,
           Price = 3.2,
           BakeryId = 2,
           ImageMimeType = "image/jpeg",
           ImageName = "chocolate-cupcake.jpg"
       },
       new Cupcake
       {
           CupcakeId = 3,
           CupcakeType = CupcakeType.Strawberry,
           Description = "Chocolate cupcake with straberry cream filling",
           GlutenFree = false,
           Price = 4,
           BakeryId = 3,
           ImageMimeType = "image/jpeg",
           ImageName = "pink-cupcake.jpg"
       },
       new Cupcake
       {
           CupcakeId = 4,
           CupcakeType = CupcakeType.Turquoise,
           Description = "Vanilla cupcake with butter cream",
           GlutenFree = true,
           Price = 1.5,
           BakeryId = 4,
           ImageMimeType = "image/jpeg",
           ImageName = "turquoise-cupcake.jpg"
       }
   );
   ```

6. En Visual Studio, en el menú, elija `File` y después `Save All` para guardar los cambios.

**Resultados:** Después de completar este ejercicio, habrá agregado Entity Framework Core a la aplicación Cupcake Shop.

## Ejercicio 2: Usar Entity Framework Core para recuperar y almacenar datos

### Escenario

En este ejercicio, primero creará un repositorio para la aplicación web. El repositorio accederá a una base de datos SQLite mediante Entity Framework Core. Después usará la inyección de dependencias para inyectar el servicio en un controlador. Usará el repositorio en el controlador para acceder a la base de datos. En el controlador, recuperará los datos de los cupcakes y las pastelerías, y después manipulará los datos.

Las tareas principales de este ejercicio son las siguientes:

- Crear un repositorio.
- Actualizar un controlador para usar un repositorio.
- Usar Entity Framework Core para recuperar datos.
- Manipular datos mediante Entity Framework Core.
- Ejecutar la aplicación.

### Tarea 1: Crear un repositorio

1. En `Solution Explorer`, haga clic con el botón derecho en `Cupcakes`, señale `Add` y haga clic en `New Folder`.
2. En el cuadro `NewFolder`, escriba `Repositories` y presione Entrar.
3. En la ventana `Cupcakes - Microsoft Visual Studio`, en `Solution Explorer`, haga clic con el botón derecho en `Repositories`, señale `Add` y haga clic en `New Item…`.
4. En el cuadro de diálogo `Add New Item - Cupcakes`, seleccione `Interface`.
5. En el cuadro de texto `Name:`, escriba `ICupcakeRepository` y haga clic en `Add`.
6. En el bloque de código de `ICupcakeRepository.cs`, coloque el cursor después de la segunda llave de apertura `{`, presione Entrar y escriba el siguiente código:

   ```csharp
   IEnumerable<Cupcake> GetCupcakes();
   Cupcake GetCupcakeById(int id);
   void CreateCupcake(Cupcake cupcake);
   void DeleteCupcake(int id);
   void SaveChanges();
   IQueryable<Bakery> PopulateBakeriesDropDownList();
   ```

7. En la ventana `Cupcakes - Microsoft Visual Studio`, en `Solution Explorer`, haga clic con el botón derecho en `Repositories`, señale `Add` y haga clic en `Class…`.
8. En el cuadro de diálogo `Add New Item - Cupcakes`, en el cuadro `Name`, escriba `CupcakeRepository` y haga clic en `Add`.
9. En la ventana de código de `CupcakeRepository.cs`, localice el siguiente código:

   ```csharp
   public class CupcakeRepository
   ```

10. Agregue la interfaz `ICupcakeRepository` a la clase para que se vea como el siguiente código:

    ```csharp
    public class CupcakeRepository : ICupcakeRepository
    ```

11. En el bloque de código de `CupcakeRepository.cs`, coloque el cursor después de la segunda llave de apertura `{`, presione Entrar y escriba el siguiente código:

    ```csharp
    private CupcakeContext _context;

    public CupcakeRepository(CupcakeContext context)
    {
        _context = context;
    }
    ```

12. Asegúrese de que el cursor esté al final del bloque de código del constructor, presione Entrar dos veces y escriba el siguiente código:

    ```csharp
    public IEnumerable<Cupcake> GetCupcakes()
    {
        return _context.Cupcakes.ToList();
    }
    ```

13. Asegúrese de que el cursor esté al final del bloque de código del método `GetCupcakes`, presione Entrar dos veces y escriba el siguiente código:

    ```csharp
    public Cupcake GetCupcakeById(int id)
    {
        return _context.Cupcakes.Include(b => b.Bakery)
            .SingleOrDefault(c => c.CupcakeId == id);
    }
    ```

14. Asegúrese de que el cursor esté al final del bloque de código del método `GetCupcakeById`, presione Entrar dos veces y escriba el siguiente código:

    ```csharp
    public void CreateCupcake(Cupcake cupcake)
    {
        if (cupcake.PhotoAvatar != null && cupcake.PhotoAvatar.Length > 0)
        {
            cupcake.ImageMimeType = cupcake.PhotoAvatar.ContentType;
            cupcake.ImageName = Path.GetFileName(cupcake.PhotoAvatar.FileName);
            using (var memoryStream = new MemoryStream())
            {
                cupcake.PhotoAvatar.CopyTo(memoryStream);
                cupcake.PhotoFile = memoryStream.ToArray();
            }
            _context.Add(cupcake);
            _context.SaveChanges();
        }
    }
    ```

15. Asegúrese de que el cursor esté al final del bloque de código del método `CreateCupcake`, presione Entrar dos veces y escriba el siguiente código:

    ```csharp
    public void DeleteCupcake(int id)
    {
        var cupcake = _context.Cupcakes.SingleOrDefault(c => c.CupcakeId == id);
        _context.Cupcakes.Remove(cupcake);
        _context.SaveChanges();
    }
    ```

16. Asegúrese de que el cursor esté al final del bloque de código del método `DeleteCupcake`, presione Entrar dos veces y escriba el siguiente código:

    ```csharp
    public void SaveChanges()
    {
        _context.SaveChanges();
    }
    ```

17. Asegúrese de que el cursor esté al final del bloque de código del método `SaveChanges`, presione Entrar dos veces y escriba el siguiente código:

    ```csharp
    public IQueryable<Bakery> PopulateBakeriesDropDownList()
    {
        var bakeriesQuery = from b in _context.Bakeries
                               orderby b.BakeryName
                               select b;
        return bakeriesQuery;
    }
    ```

18. En la ventana `Cupcakes - Microsoft Visual Studio`, en `Solution Explorer`, haga clic en `Program.cs`.
19. En la ventana de código de `Program.cs`, busque la línea:

    ```csharp
    var app = builder.Build();
    ```

20. Antes de esta línea, agregue el siguiente código:

    ```csharp
    builder.Services.AddTransient<ICupcakeRepository, CupcakeRepository>();
    ```

21. En la ventana `Cupcakes - Microsoft Visual Studio`, en el menú `File`, haga clic en `Save All`.

### Tarea 2: Actualizar un controlador para usar un repositorio

1. En la ventana `Cupcakes - Microsoft Visual Studio`, en `Solution Explorer`, expanda `Controllers` y haga clic en `CupcakeController.cs`.
2. En la ventana de código de `CupcakeController.cs`, localice el siguiente código:

   ```csharp
   using Microsoft.AspNetCore.Mvc;
   ```

3. Asegúrese de que el cursor esté al final del espacio de nombres `Microsoft.AspNetCore.Mvc`, presione Entrar y escriba el siguiente código:

   ```csharp
   using Microsoft.AspNetCore.Mvc.Rendering;
   using Microsoft.EntityFrameworkCore;
   ```

4. En el bloque de código de `CupcakeController.cs`, localice el siguiente código:

   ```csharp
   public IActionResult Index()
   {
       return View();
   }
   ```

5. Coloque el cursor antes del código localizado, presione Entrar, presione la tecla de flecha arriba, escriba el siguiente código y presione Entrar:

   ```csharp
   private ICupcakeRepository _repository;

   public CupcakeController(ICupcakeRepository repository, IWebHostEnvironment environment)
   {
       _repository = repository;
       _environment = environment;
   }
   ```

### Tarea 3: Usar Entity Framework Core para recuperar datos

1. En el bloque de código de `CupcakeController.cs`, dentro de la acción `Index`, seleccione el siguiente código:

   ```csharp
   return View();
   ```

2. Reemplace el código seleccionado por el siguiente:

   ```csharp
   return View(_repository.GetCupcakes());
   ```

3. Asegúrese de que el cursor esté al final del bloque de código de la acción `Index`, presione Entrar dos veces y escriba el siguiente código:

   ```csharp
   public IActionResult Details(int id)
   {
       var cupcake = _repository.GetCupcakeById(id);
       if (cupcake == null)
       {
           return NotFound();
       }
       return View(cupcake);
   }
   ```

4. Busque las líneas:

   ```csharp
   public IActionResult GetImage(int id)
   {
     Cupcake requestedCupcake = null;
   ```

5. Reemplácelas por el siguiente código:

   ```csharp
   public IActionResult GetImage(int id)
   {
     Cupcake requestedCupcake = _repository.GetCupcakeById(id);
   ```

6. Asegúrese de que el cursor esté al final del bloque de código de la acción `Details`, presione Entrar dos veces y escriba el siguiente código:

   ```csharp
   private void PopulateBakeriesDropDownList(int? selectedBakery = null)
   {
       var bakeries = _repository.PopulateBakeriesDropDownList();
       ViewBag.BakeryID = new SelectList(bakeries.AsNoTracking(), "BakeryId", "BakeryName", selectedBakery);
   }
   ```

7. En la ventana `Cupcakes - Microsoft Visual Studio`, en el menú `File`, haga clic en `Save All`.

### Tarea 4: Manipular datos mediante Entity Framework Core

1. En el bloque de código de `CupcakeController.cs`, localice el siguiente código:

   ```csharp
   public IActionResult Details(int id)
   {
       var cupcake = _repository.GetCupcakeById(id);
       if (cupcake == null)
       {
           return NotFound();
       }
       return View(cupcake);
   }
   ```

2. Coloque el cursor al final del código localizado, presione Entrar, escriba el siguiente código y después presione Entrar dos veces:

   ```csharp
   [HttpGet]
   public IActionResult Create()
   {
       PopulateBakeriesDropDownList();
       return View();
   }
   ```

3. Asegúrese de que el cursor esté al final del bloque de código de la acción `Create`, presione Entrar dos veces y escriba el siguiente código:

   ```csharp
   [HttpPost, ActionName("Create")]
   public IActionResult CreatePost(Cupcake cupcake)
   {
       if (ModelState.IsValid)
       {
           _repository.CreateCupcake(cupcake);
           return RedirectToAction(nameof(Index));
       }
       PopulateBakeriesDropDownList(cupcake.BakeryId);
       return View(cupcake);
   }
   ```

4. Asegúrese de que el cursor esté al final del bloque de código del método `CreatePost`, presione Entrar dos veces y escriba el siguiente código:

   ```csharp
   [HttpGet]
   public IActionResult Edit(int id)
   {
       Cupcake cupcake = _repository.GetCupcakeById(id);
       if (cupcake == null)
       {
           return NotFound();
       }
       PopulateBakeriesDropDownList(cupcake.BakeryId);
       return View(cupcake);
   }
   ```

5. Asegúrese de que el cursor esté al final del bloque de código de la acción `Edit`, presione Entrar dos veces y escriba el siguiente código:

   ```csharp
   [HttpPost, ActionName("Edit")]
   public async Task<IActionResult> EditPost(int id)
   {
       var cupcakeToUpdate = _repository.GetCupcakeById(id);
       bool isUpdated = await TryUpdateModelAsync<Cupcake>(
                               cupcakeToUpdate,
                               "",
                               c => c.BakeryId,
                               c => c.CupcakeType,
                               c => c.Description,
                               c => c.GlutenFree,
                               c => c.Price);
       if (isUpdated == true)
       {
           _repository.SaveChanges();
           return RedirectToAction(nameof(Index));
       }
       PopulateBakeriesDropDownList(cupcakeToUpdate.BakeryId);
       return View(cupcakeToUpdate);
   }
   ```

6. Asegúrese de que el cursor esté al final del bloque de código de la acción `EditPost`, presione Entrar dos veces y escriba el siguiente código:

   ```csharp
   [HttpGet]
   public IActionResult Delete(int id)
   {
       var cupcake = _repository.GetCupcakeById(id);
       if (cupcake == null)
       {
           return NotFound();
       }
       return View(cupcake);
   }
   ```

7. Asegúrese de que el cursor esté al final del bloque de código de la acción `Delete`, presione Entrar dos veces y escriba el siguiente código:

   ```csharp
   [HttpPost, ActionName("Delete")]
   public IActionResult DeleteConfirmed(int id)
   {
       _repository.DeleteCupcake(id);
       return RedirectToAction(nameof(Index));
   }
   ```

### Tarea 5: Ejecutar la aplicación

1. En la ventana `Cupcakes - Microsoft Visual Studio`, en el menú `File`, haga clic en `Save All`.
2. En el menú `Debug`, haga clic en `Start Without Debugging`.
3. En Microsoft Edge, haga clic en `Add Cupcake`.

   **Nota:** El navegador muestra la acción `Create` dentro de `CupcakesController`.

4. En la página `Add a Cupcake to the Shop`, en la lista `Bakery`, elija cualquier valor.
5. En la lista `Cupcake Type`, elija cualquier valor.
6. En el cuadro `Description`, escriba `A delicious cupcake`.
7. En el cuadro `Price`, escriba `3.50`.
8. En el cuadro `Cupcake Picture`, importe la imagen de `D:\Allfiles\Mod07\Labfiles\Image\strawberry-cupcake.jpg` y haga clic en `Submit`.
9. En la página `Welcome to our Cupcakes Shop`, compruebe que estén presentes los detalles del cupcake recién enviado.
10. Seleccione un cupcake de su elección y haga clic en `Details`.
11. Vea los detalles del cupcake y haga clic en `Back to List`.
12. En la página `Welcome to our Cupcakes Shop`, seleccione un cupcake de su elección y haga clic en `Edit`.
13. En la página `Edit a Cupcake`, en el cuadro `Price`, escriba `9.35` y haga clic en `Save`.
14. En la página `Welcome to our Cupcakes Shop`, seleccione el cupcake que editó y haga clic en `Details`.
15. En la página `Cupcake's Details`, compruebe los detalles recién editados del cupcake y haga clic en `Back to List`.
16. En la página `Welcome to our Cupcakes Shop`, seleccione un cupcake de su elección y haga clic en `Delete`.
17. En la página `Are you sure you want to delete this?`, haga clic en el botón `Delete`.
18. En la página `Welcome to our Cupcakes Shop`, compruebe que el cupcake se haya eliminado.
19. En Microsoft Edge, haga clic en `Close`.
20. En la ventana `Cupcakes - Microsoft Visual Studio`, en el menú `Debug`, haga clic en `Start Without Debugging`.
21. En Microsoft Edge, compruebe que los cambios realizados se hayan conservado.
22. En Microsoft Edge, haga clic en `Close`.

    **Nota:** Si desea restablecer los datos a sus valores originales, puede eliminar `cupcake.db` de la carpeta de la solución. La próxima vez que ejecute la aplicación, el archivo volverá a crearse.

**Resultados:** Después de completar este ejercicio, podrá usar Entity Framework Core para recuperar y almacenar datos mediante un repositorio en `CupcakeController`.

## Ejercicio 3: Usar Entity Framework Core para conectarse a Microsoft SQL Server

### Escenario

En este ejercicio, primero configurará la aplicación web Cupcakes Shop para conectarse a una base de datos SQL Server en lugar de una base de datos SQLite. Después almacenará en un archivo de configuración la cadena de conexión utilizada para conectarse a la base de datos. A continuación, usará las migraciones para crear la base de datos. Por último, agregará una propiedad a una entidad y usará las migraciones para actualizar el esquema de la base de datos.

Las tareas principales de este ejercicio son las siguientes:

- Conectarse a una base de datos SQL.
- Especificar una cadena de conexión en un archivo de configuración.
- Usar migraciones para crear una base de datos.
- Ejecutar la aplicación.
- Usar migraciones para actualizar el esquema de la base de datos.
- Ejecutar la aplicación.

### Tarea 1: Conectarse a una base de datos SQL Server

1. En la ventana `Cupcakes - Microsoft Visual Studio`, en `Solution Explorer`, expanda la carpeta `Data` y haga clic en `CupcakeContext.cs`.
2. En la ventana de código de `CupcakeContext.cs`, elimine el siguiente código:

   ```csharp
   cupcakeContext.Database.EnsureCreated();
   ```

3. En la ventana `Cupcakes - Microsoft Visual Studio`, en `Solution Explorer`, haga clic en `Program.cs`.
4. En la ventana de código de `Program.cs`, seleccione el siguiente código:

   ```csharp
   services.AddDbContext<CupcakeContext>(options =>
       options.UseSqlite("Data Source=cupcake.db"));
   ```

5. Reemplace el código seleccionado por el siguiente:

   ```csharp
   string connectionString = "Server=(localdb)\\MSSQLLocalDB;Database=BakeriesDb;Trusted_Connection=True;MultipleActiveResultSets=true";
   builder.Services.AddDbContext<CupcakeContext>(options =>
       options.UseSqlServer(connectionString));
   ```

### Tarea 2: Especificar una cadena de conexión en un archivo de configuración

1. En la ventana `Cupcakes - Microsoft Visual Studio`, en `Solution Explorer`, haga clic con el botón derecho en `Cupcakes`, señale `Add` y haga clic en `New Item…`.
2. En el cuadro de diálogo `Add New Item - Cupcakes`, en el panel de navegación, bajo `Installed`, haga clic en `ASP.NET Core`. En el panel de resultados, haga clic en `App Settings File` y después en `Add`.
3. En la ventana de código de `appsettings.json`, seleccione el siguiente código:

   ```json
   "DefaultConnection": "Server=(localdb)\\MSSQLLocalDB;Database=_CHANGE_ME;Trusted_Connection=True;MultipleActiveResultSets=true"
   ```

4. Reemplace el código seleccionado por el siguiente:

   ```json
   "DefaultConnection": "Server=(localdb)\\MSSQLLocalDB;Database=BakeriesDb;Trusted_Connection=True;MultipleActiveResultSets=true"
   ```

5. En la ventana de código de `Program.cs`, seleccione el siguiente código:

   ```csharp
   string connectionString = "Server=(localdb)\\MSSQLLocalDB;Database=BakeriesDb;Trusted_Connection=True;MultipleActiveResultSets=true";
   services.AddDbContext<CupcakeContext>(options =>
       options.UseSqlServer(connectionString));
   ```

6. Reemplace el código seleccionado por el siguiente:

   ```csharp
   builder.Services.AddDbContext<CupcakeContext>(options =>
       options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));
   ```

### Tarea 3: Usar migraciones para crear una base de datos

1. En la ventana `Cupcakes - Microsoft Visual Studio`, en el menú `Tools`, señale `NuGet Package Manager` y haga clic en `Package Manager Console`.
2. En la pestaña `Package Manager Console`, escriba el siguiente comando y presione Entrar:

   ```text
   Add-Migration InitialCreate
   ```

   **Nota:** En `Solution Explorer`, compruebe si se creó una carpeta nueva llamada `Migrations` con varios archivos. Si el comando produce un error, ejecute `Install-Package Microsoft.EntityFrameworkCore.Tools` para asegurarse de que las herramientas estén instaladas.

3. En la pestaña `Package Manager Console`, escriba el siguiente comando y presione Entrar (puede indicar que la base de datos ya existe si ha realizado antes este ejercicio):

   ```text
   Update-Database
   ```

4. En la ventana `Cupcakes - Microsoft Visual Studio`, en el menú `View`, haga clic en `SQL Server Object Explorer`.
5. En `SQL Server Object Explorer`, expanda `(localdb)\MSSQLLocalDB`, expanda `Databases` y después `BakeriesDb`.

   **Nota:** Debe haber varias tablas en `BakeriesDb`, incluidas `dbo.Bakeries` y `dbo.Cupcakes`.

### Tarea 4: Ejecutar la aplicación

1. En la ventana `Cupcakes - Microsoft Visual Studio`, en el menú `File`, haga clic en `Save All`.
2. En el menú `Debug`, haga clic en `Start Without Debugging`.
3. En Microsoft Edge, haga clic en `Add Cupcake`.

   **Nota:** El navegador muestra la acción `Create` dentro de `CupcakesController`.

4. En la página `Add a Cupcake to the Shop`, en la lista `Bakery`, elija cualquier valor.
5. En la lista `Cupcake Type`, elija cualquier valor.
6. En el cuadro `Description`, escriba `A delicious cupcake`.
7. En el cuadro `Price`, escriba `3.78`.
8. En el cuadro `Cupcake Picture`, importe una imagen de `D:\Allfiles\Mod07\Labfiles\Image\strawberry-cupcake.jpg` y haga clic en `Submit`.
9. En la página `Welcome to our Cupcakes Shop`, compruebe los detalles del cupcake recién enviado.
10. Seleccione un cupcake de su elección y haga clic en `Details`.
11. Vea los detalles del cupcake y haga clic en `Back to List`.
12. En la página `Welcome to our Cupcakes Shop`, seleccione un cupcake de su elección y haga clic en `Edit`.
13. En la página `Edit a Cupcake`, en el cuadro `Price`, escriba `2.99` y haga clic en `Save`.
14. En la página `Welcome to our Cupcakes Shop`, seleccione el cupcake que editó y haga clic en `Details`.
15. En la página `Cupcake's Details`, compruebe los detalles recién editados del cupcake y haga clic en `Back to List`.
16. En la página `Welcome to our Cupcakes Shop`, seleccione un cupcake de su elección y haga clic en `Delete`.
17. En la página `Are you sure you want to delete?`, haga clic en `Delete`.
18. En la página `Welcome to our Cupcakes Shop`, compruebe que el cupcake se haya eliminado.
19. En Microsoft Edge, haga clic en `Close`.
20. En la ventana `Cupcakes - Microsoft Visual Studio`, en el menú `Debug`, haga clic en `Start Without Debugging`.
21. En Microsoft Edge, compruebe que los cambios realizados se hayan conservado en la base de datos.
22. En Microsoft Edge, haga clic en `Close`.

### Tarea 5: Usar migraciones para actualizar el esquema de la base de datos

1. En la ventana `Cupcakes - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Models`, haga clic en `Cupcake.cs`.
2. En la ventana de código de `Cupcake.cs`, localice el siguiente código:

   ```csharp
   [Display(Name = "Gluten Free:")]
   public bool GlutenFree { get; set; }
   ```

3. Coloque el cursor al final del código localizado, presione Entrar dos veces y escriba el siguiente código:

   ```csharp
   [Display(Name = "Calorific Value:")]
   public int CalorificValue { get; set; }
   ```

4. En la ventana `Cupcakes - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Data`, haga clic en `CupcakeContext.cs`.
5. En la ventana de código de `CupcakeContext.cs`, dentro del bloque de código del método `OnModelCreating`, seleccione el siguiente código:

   ```csharp
   ImageName = "birthday-cupcake.jpg"
   ```

6. Reemplace el código seleccionado por el siguiente:

   ```csharp
   ImageName = "birthday-cupcake.jpg",
   CalorificValue = 355
   ```

7. En el bloque de código del método `OnModelCreating`, seleccione el siguiente código:

   ```csharp
   ImageName = "chocolate-cupcake.jpg"
   ```

8. Reemplace el código seleccionado por el siguiente:

   ```csharp
   ImageName = "chocolate-cupcake.jpg",
   CalorificValue = 195
   ```

9. En el bloque de código del método `OnModelCreating`, seleccione el siguiente código:

   ```csharp
   ImageName = "pink-cupcake.jpg"
   ```

10. Reemplace el código seleccionado por el siguiente:

    ```csharp
    ImageName = "pink-cupcake.jpg",
    CalorificValue = 295
    ```

11. En el bloque de código del método `OnModelCreating`, seleccione el siguiente código:

    ```csharp
    ImageName = "turquoise-cupcake.jpg"
    ```

12. Reemplace el código seleccionado por el siguiente:

    ```csharp
    ImageName = "turquoise-cupcake.jpg",
    CalorificValue = 360
    ```

13. En la ventana `Cupcakes - Microsoft Visual Studio`, en `Solution Explorer`, expanda `Views`, expanda `Cupcake` y haga clic en `Details.cshtml`.
14. En la ventana de código de `Details.cshtml`, localice el siguiente código:

    ```html
    <div>
        <p class="display-label">
                @Html.DisplayNameFor(model => model.Price)
        </p>
        <p class="display-field">
                @Html.DisplayFor(model => model.Price)
        </p>
    </div>
    ```

15. Coloque el cursor después del signo `>` de la etiqueta `</div>`, presione Entrar y escriba el siguiente código:

    ```html
    <div>
        <p class="display-label">
                @Html.DisplayNameFor(model => model.CalorificValue)
        </p>
        <p class="display-field">
                @Html.DisplayFor(model => model.CalorificValue)
        </p>
    </div>
    ```

16. En la ventana `Cupcakes - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Views` y `Cupcake`, haga clic en `Edit.cshtml`.
17. En la ventana de código de `Edit.cshtml`, localice el siguiente código:

    ```html
    <div class="form-field">
        <label asp-for="Price"></label>
        <input asp-for="Price" />
        <span asp-validation-for="Price"></span>
    </div>
    ```

18. Coloque el cursor después del signo `>` de la etiqueta `</div>`, presione Entrar y escriba el siguiente código:

    ```html
    <div class="form-field">
        <label asp-for="CalorificValue"></label>
        <input asp-for="CalorificValue" />
        <span asp-validation-for="CalorificValue"></span>
    </div>
    ```

19. En la ventana `Cupcakes - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Views` y `Cupcake`, haga clic en `Create.cshtml`.
20. En la ventana de código de `Create.cshtml`, localice el siguiente código:

    ```html
    <div class="form-field">
        <label asp-for="Price"></label>
        <input asp-for="Price" />
        <span asp-validation-for="Price"></span>
    </div>
    ```

21. Coloque el cursor después del signo `>` de la etiqueta `</div>`, presione Entrar y escriba el siguiente código:

    ```html
    <div class="form-field">
        <label asp-for="CalorificValue"></label>
        <input asp-for="CalorificValue" />
        <span asp-validation-for="CalorificValue"></span>
    </div>
    ```

22. En la ventana `Cupcakes - Microsoft Visual Studio`, en el menú `Tools`, señale `NuGet Package Manager` y haga clic en `Package Manager Console`.
23. En la pestaña `Package Manager Console`, escriba el siguiente comando y presione Entrar:

    ```text
    Add-Migration AddCupcakeCalorificValue
    ```

    **Nota:** En `Solution Explorer`, bajo `Migrations`, compruebe que se creó un archivo de migración nuevo.

24. En el panel `Package Manager Console`, escriba el siguiente comando y presione Entrar:

    ```text
    Update-Database
    ```

### Tarea 6: Ejecutar la aplicación

1. En la ventana `Cupcakes - Microsoft Visual Studio`, en el menú `File`, haga clic en `Save All`.
2. En el menú `Debug`, haga clic en `Start Without Debugging`.
3. Seleccione un cupcake de su elección y haga clic en `Details`.

   **Nota:** El navegador muestra el valor calórico del cupcake. Si no se hubiera realizado la migración de la base de datos, se produciría un error.

4. Vea los detalles del cupcake y haga clic en `Back to List`.
5. En Microsoft Edge, haga clic en `Close`.
6. En la ventana `Cupcakes - Microsoft Visual Studio`, en el menú `File`, haga clic en `Exit`.

**Resultados:** Después de completar este ejercicio, habrá creado una aplicación de tienda de cupcakes en la que los usuarios pueden agregar un cupcake nuevo, editar un cupcake, eliminar un cupcake y ver sus detalles.
