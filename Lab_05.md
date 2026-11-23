# Módulo 5: Desarrollo de vistas

# Laboratorio: Desarrollo de vistas

## Escenario

Para construir la interfaz de usuario de una aplicación web de ciudades, su equipo de desarrollo decidió agregar vistas. Se le ha pedido crear las vistas para representar una respuesta en un navegador.

## Ejercicio 1: Agregar vistas a una aplicación MVC

### Escenario

Para construir la interfaz de usuario de una aplicación web, deben agregarse vistas a la aplicación. En este ejercicio agregará dos vistas a la aplicación web: `Index` y `Details`. La vista `Index` mostrará una lista de ciudades y la vista `Details` mostrará los detalles de una ciudad.

Las tareas principales de este ejercicio son las siguientes:

- Agregar una vista para mostrar todas las ciudades.
- Ejecutar la aplicación.
- Agregar una vista para mostrar los datos de una ciudad.
- Agregar un vínculo `Back` a una vista.
- Agregar el nombre de cada ciudad como vínculo.
- Ejecutar la aplicación.

### Tarea 1: Agregar una vista para mostrar todas las ciudades

1. Vaya a `D:\Allfiles\Mod05\Labfiles\01_CitiesWebsite_begin` y haga doble clic en `CitiesWebsite.sln`.

   **Nota:** Si aparece el cuadro de diálogo `Security Warning for CitiesWebsite`, compruebe que la casilla `Ask me for every project in this solution` esté desmarcada y haga clic en `OK`.

2. En la ventana `How do you want to open this file?`, seleccione `Visual Studio 2022`, asegúrese de que la casilla `Always use this app to open .sln files` esté marcada y haga clic en `OK`.
3. En la ventana `CitiesWebsite - Microsoft Visual Studio`, en `Solution Explorer`, expanda `Services` y haga clic en `CityProvider.cs`.
4. En la ventana de código de `CityProvider.cs`, localice el siguiente código:

   ```csharp
   public CityProvider()
   {

   }
   ```

5. Coloque el cursor dentro del bloque de código del constructor `CityProvider` y escriba el siguiente código:

   ```csharp
   _cities = CityInitializer();
   ```

6. En la ventana `CitiesWebsite - Microsoft Visual Studio`, en `Solution Explorer`, expanda `Models` y haga clic en `City.cs`.
7. En la ventana de código de `City.cs`, localice el siguiente código:

   ```csharp
   public City(string country, string cityName, string timeZone, CityPopulation population)
   {

   }
   ```

8. Coloque el cursor dentro del bloque de código del constructor `City` y escriba el siguiente código:

   ```csharp
   Country = country;
   Name = cityName;
   TimeZone = timeZone;
   Population = population;
   ```

9. En la ventana `CitiesWebsite - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Models`, haga clic en `CityPopulation.cs`.
10. En la ventana de código de `CityPopulation.cs`, localice el siguiente código:

    ```csharp
    public CityPopulation(int year, int city, int urban, int metro)
    {

    }
    ```

11. Coloque el cursor dentro del bloque de código del constructor `CityPopulation` y escriba el siguiente código:

    ```csharp
    Year = year;
    City = city;
    Urban = urban;
    Metro = metro;
    ```

12. En la ventana `CitiesWebsite - Microsoft Visual Studio`, en `Solution Explorer`, expanda `Controllers` y haga clic en `CityController.cs`.
13. En la ventana de código de `CityController.cs`, localice el siguiente código:

    ```csharp
    public class CityController : Controller
    {
    ```

14. Coloque el cursor al final del código localizado, presione Entrar y escriba el siguiente código:

    ```csharp
    private ICityProvider _cities;
    ```

15. En la ventana de código de `CityController.cs`, seleccione el siguiente código del constructor `CityController`:

    ```csharp
    public CityController()
    {

    }
    ```

16. Reemplace el código seleccionado por el siguiente:

    ```csharp
    public CityController(ICityProvider cities)
    {
        _cities = cities;
    }
    ```

