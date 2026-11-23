# Módulo 4: Desarrollo de controladores

# Laboratorio: Desarrollo de controladores

## Escenario

Se le ha pedido agregar controladores a una aplicación nueva. Los controladores deben incluir acciones que devuelvan una vista; agregue también una acción que devuelva la fotografía como un archivo .jpg para mostrarla en una página web, y una acción que redirija a otra acción en otro controlador. Además, se le ha pedido configurar las rutas de diversas maneras.

Los integrantes de su equipo de desarrollo son nuevos en ASP.NET Core MVC y les resulta confuso el uso de las acciones de los controladores. Por lo tanto, debe ayudarlos agregando un componente que muestre los parámetros de las acciones en un archivo externo cada vez que se ejecute una acción. Para lograrlo, agregará un filtro de acción.

## Ejercicio 1: Agregar controladores y acciones a una aplicación MVC

### Escenario

En este ejercicio, creará el controlador MVC que administra las operaciones del usuario. También agregará las siguientes acciones:

- `Index`. Esta acción muestra la vista `Index`.
- `Display`. Esta acción recibe un ID para encontrar un único objeto `City`. Pasa el objeto `City` a la vista `Display`.
- `GetImage`. Esta acción devuelve la imagen de la fotografía desde el servicio como un archivo .jpg.

Las tareas principales de este ejercicio son las siguientes:

- Agregar controladores a una aplicación MVC.
- Agregar acciones a un controlador.
- Modificar acciones para que reciban un parámetro.
- Modificar una acción para que redirija a otra acción en otro controlador.
- Usar un servicio.
- Almacenar el resultado en una propiedad de `ViewBag`.
- Ejecutar la aplicación.

### Tarea 1: Agregar controladores a una aplicación MVC

1. Vaya a `D:\Allfiles\Mod04\Labfiles\01_WorldJourney_begin` y haga doble clic en `WorldJourney.sln`.

   **Nota:** Si aparece el cuadro de diálogo `Security Warning for WorldJourney`, compruebe que la casilla `Ask me for every project in this solution` esté desmarcada y, a continuación, haga clic en `OK`.

2. En `Solution Explorer`, haga clic con el botón derecho en `WorldJourney`, señale `Add` y seleccione `New Folder`.
3. En el cuadro `NewFolder`, escriba `Controllers` y presione Entrar.
4. En la ventana `WorldJourney - Microsoft Visual Studio`, en `Solution Explorer`, haga clic con el botón derecho en la carpeta `Controllers`, señale `Add` y seleccione `Controller…`.
5. En el cuadro de diálogo `Add New Scaffolded Item`, haga clic en `MVC Controller - Empty` y, a continuación, en `Add`.
6. En el cuadro de diálogo `Add New Item`, en el cuadro de texto `Name:`, escriba `HomeController` y haga clic en `Add` (si este es el primer controlador, ya tendrá ese nombre).
7. En la ventana `WorldJourney - Microsoft Visual Studio`, en `Solution Explorer`, haga clic con el botón derecho en la carpeta `Controllers`, señale `Add` y seleccione `Controller…`.
8. En el cuadro de diálogo `Add New Scaffolded Item`, haga clic en `MVC Controller - Empty` y, a continuación, en `Add`.
9. En el cuadro de diálogo `Add New Item`, en el cuadro de texto `Name:`, escriba `CityController` y haga clic en `Add`.

### Tarea 2: Agregar acciones a un controlador

1. En el bloque de código de la clase `CityController`, dentro del bloque de código de la acción `Index`, localice el siguiente código:

   ```csharp
   return View();
   ```

2. Coloque el cursor antes del código localizado y escriba el siguiente código:

   ```csharp
   ViewData["Page"] = "Search city";
   ```

3. En la ventana de código de `CityController`, asegúrese de que el cursor esté al final del bloque de código de la acción `Index`, presione Entrar y escriba el siguiente código:

   ```csharp
   public IActionResult Details()
   {
   }
   ```

4. En el bloque de código de la acción `Details`, escriba el siguiente código:

   ```csharp
   ViewData["Page"] = "Selected city";
   City city = null;
   if (city == null)
   {
        return NotFound();
   }

   return View(city);
   ```

