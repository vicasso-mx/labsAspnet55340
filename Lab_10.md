# Lab 10: Pruebas y solución de problemas

## Escenario

Para mejorar la calidad de una aplicación web de una tienda de camisas, el equipo agregará pruebas y herramientas de diagnóstico. Debes crear pruebas unitarias para un modelo y un controlador. En desarrollo, los errores mostrarán una página detallada de excepciones; en producción, una página personalizada. También agregarás registros de eventos.

## Ejercicio 1: Probar un modelo

Crearás un proyecto MSTest, lo agregarás a la solución, establecerás una referencia a la aplicación ASP.NET Core y probarás el modelo `Shirt` mediante desarrollo guiado por pruebas.

Tareas: agregar un proyecto de pruebas; escribir una prueba del modelo; ejecutarla y observar el fallo; implementar el modelo; ejecutarla de nuevo y observar que pasa.

### Tarea 1: Agregar un proyecto de pruebas

1. Ve a `D:\Allfiles\Mod10\Labfiles\01_ShirtStore_begin` y abre `ShirtStore.sln` con doble clic. **Nota:** Si aparece **Security Warning for ShirtStore**, desmarca **Ask me for every project in this solution** y haz clic en **OK**.
2. En **Solution Explorer**, haz clic derecho en **Solution 'ShirtStore'** y selecciona **Add > New Project**.
3. En **Add a new project**, escribe `test` en el cuadro de búsqueda.
4. En los resultados, selecciona **MSTest Test Project**.
5. En **Name**, escribe `ShirtStoreWebsite.Tests` y haz clic en **Next**.
6. En **Additional information**, selecciona **.NET 8.0 (Long-term support)** y haz clic en **Create**.
7. En **Solution Explorer > ShirtStoreWebsite.Tests**, haz clic derecho en **Dependencies** y selecciona **Add Project Reference**.
8. En **Reference Manager - ShirtStoreWebsite.Tests**, marca **ShirtStoreWebsite** y haz clic en **OK**.

### Tarea 2: Escribir una prueba para un modelo

1. En **Solution Explorer**, haz clic derecho en **ShirtStoreWebsite.Tests** y selecciona **Add > New Folder**.
2. En **NewFolder**, escribe `Models` y presiona **Enter**.
3. En **ShirtStoreWebsite.Tests**, haz clic derecho en `UnitTest1.cs` y selecciona **Rename**.
4. Escribe `ShirtTest` y presiona **Enter**.
5. Cuando Visual Studio pregunte si debe cambiar las referencias, haz clic en **Yes**.
6. Haz clic derecho en `ShirtTest.cs` y selecciona **Cut**.
7. Haz clic derecho en **Models** y selecciona **Paste**.
8. En `ShirtTest.cs`, comprueba el nombre de la clase y cámbialo a `ShirtTest` si es necesario:

   ```csharp
   namespace ShirtStoreWebsite.Tests.Models
   {
       [TestClass]
       public class ShirtTest
       {
   ```
9. Selecciona:

   ```csharp
   public void TestMethod1()
   {
   }
   ```
10. Reemplaza la selección por:

    ```csharp
    public void IsGetFormattedTaxedPriceReturnsCorrectly()
    {
        Shirt shirt = new Shirt
        {
            Price = 10F,
            Tax = 1.2F
        };

        string taxedPrice = shirt.GetFormattedTaxedPrice();

        Assert.AreEqual("$12.00", taxedPrice);
    }
    ```

    **Nota:** Comprueba que Visual Studio haya agregado automáticamente la referencia al espacio de nombres `ShirtStoreWebsite.Models`.
11. En Visual Studio, selecciona **File > Save All**.

### Tarea 3: Ejecutar la prueba unitaria; debe fallar

1. En Visual Studio, selecciona **Test > Run All Tests**. **Nota:** **Test Explorer** mostrará una prueba fallida: `IsGetFormattedTaxedPriceReturnsCorrectly`.

### Tarea 4: Implementar el modelo para que la prueba pase

1. En **Solution Explorer > ShirtStoreWebsite > Models**, abre `Shirt.cs`.
2. Selecciona:

   ```csharp
   return Price.ToString($"C2", CultureInfo.GetCultureInfo("en-US"));
   ```