17. En la ventana de código de `CityController.cs`, localice el siguiente código:

    ```csharp
    public IActionResult ShowCities()
    {
    ```

18. Coloque el cursor después del signo `{` (llave de apertura), presione Entrar y escriba el siguiente código:

    ```csharp
    ViewBag.Cities = _cities;
    ```

19. En la ventana de código de `CityController.cs`, haga clic con el botón derecho en el siguiente código y después haga clic en `Add View…`:

    ```csharp
    public IActionResult ShowCities()
    ```

20. En el cuadro de diálogo `Add New Scaffolded Item`, asegúrese de que esté seleccionada la plantilla `Razor View` (no la plantilla `Empty`) y haga clic en `Add`.
21. En el cuadro de diálogo `Add Razor View`, asegúrese de que el cuadro de texto `View name` contenga el nombre `ShowCities`.
22. En el cuadro de diálogo `Add Razor View`, asegúrese de que las casillas `Create as a partial view` y `Use a layout page` estén desmarcadas y haga clic en `Add`.
23. En la ventana de código de `ShowCities.cshtml`, localice el siguiente código:

    ```html
    <title>ShowCities</title>
    ```

24. Coloque el cursor al final del código localizado, presione Entrar y escriba el siguiente código:

    ```html
    <link rel="stylesheet" type="text/css" href="~/css/style.css" />
    ```

25. En la ventana de código de `ShowCities.cshtml`, dentro del elemento `BODY`, escriba el siguiente código:

    ```html
    <h1>Select City</h1>
    @foreach (var item in ViewBag.Cities)
    {
        <p>@item.Key</p>
    }
    ```

### Tarea 2: Ejecutar la aplicación

1. En la ventana `CitiesWebsite - Microsoft Visual Studio`, en el menú `File`, haga clic en `Save All`.
2. En la ventana `CitiesWebsite - Microsoft Visual Studio`, en el menú `Debug`, haga clic en `Start Without Debugging`.

   **Nota:** El navegador muestra el título `Select City` y, debajo, una lista de ciudades: Madrid, Londres y París.

3. En Microsoft Edge, haga clic en `Close`.

### Tarea 3: Agregar una vista para mostrar los datos de una ciudad

1. En la ventana `CitiesWebsite - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Controllers`, haga clic en `CityController.cs`.
2. En la ventana de código de `CityController.cs`, seleccione el siguiente código:

   ```csharp
   public IActionResult ShowDataForCity()
   ```

3. Reemplace el código seleccionado por el siguiente:

   ```csharp
   public IActionResult ShowDataForCity(string cityName)
   ```

4. Coloque el cursor después del signo `{` (llave de apertura), presione Entrar y escriba el siguiente código:

   ```csharp
   ViewBag.City = _cities[cityName];
   ```

5. En la ventana de código de `CityController.cs`, haga clic con el botón derecho en cualquier lugar del método `ShowDataForCity` y, a continuación, haga clic en `Add View…`.
6. En el cuadro de diálogo `Add New Scaffolded Item`, asegúrese de que esté seleccionada la plantilla `Razor View` (no la plantilla `Empty`) y haga clic en `Add`.
7. En el cuadro de diálogo `Add Razor View`, asegúrese de que el valor del cuadro de texto `Name:` sea `ShowDataForCity`.
8. En el cuadro de diálogo `Add Razor View`, asegúrese de que las casillas `Create as a partial view` y `Use a layout page` estén desmarcadas y haga clic en `Add`.
9. En la ventana de código de `ShowDataForCity.cshtml`, localice el siguiente código:

   ```html
   <title>ShowDataForCity</title>
   ```

10. Coloque el cursor al final del código localizado, presione Entrar y escriba el siguiente código:

    ```html
    <link rel="stylesheet" type="text/css" href="~/css/style.css" />
    ```