5. Compruebe que Visual Studio haya agregado automáticamente una instrucción `using` para el espacio de nombres `WorldJourney.Models` como resultado de hacer referencia al tipo `City`:

   ```csharp
   using WorldJourney.Models;
   ```

6. En la ventana de código de `CityController`, asegúrese de que el cursor esté al final del bloque de código de la acción `Details`, presione Entrar y escriba el siguiente código:

   ```csharp
   public IActionResult GetImage()
   {
   }
   ```

7. En el bloque de código de la acción `GetImage`, escriba el siguiente código:

   ```csharp
   ViewData["Message"] = "display Image";
   City requestedCity = null;
   if (requestedCity != null)
   {
        string fullPath = "";
        FileStream fileOnDisk = new FileStream(fullPath, FileMode.Open);
        byte[] fileBytes;
        using (BinaryReader br = new BinaryReader(fileOnDisk))
        {
             fileBytes = br.ReadBytes((int)fileOnDisk.Length);
        }
        return File(fileBytes, requestedCity.ImageMimeType);
   }
   else
   {
        return NotFound();
   }
   ```

### Tarea 3: Modificar acciones para que reciban un parámetro

1. En el bloque de código de la clase `CityController`, seleccione el siguiente código:

   ```csharp
   public IActionResult Details()
   ```

2. Agregue un parámetro reemplazando el código seleccionado por el siguiente:

   ```csharp
   public IActionResult Details(int? id)
   ```

3. En el bloque de código de la clase `CityController`, seleccione el siguiente código:

   ```csharp
   public IActionResult GetImage()
   ```

4. Agregue un parámetro reemplazando el código seleccionado por el siguiente:

   ```csharp
   public IActionResult GetImage(int? cityId)
   ```

### Tarea 4: Modificar una acción para que redirija a otra acción en otro controlador

1. En la ventana `WorldJourney - Microsoft Visual Studio`, en `Solution Explorer`, expanda `Controllers` y haga clic en `HomeController.cs`.
2. En la ventana de código de `HomeController`, dentro del bloque de código de la acción `Index`, seleccione el siguiente código:

   ```csharp
   return View();
   ```

3. Reemplace el código seleccionado por el siguiente:

   ```csharp
   return RedirectToAction("Index", "City");
   ```

### Tarea 5: Usar un servicio

1. En la ventana `WorldJourney - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Controllers`, haga clic en `CityController.cs`.
2. En el bloque de código de la clase `CityController`, localice el siguiente código:

   ```csharp
   public IActionResult Index()
   ```

3. Coloque el cursor del mouse antes del código localizado, escriba el siguiente código y presione Entrar:

   ```csharp
   private IData _data;
   private IWebHostEnvironment _environment;

   public CityController(IData data, IWebHostEnvironment environment)
   {
        _data = data;
        _environment = environment;
        _data.CityInitializeData();
   }
   ```

4. En el bloque de código de la acción `Details`, seleccione el siguiente código:

   ```csharp
   City city = null;
   ```

5. Reemplace el código seleccionado por el siguiente:

   ```csharp
   City city = _data.GetCityById(id);
   ```

6. En el bloque de código de la acción `GetImage`, seleccione el siguiente código:

   ```csharp
   City requestedCity = null;
   ```

7. Reemplace el código seleccionado por el siguiente:

   ```csharp
   City requestedCity = _data.GetCityById(cityId);
   ```

8. En el bloque de código de la acción `GetImage`, seleccione el siguiente código:

   ```csharp
   string fullPath = "";
   ```

9. Reemplace el código seleccionado por el siguiente:

   ```csharp
   string webRootpath = _environment.WebRootPath;
   string folderPath = "\\images\\";
   string fullPath = webRootpath + folderPath + requestedCity.ImageName;
   ```

### Tarea 6: Almacenar el resultado en una propiedad de ViewBag

1. En el bloque de código de la clase `CityController`, dentro del bloque de código de la acción `Details`, localice el siguiente código:

   ```csharp
   return View(city);
   ```

2. Coloque el cursor del mouse antes del código localizado y escriba el siguiente código:

   ```csharp
   ViewBag.Title = city.CityName;
   ```