3. Reemplaza la selección por:

   ```csharp
   return (Price * Tax).ToString($"C2", CultureInfo.GetCultureInfo("en-US"));
   ```
4. En Visual Studio, selecciona **File > Save All**.

### Tarea 5: Ejecutar la prueba unitaria; ahora pasa

1. En Visual Studio, selecciona **Test > Run > All Tests**. **Nota:** **Test Explorer** mostrará que pasó `IsGetFormattedTaxedPriceReturnsCorrectly`.

**Resultado:** Creaste un proyecto de pruebas y probaste el modelo al corregir su código, como ejemplo de desarrollo guiado por pruebas.

## Ejercicio 2: Probar un controlador con un repositorio simulado

Crearás una interfaz de repositorio como dependencia del controlador. Para probarlo, implementarás un repositorio simulado y lo pasarás al constructor.

Tareas: crear la interfaz; implementarla con un repositorio simulado; pasarla al controlador; escribir y ejecutar una prueba; implementar el controlador; volver a ejecutar la prueba.

### Tarea 1: Crear una interfaz de repositorio

1. En **Solution Explorer**, haz clic derecho en **ShirtStoreWebsite** y selecciona **Add > New Folder**.
2. Escribe `Services` en **NewFolder** y presiona **Enter**.
3. Haz clic derecho en **Services** y selecciona **Add > New Item…**.
4. En **Add New Item -- ShirtStoreWebsite**, selecciona **Interface**.
5. En **Name**, escribe `IShirtRepository` y haz clic en **Add**.
6. En `IShirtRepository.cs`, selecciona:

   ```csharp
   public interface IShirtRepository
   {
   }
   ```
7. Reemplaza la selección por:

   ```csharp
   public interface IShirtRepository
   {
       IEnumerable<Shirt> GetShirts();
       bool AddShirt(Shirt shirt);
       bool RemoveShirt(int id);
   }
   ```

### Tarea 2: Implementar la interfaz con un repositorio simulado

1. En **Solution Explorer**, haz clic derecho en **ShirtStoreWebsite.Tests** y selecciona **Add > New Folder**.
2. Escribe `FakeRepositories` y presiona **Enter**.
3. Haz clic derecho en **FakeRepositories** y selecciona **Add > Class…**.
4. En **Name**, escribe `FakeShirtRepository` y haz clic en **Add**.
5. En `FakeShirtRepository.cs`, selecciona:

   ```csharp
   internal class FakeShirtRepository
   ```
6. Reemplaza la selección por:

   ```csharp
   internal class FakeShirtRepository
   {
   }
   ```
7. En `FakeShirtRepository.cs`, localiza el siguiente código proporcionado para la implementación del repositorio simulado:

   ```csharp
   internal class FakeShirtRepository : IShirtRepository
   {
       public IEnumerable<Shirt> GetShirts()
       {
           return new List<Shirt>()
           {
               new Shirt { Color = ShirtColor.Black, Size = ShirtSize.S, Price = 11F },
               new Shirt { Color = ShirtColor.Gray, Size = ShirtSize.M, Price = 12F },
               new Shirt { Color = ShirtColor.White, Size = ShirtSize.L, Price = 13F }
           };
       }

       public bool AddShirt(Shirt shirt)
       {
           return true;
       }

       public bool RemoveShirt(int id)
       {
           return true;
       }
   }
   ```

### Tarea 3: Pasar el repositorio simulado al constructor de un controlador

1. En **Solution Explorer > Controllers**, abre `ShirtController.cs`.
2. Localiza:

   ```csharp
   using ShirtStoreWebsite.Models;
   ```
3. Al final de esa línea, presiona **Enter** y agrega:

   ```csharp
   using ShirtStoreWebsite.Services;
   ```
4. Localiza:

   ```csharp
   public class ShirtController : Controller
   {
   ```
5. Después de la llave de apertura, presiona **Enter** y agrega:

   ```csharp
   private IShirtRepository _repository;

   public ShirtController(IShirtRepository repository)
   {
       _repository = repository;
   }
   ```

### Tarea 4: Escribir una prueba para el controlador