11. En la ventana de código de `ShowDataForCity.cshtml`, dentro del elemento `BODY`, escriba el siguiente código:

    ```html
    <div>
        <h2>@ViewBag.City.Name</h2>
        <p>Country: @ViewBag.City.Country</p>
        <p>Time zone: @ViewBag.City.TimeZone</p>
        <span><img src="@Url.Action("GetImage", new { cityName = ViewBag.City.Name })" /></span>
    </div>
    ```

12. En la ventana `CitiesWebsite - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Controllers`, haga clic en `CityController.cs`.
13. En la ventana de código de `CityController.cs`, seleccione el siguiente código:

    ```csharp
    return Content(cityName);
    ```

14. Reemplace el código seleccionado por el siguiente:

    ```csharp
    return File($@"images\{cityName}.jpg", "image/jpeg");
    ```

### Tarea 4: Agregar vínculos a las vistas mediante asistentes de etiquetas

1. En la ventana `CitiesWebsite - Microsoft Visual Studio`, en `Solution Explorer`, haga clic con el botón derecho en `Views`, señale `Add` y después haga clic en `New Item…`.
2. En el cuadro de diálogo `Add New Item -- CitiesWebsite`, elija `Razor View Imports` y haga clic en `Add`.
3. En la ventana de código de `_ViewImports.cshtml`, escriba el siguiente código:

   ```csharp
   @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
   ```

4. En la ventana `CitiesWebsite - Microsoft Visual Studio`, en `Solution Explorer`, expanda `Views`, expanda `City` y haga clic en `ShowDataForCity.cshtml`.
5. En la ventana de código de `ShowDataForCity.cshtml`, localice el siguiente código:

   ```html
   <span><img src="@Url.Action("GetImage", new { cityName = ViewBag.City.Name })" /></span>
   ```

6. Coloque el cursor al final del código localizado, presione Entrar y escriba el siguiente código:

   ```html
   <a asp-action="ShowCities">Back</a>
   ```

7. En la ventana `CitiesWebsite - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Views` y `City`, haga clic en `ShowCities.cshtml`.
8. En la ventana de código de `ShowCities.cshtml`, seleccione el siguiente código:

   ```html
   <p>@item.Key</p>
   ```

9. Reemplace el código seleccionado por el siguiente:

   ```html
   <h2>
       <a asp-action="ShowDataForCity" asp-route-cityname="@item.Key">@item.Key</a>
   </h2>
   ```

### Tarea 5: Ejecutar la aplicación

1. En la ventana `CitiesWebsite - Microsoft Visual Studio`, en el menú `File`, haga clic en `Save All`.
2. En la ventana `CitiesWebsite - Microsoft Visual Studio`, en el menú `Debug`, haga clic en `Start Without Debugging`.
3. En Microsoft Edge, haga clic en `London`.

   **Nota:** El navegador muestra el nombre de la ciudad, sus detalles, un minimapa y un vínculo `Back`.

4. En Microsoft Edge, haga clic en `Back`.
5. En Microsoft Edge, haga clic en `Close`.

**Resultado:** Al final de este ejercicio, habrá agregado vistas a una aplicación MVC, pasado datos de un controlador a una vista mediante `ViewBag` y navegado entre páginas mediante asistentes.

## Ejercicio 2: Agregar una vista parcial

### Escenario

Se le ha pedido mostrar la población de cada ciudad. Para hacerlo, se le ha pedido agregar una vista parcial. En este ejercicio creará una vista parcial y la incorporará a la vista `ShowDataForCity`.

Las tareas principales de este ejercicio son las siguientes:

- Agregar una vista parcial.
- Usar la vista parcial en la vista `ShowDataForCity`.
- Ejecutar la aplicación.

### Tarea 1: Agregar una vista parcial