### Tarea 7: Ejecutar la aplicación

1. En la ventana `WorldJourney - Microsoft Visual Studio`, en el menú `File`, haga clic en `Save All`.
2. En la ventana `WorldJourney - Microsoft Visual Studio`, en el menú `Debug`, haga clic en `Start Without Debugging`.

   **Nota:** El navegador muestra el resultado de la acción `Index` dentro del controlador `City`.

3. En Microsoft Edge, sobre la imagen de la Tierra, haga clic en la zona de Londres. Observe la flecha roja en el centro de la imagen de la Tierra (la zona activa del mapa no es muy precisa: ¡es posible que deba hacer clic sobre Escocia o Irlanda!).

   **Nota:** El navegador muestra el resultado de la acción `Details` dentro del controlador `City`.

4. En Microsoft Edge, haga clic en `Close`.

**Resultados:** Después de completar este ejercicio, habrá creado controladores MVC que implementan acciones comunes para la clase de modelo `City` en la aplicación.

## Ejercicio 2: Configurar rutas mediante la tabla de enrutamiento

### Escenario

Una prioridad importante del diseño de la aplicación es que los visitantes puedan localizar ciudades de forma fácil y lógica. Para implementar estas prioridades, se le ha pedido configurar rutas mediante la tabla de enrutamiento, que permite introducir direcciones URL fáciles de usar para acceder a las ciudades.

Las tareas principales de este ejercicio son las siguientes:

- Agregar un controlador con una acción.
- Ejecutar la aplicación.
- Registrar rutas nuevas en la tabla de enrutamiento.
- Ejecutar la aplicación y comprobar que la ruta nueva funciona.

### Tarea 1: Agregar un controlador con una acción

1. En la ventana `WorldJourney - Microsoft Visual Studio`, en `Solution Explorer`, haga clic con el botón derecho en la carpeta `Controllers`, señale `Add` y seleccione `Controller…`.
2. En el cuadro de diálogo `Add New Scaffolded Item`, haga clic en `MVC controller - Empty` y, a continuación, en `Add`.
3. En el cuadro de diálogo `Add New Item`, en el cuadro de texto `Name:`, escriba `TravelerController` y haga clic en `Add`.
4. En el bloque de código de la clase `TravelerController`, seleccione el siguiente código:

   ```csharp
   public IActionResult Index()
   {
        return View();
   }
   ```

5. Reemplace el código seleccionado por el siguiente:

   ```csharp
   public IActionResult Index(string name)
   {
        ViewBag.VisitorName = name;
        return View();
   }
   ```

### Tarea 2: Ejecutar la aplicación

1. En la ventana `WorldJourney - Microsoft Visual Studio`, en el menú `File`, haga clic en `Save All`.
2. En la ventana `WorldJourney - Microsoft Visual Studio`, en el menú `Debug`, haga clic en `Start Without Debugging`.
3. En Microsoft Edge, en la barra de direcciones, escriba `http://localhost:[port]/Traveler/Index` y presione Entrar.

   **Nota:** En la siguiente tarea registrará una ruta nueva en la tabla de enrutamiento. Entonces, no necesitará introducir manualmente la URL relativa `Traveler/Index` en la barra de direcciones.

4. En Microsoft Edge, haga clic en `Close`.

### Tarea 3: Registrar rutas nuevas en la tabla de enrutamiento

1. En la ventana `WorldJourney - Microsoft Visual Studio`, en `Solution Explorer`, haga clic en `Program.cs`.
2. Dentro del archivo `Program.cs`, seleccione el siguiente código:

   ```csharp
   app.MapDefaultControllerRoute();
   ```

3. Reemplace el código seleccionado por el siguiente:

   ```csharp
   app.MapControllerRoute(
       "TravelerRoute",
       "{controller}/{action}/{name}",
       new { controller = "Traveler", action = "Index", name = "Katie Bruce" },
       new { name = "[A-Za-z ]+" }
   );
   app.MapControllerRoute(
       "defaultRoute",
       "{controller}/{action}/{id?}",
       new { controller = "Home", action = "Index" },
       new { id = "[0-9]+" }
   );
   ```

   **Nota:** Puede reemplazar el nombre predeterminado `Katie Bruce` por el suyo.