1. En **Solution Explorer**, haz clic derecho en **ShirtStoreWebsite.Tests** y selecciona **Add > New Folder**.
2. Escribe `Controllers` y presiona **Enter**.
3. Haz clic derecho en **Controllers** y selecciona **Add > Class…**.
4. En **Name**, escribe `ShirtControllerTest` y haz clic en **Add**.
5. En `ShirtControllerTest.cs`, selecciona:

   ```csharp
   internal class ShirtControllerTest
   {
   }
   ```
6. Reemplaza la selección por:

   ```csharp
   [TestClass]
   public class ShirtControllerTest
   {
       [TestMethod]
       public void IndexModelShouldContainAllShirts()
       {
           IShirtRepository fakeShirtRepository = new FakeShirtRepository();
           ShirtController shirtController = new ShirtController(fakeShirtRepository);
           ViewResult viewResult = shirtController.Index() as ViewResult;
           List<Shirt> shirts = viewResult.Model as List<Shirt>;
           Assert.AreEqual(shirts.Count, 3);
       }
   }
   ```
7. En Visual Studio, selecciona **File > Save All**.

### Tarea 5: Ejecutar la prueba unitaria; debe fallar

1. En Visual Studio, selecciona **Test > Run > All Tests**. **Nota:** **Test Explorer** mostrará `IndexModelShouldContainAllShirts` como fallida y `IsGetFormattedTaxedPriceReturnsCorrectly` como superada.

### Tarea 6: Implementar el controlador para que la prueba pase

1. En **Solution Explorer > ShirtStoreWebsite > Controllers**, abre `ShirtController.cs`.
2. Selecciona:

   ```csharp
   return View();
   ```
3. Reemplaza la selección por:

   ```csharp
   IEnumerable<Shirt> shirts = _repository.GetShirts();
   return View(shirts);
   ```
4. Localiza:

   ```csharp
   public IActionResult AddShirt(Shirt shirt)
   {
   ```
5. Al final de ese código, presiona **Enter** y agrega:

   ```csharp
   _repository.AddShirt(shirt);
   ```
6. Localiza:

   ```csharp
   public IActionResult Delete(int id)
   {
   ```
7. Al final de ese código, presiona **Enter** y agrega:

   ```csharp
   _repository.RemoveShirt(id);
   ```
8. En Visual Studio, selecciona **File > Save All**.

### Tarea 7: Ejecutar la prueba unitaria; ahora pasa

1. En Visual Studio, selecciona **Test > Run > All Tests**. **Nota:** **Test Explorer** mostrará que pasaron `IndexModelShouldContainAllShirts` e `IsGetFormattedTaxedPriceReturnsCorrectly`.

**Resultado:** Probaste un controlador mediante un repositorio simulado.

## Ejercicio 3: Implementar un repositorio en el proyecto MVC

Después de probar el modelo y el controlador, agregarás `ShirtRepository` para consultar y actualizar la base de datos. Registrarás el repositorio como servicio de la aplicación.

### Tarea 1: Implementar la interfaz en una clase de repositorio

1. En **Solution Explorer**, haz clic derecho en **Services** y selecciona **Add > Class…**.
2. En **Name**, escribe `ShirtRepository` y haz clic en **Add**.
3. En `ShirtRepository.cs`, selecciona:

   ```csharp
   public class ShirtRepository
   ```
4. Reemplaza la selección por:

   ```csharp
   public class ShirtRepository : IShirtRepository
   ```
5. Localiza:

   ```csharp
   public class ShirtRepository : IShirtRepository
   {
   ```

   **Nota:** Aparecerá una advertencia porque aún no se implementó la interfaz. Puedes ignorarla por ahora.
6. Al final de ese código, presiona **Enter** y agrega:

   ```csharp
   private ShirtContext _context;

   public ShirtRepository(ShirtContext context)
   {
       _context = context;
   }

   public IEnumerable<Shirt> GetShirts()
   {
       return _context.Shirts.ToList();
   }

   public bool AddShirt(Shirt shirt)
   {
       _context.Add(shirt);
       int entries = _context.SaveChanges();
       if (entries > 0)
       {
           return true;
       }
       else
       {
           return false;
       }
   }

   public bool RemoveShirt(int id)
   {
       var shirt = _context.Shirts.SingleOrDefault(m => m.Id == id);
       _context.Shirts.Remove(shirt);
       int entries = _context.SaveChanges();
       if (entries > 0)
       {
           return true;
       }
       else
       {
           return false;
       }
   }
   ```