1. En la ventana `CitiesWebsite - Microsoft Visual Studio`, en `Solution Explorer`, haga clic con el botón derecho en `Views`, señale `Add` y haga clic en `New Folder`.
2. En el cuadro `NewFolder`, escriba `Shared` y presione Entrar.
3. En `Solution Explorer`, haga clic con el botón derecho en `Shared`, señale `Add` y haga clic en `View…`.
4. En el cuadro de diálogo `Add New Scaffolded Item`, asegúrese de que esté seleccionada la plantilla `Razor View` (no la plantilla `Empty`) y haga clic en `Add`.
5. En el cuadro de diálogo `Add Razor View`, en el cuadro de texto `Name:`, escriba `_CityPopulation`.
6. En el cuadro de diálogo `Add Razor View`, asegúrese de que la casilla `Create as a partial view` esté marcada y haga clic en `Add`.
7. En la ventana de código de `_CityPopulation.cshtml`, reemplace todo el contenido por el siguiente código:

   ```html
   @inject CitiesWebsite.Services.ICityFormatter cityFormatter

   @{
       CitiesWebsite.Models.CityPopulation population = ViewBag.City.Population;
   }

   <h3>City Population (@population.Year)</h3>
   <p>City: @cityFormatter.GetFormattedPopulation(@population.City)</p>
   <p>Urban: @cityFormatter.GetFormattedPopulation(@population.Urban)</p>
   <p>Metro: @cityFormatter.GetFormattedPopulation(@population.Metro)</p>
   ```

### Tarea 2: Usar la vista parcial en la vista ShowDataForCity

1. En la ventana `CitiesWebsite - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Views` y `City`, haga clic en `ShowDataForCity.cshtml`.
2. En la ventana de código de `ShowDataForCity.cshtml`, localice el siguiente código:

   ```html
   <span><img src="@Url.Action("GetImage", new { cityName = ViewBag.City.Name })" /></span>
   ```

3. Coloque el cursor al final del código localizado, presione Entrar y escriba el siguiente código:

   ```html
   @await Html.PartialAsync("_CityPopulation")
   ```

### Tarea 3: Ejecutar la aplicación

1. En la ventana `CitiesWebsite - Microsoft Visual Studio`, en el menú `File`, haga clic en `Save All`.
2. En la ventana `CitiesWebsite - Microsoft Visual Studio`, en el menú `Debug`, haga clic en `Start Without Debugging`.
3. En Microsoft Edge, haga clic en `Madrid`.

   **Nota:** El navegador muestra el nombre de la ciudad, sus detalles, un minimapa, la población y un vínculo `Back`.

4. En Microsoft Edge, haga clic en `Close`.

**Resultado:** Al final de este ejercicio, habrá usado vistas parciales y servicios dentro de una vista mediante la directiva `@inject`.

## Ejercicio 3: Agregar un componente de vista

### Escenario

Actualmente, en la vista `ShowCities`, se muestra un vínculo con el nombre de cada ciudad. Se le ha pedido mostrar, para cada ciudad de la vista `ShowCities`, el país al que pertenece y un minimapa de la ciudad. Para implementarlo, se le ha pedido usar un componente de vista. En este ejercicio creará un componente de vista y lo incorporará a la vista `ShowCities`.

Las tareas principales de este ejercicio son las siguientes:

- Agregar una clase de componente de vista.
- Agregar una vista del componente de vista.
- Usar el componente de vista.
- Ejecutar la aplicación.

### Tarea 1: Agregar una clase de componente de vista

1. En la ventana `CitiesWebsite - Microsoft Visual Studio`, en `Solution Explorer`, haga clic con el botón derecho en `CitiesWebsite`, señale `Add` y haga clic en `New Folder`.
2. En el cuadro `NewFolder`, escriba `ViewComponents` y presione Entrar.
3. En la ventana `CitiesWebsite - Microsoft Visual Studio`, en `Solution Explorer`, haga clic con el botón derecho en `ViewComponents`, señale `Add` y haga clic en `Class…`.
4. En el cuadro de diálogo `Add New Item -- CitiesWebsite`, en el cuadro `Name:`, escriba `CityViewComponent` y haga clic en `Add`.
5. En la ventana de código de `CityViewComponent.cs`, seleccione el siguiente código:

   ```csharp
   public class CityViewComponent
   ```