### Tarea 4: Ejecutar la aplicación y comprobar que la ruta nueva funciona

1. En la ventana `WorldJourney - Microsoft Visual Studio`, en el menú `File`, haga clic en `Save All`.
2. En la ventana `WorldJourney - Microsoft Visual Studio`, en el menú `Debug`, haga clic en `Start Without Debugging`.

   **Nota:** El nombre «Katie Bruce» que aparece en el título proviene de la nueva ruta `TravelerRoute`, registrada en la tabla de enrutamiento.

3. En Microsoft Edge, haga clic en `Close`.

**Resultados:** Después de completar este ejercicio, podrá registrar rutas personalizadas nuevas en la canalización de solicitudes para los controladores de la aplicación.

## Ejercicio 3: Configurar rutas mediante atributos

### Escenario

Además de configurar rutas mediante la tabla de enrutamiento, se le ha pedido configurar rutas mediante atributos para permitir también la introducción de direcciones URL fáciles de usar. En este ejercicio utilizará rutas personalizadas por medio de atributos.

### Tarea 1: Aplicar rutas personalizadas a un controlador mediante atributos

1. En la ventana `WorldJourney - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Controllers`, haga clic en `CityController.cs`.
2. En el bloque de código de la acción `Index`, localice el siguiente código:

   ```csharp
   public IActionResult Index()
   ```

3. Coloque el cursor antes del código localizado, presione Entrar y escriba el siguiente código:

   ```csharp
   [Route("WorldJourney")]
   ```

4. En el bloque de código de la acción `Details`, localice el siguiente código:

   ```csharp
   public IActionResult Details(int? id)
   ```

5. Coloque el cursor antes del código localizado, presione Entrar y escriba el siguiente código:

   ```csharp
   [Route("CityDetails/{id?}")]
   ```

### Tarea 2: Ejecutar la aplicación y comprobar que las rutas nuevas funcionan

1. En la ventana `WorldJourney - Microsoft Visual Studio`, en el menú `File`, haga clic en `Save All`.
2. En la ventana `WorldJourney - Microsoft Visual Studio`, en el menú `Debug`, haga clic en `Start Without Debugging`.
3. En Microsoft Edge, haga clic con el botón derecho en la página y seleccione `View page source`.
4. Localice la línea de HTML (cerca del final de la página):

   ```html
   <div class="button-center">
   ```

5. Busque la etiqueta de anclaje en la línea siguiente y compruebe que el atributo `href` sea `/WorldJourney`:

   ```html
   <a href="/WorldJourney">Go Next</a>
   ```

6. Cierre la pestaña del código fuente de la página.
7. En la vista normal de Microsoft Edge, haga clic en el botón `Go Next` y compruebe que le lleve a la vista `WorldJourney`.

   **Nota:** Como resultado de aplicar una ruta personalizada a `CityController` en la acción `Index` mediante atributos, debe aparecer `http://localhost:[port]/WorldJourney` en la barra de direcciones.

8. En Microsoft Edge, haga clic con el botón derecho en cualquier lugar de la página fuera del gráfico grande y seleccione `View page source`.
9. Busque el elemento `map` en la página, que define las zonas activas del gráfico grande.
10. Compruebe que el valor del atributo `href` de la zona activa de Londres sea `/CityDetails/2`.
11. Cierre la pestaña del código fuente de la página.
12. En Microsoft Edge, sobre la imagen de la Tierra, haga clic en la zona de Londres (junto a la flecha roja en el centro de la imagen) y compruebe que le lleve a la página de detalles de Londres.

    **Nota:** Como resultado de aplicar una ruta personalizada a `CityController` en la acción `Details` mediante atributos, debe aparecer `http://localhost:[port]/CityDetails/2` en la barra de direcciones.

13. En Microsoft Edge, haga clic en `Close`.

**Resultados:** Después de completar este ejercicio, habrá agregado rutas personalizadas al controlador `City` mediante el atributo `Route`.

## Ejercicio 4: Agregar un filtro de acción

### Escenario