### Tarea 2: Registrar el repositorio como servicio

1. En **Solution Explorer**, abre `Program.cs`.
2. Localiza:

   ```csharp
   builder.Services.AddDbContext<ShirtContext>(options =>
       options.UseSqlServer(_configuration.GetConnectionString("DefaultConnection")));
   ```
3. Al final de ese código, presiona **Enter** dos veces y agrega:

   ```csharp
   builder.Services.AddScoped<IShirtRepository, ShirtRepository>();
   ```
4. En Visual Studio, selecciona **File > Save All**.

### Tarea 3: Ejecutar la aplicación MVC

1. En Visual Studio, selecciona **Debug > Start Without Debugging**. **Nota:** El navegador mostrará la página **Shirt Store**.
2. Cierra Microsoft Edge.

**Resultado:** Implementaste un repositorio que permite ejecutar la aplicación MVC.

## Ejercicio 4: Agregar manejo de excepciones

Configurarás una página detallada de excepciones para desarrollo y una página de error personalizada para producción. Crearás temporalmente un error para comprobar ambos comportamientos y luego lo quitarás.

### Tarea 1: Agregar manejo de excepciones en Program.cs

1. En `Program.cs`, localiza:

   ```csharp
   var app = builder.Build();
   ```
2. Después de esa línea, inserta:

   ```csharp
   if (builder.Environment.IsDevelopment())
   {
       app.UseDeveloperExceptionPage();
   }
   else
   {
       app.UseExceptionHandler("/error.html");
   }
   ```

### Tarea 2: Crear una excepción temporal para la prueba

1. En **Solution Explorer > Controllers**, abre `ShirtController.cs`.
2. Selecciona:

   ```csharp
   _repository.RemoveShirt(id);
   ```
3. Reemplaza la selección por:

   ```csharp
   _repository.RemoveShirt(-1);
   ```
4. En Visual Studio, selecciona **File > Save All**.

### Tarea 3: Ejecutar la aplicación en el entorno de desarrollo

1. En la barra de herramientas de Visual Studio, abre la flecha junto a **Start Debugging** y selecciona **Development**.
2. Selecciona **Debug > Start Without Debugging**.
3. En Microsoft Edge, haz clic en el enlace **Delete** de la parte superior de la tabla **Shirts in stock**. **Nota:** Se mostrará una página detallada de la excepción con información de la aplicación.
4. Cierra Microsoft Edge.

### Tarea 4: Ejecutar la aplicación en el entorno de producción

1. En la barra de herramientas de Visual Studio, abre la flecha junto a **Start Debugging** y selecciona **Production**.
2. Selecciona **Debug > Start Without Debugging**.
3. En Microsoft Edge, haz clic en el enlace **Delete** de la parte superior de la tabla **Shirts in stock**. **Nota:** Se mostrará la página personalizada `error.html`, situada en `wwwroot`.
4. Cierra Microsoft Edge.

### Tarea 5: Quitar la excepción temporal

1. En **Solution Explorer > Controllers**, abre `ShirtController.cs`.
2. Selecciona:

   ```csharp
   _repository.RemoveShirt(-1);
   ```
3. Reemplaza la selección por:

   ```csharp
   _repository.RemoveShirt(id);
   ```
4. En Visual Studio, selecciona **File > Save All**.

**Resultado:** Agregaste manejo de excepciones con una página personalizada en producción y una página detallada en desarrollo.

## Ejercicio 5: Agregar registro de eventos

Configurarás los registros de la aplicación por entorno mediante archivos `appsettings.json`. En desarrollo, los mensajes de nivel Trace aparecerán en la consola. En producción, los mensajes de nivel Warning se escribirán en un archivo. También inyectarás `ILogger` en el controlador y actualizarás su prueba.

### Tarea 1: Agregar registro de eventos a la aplicación MVC