6. Agregue una clase base a la clase `CityViewComponent` como se muestra a continuación:

   ```csharp
   public class CityViewComponent : ViewComponent
   ```

7. En la ventana de código de `CityViewComponent.cs`, coloque el cursor dentro del bloque de código de `CityViewComponent` y escriba el siguiente código:

   ```csharp
   private ICityProvider _cities;

   public CityViewComponent(ICityProvider cities)
   {
       _cities = cities;
   }

   public async Task<IViewComponentResult> InvokeAsync(string cityName)
   {
       ViewBag.CurrentCity = await GetCity(cityName);
       return View("SelectCity");
   }

   private Task<City> GetCity(string cityName)
   {
       return Task.FromResult<City>(_cities[cityName]);
   }
   ```

### Tarea 2: Agregar una vista del componente de vista

1. En la ventana `CitiesWebsite - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Views`, haga clic con el botón derecho en `Shared`, señale `Add` y haga clic en `New Folder`.
2. En el cuadro `NewFolder`, escriba `Components` y presione Entrar.
3. En la ventana `CitiesWebsite - Microsoft Visual Studio`, en `Solution Explorer`, haga clic con el botón derecho en `Components`, señale `Add` y haga clic en `New Folder`.
4. En el cuadro `NewFolder`, escriba `City` y presione Entrar.
5. En la ventana `CitiesWebsite - Microsoft Visual Studio`, en `Solution Explorer`, haga clic con el botón derecho en la carpeta `City` que acaba de crear, señale `Add` y haga clic en `View…`.
6. En el cuadro de diálogo `Add New Scaffolded Item`, asegúrese de que esté seleccionada la plantilla `Razor View` (no la plantilla `Empty`) y haga clic en `Add`.
7. En el cuadro de diálogo `Add Razor View`, en el cuadro de texto `Name:`, escriba `SelectCity`.
8. En el cuadro de diálogo `Add Razor View`, asegúrese de que la casilla `Create as a partial view` esté marcada y haga clic en `Add`.
9. En la ventana de código de `SelectCity.cshtml`, reemplace todo el contenido por el siguiente código:

   ```html
   <div>
       <h2>
               <a asp-action="ShowDataForCity" asp-route-cityname=@ViewBag.CurrentCity.Name>@ViewBag.CurrentCity.Name (Capital of @ViewBag.CurrentCity.Country)</a>
       </h2>
       <img src="@Url.Action("GetImage", new { cityName = ViewBag.CurrentCity.Name })" />
   </div>
   ```

### Tarea 3: Usar el componente de vista

1. En la ventana `CitiesWebsite - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Views` y `City`, haga clic en `ShowCities.cshtml`.
2. En la ventana de código de `ShowCities.cshtml`, seleccione el siguiente código:

   ```html
   <h2>
       <a asp-action="ShowDataForCity" asp-route-cityname="@item.Key">@item.Key</a>
   </h2>
   ```

3. Reemplace el código seleccionado por el siguiente:

   ```html
   @await Component.InvokeAsync("City", item.Key)
   ```

### Tarea 4: Ejecutar la aplicación

1. En la ventana `CitiesWebsite - Microsoft Visual Studio`, en el menú `File`, haga clic en `Save All`.
2. En la ventana `CitiesWebsite - Microsoft Visual Studio`, en el menú `Debug`, haga clic en `Start Without Debugging`.

   **Nota:** El navegador muestra una lista de ciudades. Cada una tiene un vínculo de encabezado y una imagen de minimapa de la región.

3. En Microsoft Edge, haga clic en `Madrid (Capital of Spain)`.
4. En Microsoft Edge, haga clic en `Close`.
5. En la ventana `CitiesWebsite -- Microsoft Visual Studio`, en el menú `File`, haga clic en `Exit`.

**Resultado:** Al final de este ejercicio, habrá creado componentes de vista y los habrá incorporado a una vista.