Su equipo de desarrollo es nuevo en ASP.NET Core MVC y tiene dificultades para pasar los parámetros correctos a los controladores y las acciones. Debe implementar un componente que muestre los nombres de los controladores y de las acciones en un archivo externo para ayudar con este problema. En este ejercicio creará un filtro de acción para este propósito.

Las tareas principales de este ejercicio son las siguientes:

- Agregar una clase de filtro de acción.
- Agregar un controlador para el evento `OnActionExecuting`.
- Agregar un controlador para el evento `OnActionExecuted`.
- Agregar un controlador para el evento `OnResultExecuted`.
- Aplicar el filtro de acción a la acción del controlador.
- Ejecutar la aplicación y comprobar que el filtro nuevo funciona.

### Tarea 1: Agregar una clase de filtro de acción

1. En `Solution Explorer`, haga clic con el botón derecho en `WorldJourney`, señale `Add` y seleccione `New Folder`.
2. En el cuadro `NewFolder`, escriba `Filters` y presione Entrar.
3. En la ventana `WorldJourney - Microsoft Visual Studio`, en `Solution Explorer`, haga clic con el botón derecho en `Filters`, señale `Add` y seleccione `Class…`.
4. En el cuadro de diálogo `Add New Item -- WorldJourney`, en el cuadro `Name`, escriba `LogActionFilterAttribute` y haga clic en `Add`.
5. En `LogActionFilterAttribute`, localice el siguiente código:

   ```csharp
   using System.Threading.Tasks;
   ```

6. Asegúrese de que el cursor esté al final del espacio de nombres `using System.Threading.Tasks`, presione Entrar y escriba el siguiente código:

   ```csharp
   using System.IO;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.AspNetCore.Mvc.Filters;
   ```

7. En la ventana de código de la clase `LogActionFilterAttribute`, localice el siguiente código:

   ```csharp
   public class LogActionFilterAttribute
   ```

8. Agregue `ActionFilterAttribute` como clase base para que se vea de la siguiente manera:

   ```csharp
   public class LogActionFilterAttribute : ActionFilterAttribute
   ```

9. Compruebe que Visual Studio haya agregado automáticamente una instrucción `using` para el espacio de nombres `Microsoft.AspNetCore.Mvc.Filters`.
10. En el bloque de código de la clase `LogActionFilterAttribute`, presione Entrar y escriba el siguiente código:

    ```csharp
    private IWebHostEnvironment _environment;
    private string _contentRootPath;
    private string _logPath;
    private string _fileName;
    private string _fullPath;

    public LogActionFilterAttribute(IWebHostEnvironment environment)
    {
         _environment = environment;
         _contentRootPath = _environment.ContentRootPath;
         _logPath = _contentRootPath + "\\LogFile\\";
         _fileName = $"log {DateTime.Now.ToString("MM-dd-yyyy-H-mm")}.txt";
         _fullPath = _logPath + _fileName;
    }
    ```

### Tarea 2: Agregar un controlador para el evento OnActionExecuting

1. En el bloque de código de la clase `LogActionFilterAttribute`, asegúrese de que el cursor esté al final del bloque de código del constructor `LogActionFilterAttribute`, presione Entrar y escriba el siguiente código:

   ```csharp
   public override void OnActionExecuting(ActionExecutingContext filterContext)
   {
   }
   ```

2. En el bloque de código del método `OnActionExecuting`, presione Entrar e inserte el siguiente código:

   ```csharp
   Directory.CreateDirectory(_logPath);
   string actionName = filterContext.ActionDescriptor.RouteValues["action"];
   string controllerName = filterContext.ActionDescriptor.RouteValues["controller"];
   using (FileStream fs = new FileStream(_fullPath, FileMode.Create))
   {
        using (StreamWriter sw = new StreamWriter(fs))
        {
             sw.WriteLine($"The action {actionName} in {controllerName} controller started, event fired: OnActionExecuting");
        }
   }
   ```

### Tarea 3: Agregar un controlador para el evento OnActionExecuted

1. En el bloque de código de la clase `LogActionFilterAttribute`, asegúrese de que el cursor esté al final del bloque de código del método `OnActionExecuting`, presione Entrar y escriba el siguiente código:

   ```csharp
   public override void OnActionExecuted(ActionExecutedContext filterContext)
   {
   }
   ```