1. En **Solution Explorer**, haz clic derecho en **ShirtStoreWebsite** y selecciona **Add > New Item…**.
2. En **Add New Item -- ShirtStoreWebsite**, en **Installed**, expande **ASP.NET Core** y selecciona **Web**.
3. En los resultados, selecciona **App Settings File**.
4. En **Name**, escribe `appsettings.development.json` y haz clic en **Add**.
5. En `appsettings.development.json`, selecciona:

   ```json
   "ConnectionStrings": {
       "DefaultConnection": "Server=(localdb)\\MSSQLLocalDB;Database=_CHANGE_ME;Trusted_Connection=True;MultipleActiveResultSets=true"
   }
   ```
6. Reemplaza la selección por:

   ```json
   "Logging": {
       "LogLevel": {
           "Default": "Trace"
       }
   }
   ```
7. En **Solution Explorer**, haz clic derecho en **ShirtStoreWebsite** y selecciona **Add > New Item…**.
8. En **Installed**, expande **ASP.NET Core** y selecciona **Web**.
9. En los resultados, selecciona **App Settings File**.
10. En **Name**, escribe `appsettings.production.json` y haz clic en **Add**.
11. En `appsettings.production.json`, selecciona:

    ```json
    "ConnectionStrings": {
        "DefaultConnection": "Server=(localdb)\\MSSQLLocalDB;Database=_CHANGE_ME;Trusted_Connection=True;MultipleActiveResultSets=true"
    }
    ```
12. Reemplaza la selección por:

    ```json
    "Logging": {
        "PathFormat": "shirt_store_logs.txt",
        "LogLevel": {
            "Default": "Warning"
        }
    }
    ```
13. En **Solution Explorer**, abre `Program.cs`.
14. Localiza:

    ```csharp
    var app = builder.Build();
    ```
15. Antes de esa línea, inserta:

    ```csharp
    builder.Logging.ClearProviders();
    var config = builder.Configuration.GetSection("Logging");
    if (builder.Environment.IsDevelopment())
    {
        builder.Logging.AddConfiguration(config);
        builder.Logging.AddConsole();
    }
    else
    {
        builder.Logging.AddFile("shirt_store_logs.txt");
    }
    ```
16. En **Solution Explorer > Controllers**, abre `ShirtController.cs`.
17. Localiza:

    ```csharp
    private IShirtRepository _repository;
    ```
18. Al final de esa línea, presiona **Enter** y agrega:

    ```csharp
    private ILogger _logger;
    ```
19. Selecciona:

    ```csharp
    public ShirtController(IShirtRepository repository)
    ```
20. Reemplaza la selección por:

    ```csharp
    public ShirtController(IShirtRepository repository, ILogger<ShirtController> logger)
    ```
21. Localiza:

    ```csharp
    _repository = repository;
    ```
22. Al final de esa línea, presiona **Enter** y agrega:

    ```csharp
    _logger = logger;
    ```
23. Localiza:

    ```csharp
    _repository.AddShirt(shirt);
    ```
24. Al final de esa línea, presiona **Enter** y agrega:

    ```csharp
    _logger.LogDebug($"A {shirt.Color.ToString()} shirt of size {shirt.Size.ToString()} with a price of {shirt.GetFormattedTaxedPrice()} was added successfully.");
    ```
25. Selecciona:

    ```csharp
    _repository.RemoveShirt(id);
    return RedirectToAction("Index");
    ```
26. Reemplaza la selección por:

    ```csharp
    try
    {
        _repository.RemoveShirt(id);
        _logger.LogDebug($"A shirt with id {id} was removed successfully.");
        return RedirectToAction("Index");
    }
    catch (Exception ex)
    {
        _logger.LogError(ex, $"An error occured while trying to delete shirt with id of {id}.");
        throw;
    }
    ```
27. En Visual Studio, selecciona **File > Save All**.

### Tarea 2: Probar el controlador con un framework de simulación

1. En Visual Studio, selecciona **Tools > NuGet Package Manager > Package Manager Console**.
2. En **Package Manager Console**, escribe el siguiente comando y presiona **Enter**:

   ```powershell
   Install-Package Moq -Version 4.9.0 -ProjectName ShirtStoreWebsite.Tests
   ```
3. En **Solution Explorer > ShirtStoreWebsite.Tests**, abre `ShirtControllerTest.cs`.
4. Selecciona:

   ```csharp
   ShirtController shirtController = new ShirtController(fakeShirtRepository);
   ```