2. En el bloque de código del método `OnActionExecuted`, presione Entrar, escriba el siguiente código y presione Entrar:

   ```csharp
   string actionName = filterContext.ActionDescriptor.RouteValues["action"];
   string controllerName = filterContext.ActionDescriptor.RouteValues["controller"];
   using (FileStream fs = new FileStream(_fullPath, FileMode.Append))
   {
        using (StreamWriter sw = new StreamWriter(fs))
        {
             sw.WriteLine($"The action {actionName} in {controllerName} controller finished, event fired: OnActionExecuted");
        }
   }
   ```

### Tarea 4: Agregar un controlador para el evento OnResultExecuted

1. En el bloque de código de la clase `LogActionFilterAttribute`, asegúrese de que el cursor esté al final del bloque de código del método `OnActionExecuted`, presione Entrar y escriba el siguiente código:

   ```csharp
   public override void OnResultExecuted(ResultExecutedContext filterContext)
   {
   }
   ```

2. En el bloque de código del método `OnResultExecuted`, presione Entrar, escriba el siguiente código y presione Entrar:

   ```csharp
   string actionName = filterContext.ActionDescriptor.RouteValues["action"];
   string controllerName = filterContext.ActionDescriptor.RouteValues["controller"];
   ViewResult result = (ViewResult)filterContext.Result;
   using (FileStream fs = new FileStream(_fullPath, FileMode.Append))
   {
        using (StreamWriter sw = new StreamWriter(fs))
        {
             sw.WriteLine($"The action {actionName} in {controllerName} controller has the following viewData : {result.ViewData.Values.FirstOrDefault()}, event fired: OnResultExecuted");
        }
   }
   ```

### Tarea 5: Aplicar el filtro de acción a la acción del controlador

1. En la ventana `WorldJourney - Microsoft Visual Studio`, en `Solution Explorer`, haga clic en `Program.cs`.
2. En la ventana de código de `Program.cs`, localice el siguiente código:

   ```csharp
   builder.Services.AddSingleton<IData, Data>();
   ```

3. Coloque el cursor del mouse después del código localizado y agregue lo siguiente:

   ```csharp
   builder.Services.AddScoped<LogActionFilterAttribute>();
   ```

4. Compruebe que Visual Studio haya agregado automáticamente una instrucción `using` para el espacio de nombres `WorldJourney.Filters`.
5. En la ventana `WorldJourney - Microsoft Visual Studio`, en `Solution Explorer`, expanda `Controllers` y haga clic en `CityController.cs`.
6. En el bloque de código de la clase `CityController`, localice el siguiente código:

   ```csharp
   [Route("WorldJourney")]
   ```

7. Coloque el cursor del mouse antes del código localizado, presione Entrar y escriba el siguiente código:

   ```csharp
   [ServiceFilter(typeof(LogActionFilterAttribute))]
   ```

### Tarea 6: Ejecutar la aplicación y comprobar que el filtro nuevo funciona

1. En la ventana `WorldJourney - Microsoft Visual Studio`, en el menú `File`, haga clic en `Save All`.
2. En la ventana `WorldJourney - Microsoft Visual Studio`, en el menú `Debug`, haga clic en `Start Without Debugging`.
3. En Microsoft Edge, haga clic en `Go Next`.
4. En Microsoft Edge, sobre la imagen de la Tierra, haga clic en la zona de Londres (junto a la flecha roja).
5. Haga clic en `Go Back`.
6. En Microsoft Edge, haga clic en `Close`.
7. En la ventana `WorldJourney - Microsoft Visual Studio`, en el menú `File`, haga clic en `Exit`.
8. Vaya a `D:\Allfiles\Mod04\Labfiles\01_WorldJourney_begin\LogFile`. Debe haber uno o más archivos de registro con nombres de la forma `log [date] .txt`.
9. Abra uno de los archivos de registro para comprobar que se hayan registrado los eventos de las acciones de los controladores.

   **Nota:** Los archivos de registro contienen la salida del filtro de acción.

**Resultados:** Después de completar este ejercicio, habrá creado una clase de filtro de acción que registra los detalles de las acciones, los controladores y los parámetros en un archivo de registro externo cada vez que se llama a una acción.