5. Reemplaza la selección por:

   ```csharp
   Mock<ILogger<ShirtController>> mockLogger = new Mock<ILogger<ShirtController>>();
   ShirtController shirtController = new ShirtController(fakeShirtRepository, mockLogger.Object);
   ```
6. En Visual Studio, selecciona **File > Save All**. **Nota:** La prueba del controlador debe actualizarse porque el constructor ahora recibe un parámetro `ILogger`; tras el cambio, la prueba podrá pasar.

### Tarea 3: Ejecutar la prueba unitaria

1. En Visual Studio, selecciona **Test > Run > All Tests**. **Nota:** **Test Explorer** mostrará dos pruebas superadas: `IndexModelShouldContainAllShirts` e `IsGetFormattedTaxedPriceReturnsCorrectly`.

### Tarea 4: Ejecutar la aplicación en desarrollo

1. En la barra de herramientas de Visual Studio, abre la flecha junto a **Start Debugging** y selecciona **Development**.
2. Selecciona **Debug > Start Debugging**.
3. En la pestaña **Output**, selecciona **ASP.NET Core Web Server** en **Show output from** y haz clic en **Clear All**.
4. En Microsoft Edge, selecciona **M** en **Size**.
5. Selecciona **Yellow** en **Color**.
6. En **Price**, escribe `10`.
7. En **Tax**, escribe `1.2`.
8. Haz clic en **Add Shirt to Stock**. **Nota:** La camisa nueva aparecerá en la tabla **Shirts in Stock**.
9. En la pestaña **Output** de Visual Studio, presiona **Ctrl+F** y busca:

   ```text
   A Yellow shirt of size M with a price of $12.00 was added successfully.
   ```
10. En **Output**, haz clic en **Clear All**.
11. En Microsoft Edge, haz clic en el primer enlace **Delete** de la tabla **Shirts in stock**.
12. En **Output**, presiona **Ctrl+F** y busca:

    ```text
    A shirt with id 1 was removed successfully.
    ```
13. En la barra de direcciones de Microsoft Edge, escribe `http://localhost:[port]/Shirt/Delete/-1` y presiona **Enter**. **Nota:** Deberás continuar la ejecución cuando la aplicación se detenga por la excepción. El navegador mostrará la página **DeveloperException**.
14. Cierra Microsoft Edge.

### Tarea 5: Ejecutar la aplicación en producción

1. En la barra de herramientas de Visual Studio, abre la flecha junto a **Start Debugging** y selecciona **Production**.
2. Selecciona **Debug > Start without Debugging**.
3. En la barra de direcciones de Microsoft Edge, escribe `http://localhost:[port]/Shirt/Delete/-1` y presiona **Enter**. **Nota:** Se mostrará el contenido de `error.html`, situado en `ShirtStoreWebsite/wwwroot`.
4. Cierra Microsoft Edge.
5. En el Explorador de archivos, ve a `D:\Allfiles\Mod10\Labfiles\01_ShirtStore_begin\ShirtStoreWebsite` y abre `shirt_store_logs-XXXXXXXX.txt` con doble clic. **Nota:** Examina el seguimiento de pila de `ArgumentNullException`.
6. Cierra la ventana de Bloc de notas de `shirt_store_logs-XXXXXXXX`.
7. En Visual Studio, selecciona **Debug > Start without Debugging**.
8. En Microsoft Edge, selecciona **M** en **Size**.
9. Selecciona **Yellow** en **Color**.
10. En **Price**, escribe `10`.
11. En **Tax**, escribe `1.2`.
12. Haz clic en **Add Shirt to Stock**. **Nota:** La camisa nueva aparecerá al final de la tabla **Shirts in Stock**.
13. Cierra Microsoft Edge.
14. En el Explorador de archivos, vuelve a `D:\Allfiles\Mod10\Labfiles\01_ShirtStore_begin\ShirtStoreWebsite` y abre `shirt_store_logs-XXXXXXXX.txt`. **Nota:** El archivo no contiene otro mensaje porque la operación se realizó correctamente y no hubo errores.
15. Cierra la ventana de Bloc de notas.
16. En Visual Studio, selecciona **File > Exit**.

**Resultado:** Configuraste distintos niveles de registro por entorno, con salida en consola o archivo, y creaste un sustituto simulado mediante un framework de pruebas.
