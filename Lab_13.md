# Lab 13: Implementación de API web

## Escenario

Crearás una aplicación web de restaurante que muestre sus sucursales, permita reservar una mesa, publique ofertas de empleo y reciba solicitudes para puestos. La solución incluye una API web en el proyecto **Server** y una aplicación ASP.NET Core en **Client**. El cliente llamará a la API mediante `HttpClient` y jQuery.

## Ejercicio 1: Agregar acciones y llamarlas desde Microsoft Edge

Crearás controladores y acciones GET y POST en la API. Comprobarás las respuestas GET desde el navegador.

### Tarea 1: Agregar un controlador y una acción a la API

1. Ve a `D:\Allfiles\Mod13\Labfiles\01_Restaurant_begin` y abre `Restaurant.sln` con doble clic. **Nota:** Si aparece **Security Warning for Server**, desmarca **Ask me for every project in this solution** y confirma.
2. En **Solution Explorer**, dentro de **Server**, haz clic derecho en **Controllers** y selecciona **Add > Controller…**.
3. En **Add New Scaffolded Item**, elige **API > API Controller - Empty** y haz clic en **Add**.
4. Nombra el controlador `RestaurantBranchesController` y haz clic en **Add**.
5. En `RestaurantBranchesController.cs`, localiza `using Microsoft.AspNetCore.Mvc;`.
6. Debajo, agrega `using Server.Models;`.
7. Dentro de la clase, agrega el campo y el constructor:

   ```csharp
   private RestaurantContext _context;

   public RestaurantBranchesController(RestaurantContext context)
   {
       _context = context;
   }
   ```

8. Después del constructor, crea la acción:

   ```csharp
   [HttpGet]
   public ActionResult<List<RestaurantBranch>> Get()
   {
   }
   ```

9. Dentro de `Get()`, agrega:

   ```csharp
   var branches = from r in _context.RestaurantBranches
                  orderby r.City
                  select r;
   return branches.ToList();
   ```

### Tarea 2: Ejecutar la aplicación

1. Selecciona **File > Save All**.
2. En **Solution Explorer**, haz clic derecho en **Server** y selecciona **Set as StartUp Project**.
3. Selecciona **Debug > Start Without Debugging**. **Nota:** El navegador muestra la lista de sucursales en formato JSON.
4. Cierra Microsoft Edge.

### Tarea 3: Agregar una acción que recibe un parámetro

1. En **Server/Controllers**, selecciona **Add > Controller…**.
2. Elige **API > API Controller - Empty** y haz clic en **Add**.
3. Nombra el controlador `ReservationController` y haz clic en **Add**.
4. En `ReservationController.cs`, localiza `using Microsoft.AspNetCore.Mvc;`.
5. Debajo, agrega `using Server.Models;`.
6. Dentro de la clase, agrega:

   ```csharp
   private RestaurantContext _context;

   public ReservationController(RestaurantContext context)
   {
       _context = context;
   }
   ```

7. Después del constructor, agrega:

   ```csharp
   [Route("{id:int}")]
   public ActionResult<OrderTable> GetById(int id)
   {
   }
   ```

8. Dentro de `GetById`, agrega:

   ```csharp
   var order = _context.ReservationsTables.FirstOrDefault(p => p.Id == id);
   if (order == null)
   {
       return NotFound();
   }

   return order;
   ```

### Tarea 4: Ejecutar la aplicación

1. Selecciona **File > Save All**.
2. Selecciona **Debug > Start Without Debugging**.
3. En Edge, visita `http://localhost:[port]/api/Reservation/1`, sustituyendo `[port]` por el puerto real. **Nota:** Debe mostrarse la reserva con `id=1` en formato JSON.
4. Cierra Microsoft Edge.

### Tarea 5: Agregar una acción POST

1. En **Server/Controllers**, abre `ReservationController.cs`.
2. Después de `GetById`, crea esta acción:

   ```csharp
   [HttpPost]
   public ActionResult<OrderTable> Create(OrderTable orderTable)
   {
   }
   ```

3. Dentro de `Create`, agrega:

   ```csharp
   _context.Add(orderTable);
   _context.SaveChanges();
   return CreatedAtAction(nameof(GetById), new { id = orderTable.Id }, orderTable);
   ```

**Resultado:** La API dispone de acciones para consultar sucursales y reservas y para crear una reserva.


## Ejercicio 2: Llamar a una API web desde código del servidor

Registrarás `IHttpClientFactory` en **Client** y usarás `HttpClient` para consultar sucursales, crear reservas y recuperar una reserva por identificador.

### Tarea 1: Llamar a un método GET de la API

1. En **Client**, abre `Program.cs`.
2. Localiza `builder.Services.AddControllersWithViews();`.
3. Después, agrega `builder.Services.AddHttpClient();`.
4. En **Client/Controllers**, selecciona **Add > Controller…**.
5. En **Add New Scaffolded Item**, selecciona **MVC Controller - Empty** y haz clic en **Add**.
6. Nombra el controlador `RestaurantBranchesController` y haz clic en **Add**.
7. En `RestaurantBranchesController.cs`, localiza `using Microsoft.AspNetCore.Mvc;`.
8. Debajo, agrega `using Client.Models;`.
9. Selecciona la acción `Index()` inicial, que solo devuelve `View()`.
10. Reemplázala por el campo y el constructor:

    ```csharp
    private IHttpClientFactory _httpClientFactory;

    public RestaurantBranchesController(IHttpClientFactory httpClientFactory)
    {
        _httpClientFactory = httpClientFactory;
    }
    ```

11. Después del constructor, crea la acción asincrónica:

    ```csharp
    public async Task<IActionResult> Index()
    {
    }
    ```

12. Dentro de `Index()`, agrega:

    ```csharp
    HttpClient httpClient = _httpClientFactory.CreateClient();
    httpClient.BaseAddress = new Uri("http://localhost:54517");
    HttpResponseMessage response = await httpClient.GetAsync("api/RestaurantBranches");
    if (response.IsSuccessStatusCode)
    {
        IEnumerable<RestaurantBranch> restaurantBranches =
            await response.Content.ReadAsAsync<IEnumerable<RestaurantBranch>>();
        return View(restaurantBranches);
    }
    else
    {
        return View("Error");
    }
    ```

13. En **Client/Views/RestaurantBranches**, abre `Index.cshtml` y examina su contenido.

### Tarea 2: Ejecutar la aplicación

1. En **Server/Properties**, abre `launchSettings.json`.
2. Localiza el perfil **IIS Express**, donde `launchBrowser` es `true` y `launchUrl` es `api/RestaurantBranches`.
3. Cambia el perfil para evitar que se abra un navegador al iniciar **Server**:

   ```json
   "profiles": {
     "IIS Express": {
       "commandName": "IISExpress",
       "launchBrowser": false,
       "environmentVariables": {
         "ASPNETCORE_ENVIRONMENT": "Development"
       }
     },
   ```

4. Selecciona **File > Save All**.
5. Establece **Server** como proyecto de inicio con **Set as StartUp Project**.
6. Selecciona **Debug > Start Without Debugging**.
7. Establece **Client** como proyecto de inicio.
8. Selecciona **Debug > Start Without Debugging**. **Nota:** El navegador muestra las sucursales.
9. Cierra Microsoft Edge.

### Tarea 3: Llamar a un método POST de la API

1. En **Client/Controllers**, selecciona **Add > Controller…**.
2. Elige **MVC Controller - Empty** y haz clic en **Add**.
3. Nombra el controlador `ReservationController`.
4. En `ReservationController.cs`, localiza `using Microsoft.AspNetCore.Mvc;`.
5. Debajo, agrega `using Client.Models;`.
6. Selecciona la acción `Index()` inicial.
7. Reemplázala por:

   ```csharp
   private IHttpClientFactory _httpClientFactory;

   public ReservationController(IHttpClientFactory httpClientFactory)
   {
       _httpClientFactory = httpClientFactory;
   }
   ```

8. Después del constructor, agrega la acción GET que muestra el formulario:

   ```csharp
   [HttpGet]
   public async Task<IActionResult> Create()
   {
       await PopulateRestaurantBranchesDropDownListAsync();
       return View();
   }
   ```

9. Después de esa acción, agrega el envío de la reserva:

   ```csharp
   [HttpPost, ActionName("Create")]
   public async Task<IActionResult> CreatePostAsync(OrderTable orderTable)
   {
       HttpClient httpclient = _httpClientFactory.CreateClient();
       HttpResponseMessage response =
           await httpclient.PostAsJsonAsync(
               "http://localhost:54517/api/Reservation", orderTable);

       if (response.IsSuccessStatusCode)
       {
           OrderTable order = await response.Content.ReadAsAsync<OrderTable>();
           return RedirectToAction("ThankYou", new { orderId = order.Id });
       }
       else
       {
           return View("Error");
       }
   }
   ```

10. Después, agrega el método que llena la lista de sucursales:

    ```csharp
    private async Task PopulateRestaurantBranchesDropDownListAsync()
    {
        HttpClient httpClient = _httpClientFactory.CreateClient();
        httpClient.BaseAddress = new Uri("http://localhost:54517");
        HttpResponseMessage response =
            await httpClient.GetAsync("api/RestaurantBranches");

        if (response.IsSuccessStatusCode)
        {
            IEnumerable<RestaurantBranch> restaurantBranches =
                await response.Content.ReadAsAsync<IEnumerable<RestaurantBranch>>();
            ViewBag.RestaurantBranches =
                new SelectList(restaurantBranches, "Id", "City");
        }
    }
    ```

11. En **Client/Views/Reservation**, abre `Create.cshtml` y examina su contenido.

### Tarea 4: Llamar a un método GET con parámetro

1. Después de `PopulateRestaurantBranchesDropDownListAsync()`, agrega una acción `ThankYou(int orderId)`:

   ```csharp
   public async Task<IActionResult> ThankYou(int orderId)
   {
   }
   ```

2. Dentro de la acción, agrega:

   ```csharp
   HttpClient httpClient = _httpClientFactory.CreateClient();
   httpClient.BaseAddress = new Uri("http://localhost:54517");
   HttpResponseMessage response =
       await httpClient.GetAsync("api/Reservation/" + orderId);

   if (response.IsSuccessStatusCode)
   {
       OrderTable orderResult = await response.Content.ReadAsAsync<OrderTable>();
       return View(orderResult);
   }
   else
   {
       return View("Error");
   }
   ```

3. En **Client/Views/Reservation**, abre `ThankYou.cshtml` y examina su contenido.

### Tarea 5: Ejecutar la aplicación

1. Selecciona **File > Save All**.
2. Establece **Server** como proyecto de inicio.
3. Inícialo con **Debug > Start Without Debugging**.
4. Establece **Client** como proyecto de inicio.
5. Inícialo con **Debug > Start Without Debugging**.
6. En Edge, haz clic en **Reservation**.
7. En **Restaurant Branch**, selecciona cualquier restaurante.
8. En **First Name**, escribe tu nombre.
9. En **Last Name**, escribe tu apellido.
10. En **Phone Number**, escribe algunos dígitos.
11. En **Reservation Time**, elige una fecha y hora.
12. En **Dinner Guests**, escribe un número del 1 al 20 y haz clic en **Make a Reservation**.
13. Cierra Microsoft Edge.

**Resultado:** El cliente llama a la API con `HttpClient` para mostrar sucursales, enviar una reserva y consultar la reserva creada.


## Ejercicio 3: Llamar a una API web con jQuery

Usarás jQuery para mostrar ofertas de empleo con una llamada GET y enviar una solicitud de empleo con una llamada POST. También usarás `HttpClient` para llenar la lista de puestos del formulario.

### Tarea 1: Llamar a un método GET con jQuery

1. En **Client/wwwroot/js**, selecciona **Add > New Item…**.
2. Elige **JavaScript File**.
3. Nombra el archivo `wanted-ad-get` y haz clic en **Add**.
4. En `wanted-ad-get.js`, escribe:

   ```javascript
   $(function () {
       $.ajax({
           method: "GET",
           url: "http://localhost:54517/api/RestaurantWantedAd",
           contentType: "application/json; charset=utf-8",
           dataType: "json"
       }).done(function (data) {
       }).fail(function () {
           alert('An error has occurred');
       });
   });
   ```

5. Localiza `}).done(function (data) {`.
6. Dentro de ese bloque, agrega:

   ```javascript
   $.each(data, function (index, item) {
       var html = `<div class="photo-index-card-data">
           <div class="image-wrapper">
               <img class="photo-display-img" src="~/images/white-plate.jpg" />
           </div>
           <div class="display-picture-data">
               <h6 class="display-title">Job title:</h6>
               <h6>${item.jobTitle}</h6>
               <h6 class="display-title">Hourly payment:</h6>
               <h6>$${item.pricePerHour}</h6>
               <h6 class="display-title">Job description:</h6>
               <h6>${item.jobDescription}</h6>
           </div>
       </div>`;
       $('.container').append(html);
   });
   $('.photo-display-img').attr('src', '/images/white-plate.jpg');
   ```

7. En **Client/Views/WantedAd**, abre `Index.cshtml`.
8. Localiza el elemento `<div class="container"></div>`.
9. Después, agrega la sección:

   ```cshtml
   @section Scripts
   {
       <script src="~/js/wanted-ad-get.js"></script>
   }
   ```

### Tarea 2: Ejecutar la aplicación

1. Selecciona **File > Save All**.
2. Establece **Server** como proyecto de inicio.
3. Inícialo con **Debug > Start Without Debugging**.
4. Establece **Client** como proyecto de inicio.
5. Inícialo con **Debug > Start Without Debugging**.
6. En Edge, haz clic en **We Are Hiring**. **Nota:** Deben mostrarse las vacantes del restaurante.
7. Cierra Microsoft Edge.

### Tarea 3: Llamar a un método GET con HttpClient

1. En **Client/Controllers**, selecciona **Add > Controller…**.
2. Elige **MVC Controller - Empty** y haz clic en **Add**.
3. Nombra el controlador `JobApplicationController`.
4. En `JobApplicationController.cs`, localiza `using Microsoft.AspNetCore.Mvc;`.
5. Debajo, agrega `using Client.Models;`.
6. Selecciona la acción `Index()` inicial.
7. Reemplázala por el campo y el constructor:

   ```csharp
   private IHttpClientFactory _httpClientFactory;

   public JobApplicationController(IHttpClientFactory httpClientFactory)
   {
       _httpClientFactory = httpClientFactory;
   }
   ```

8. Después, agrega la acción GET para mostrar el formulario:

   ```csharp
   [HttpGet]
   public async Task<IActionResult> Create()
   {
       await PopulateEmployeeRequirementsDropDownListAsync();
       return View();
   }
   ```

9. Agrega el método que obtiene las vacantes de la API:

   ```csharp
   private async Task PopulateEmployeeRequirementsDropDownListAsync()
   {
       HttpClient httpClient = _httpClientFactory.CreateClient();
       httpClient.BaseAddress = new Uri("http://localhost:54517");
       HttpResponseMessage response =
           await httpClient.GetAsync("api/RestaurantWantedAd");

       if (response.IsSuccessStatusCode)
       {
           IEnumerable<EmployeeRequirements> employeeRequirements =
               await response.Content.ReadAsAsync<IEnumerable<EmployeeRequirements>>();
           ViewBag.EmployeeRequirements =
               new SelectList(employeeRequirements, "Id", "JobTitle");
       }
   }
   ```

10. En **Client/Views/JobApplication**, abre `Create.cshtml` y examina su contenido.
11. Después del método anterior, agrega la acción de agradecimiento:

    ```csharp
    public IActionResult ThankYou()
    {
        return View();
    }
    ```

12. En **Client/Views/JobApplication**, abre `ThankYou.cshtml` y examina su contenido.

### Tarea 4: Llamar a un método POST con jQuery

1. En **Client/wwwroot/js**, selecciona **Add > New Item…**.
2. Elige **JavaScript File**.
3. Nombra el archivo `wanted-ad-post` y haz clic en **Add**.
4. En `wanted-ad-post.js`, escribe:

   ```javascript
   $(function() {
       $("#btn-post").click(function (e) {
           if ($('#submit-form').valid()) {
               var formData = {};
               $('#submit-form').serializeArray().map(function (item) {
                   item.name = item.name[0].toLowerCase() + item.name.slice(1);
                   if (formData[item.name]) {
                       if (formData[item.name] === "string") {
                           formData[item.name] = [formData[item.name]];
                       }
                       formData[item.name].push(item.value);
                   } else {
                       formData[item.name] = item.value;
                   }
               });
               e.preventDefault();
               $.ajax({
                   method: "POST",
                   url: "http://localhost:54517/api/job",
                   data: JSON.stringify(formData),
                   contentType: "application/json;charset=utf-8"
               }).done(function () {
                   location.href = 'http://localhost:54508/JobApplication/ThankYou';
               }).fail(function () {
                   alert('An error has occurred');
               });
           }
       });
   });
   ```

5. En **Client/Views/JobApplication**, abre `Create.cshtml`.
6. Localiza el final del formulario, seguido de los dos elementos `</div>`.
7. Después, agrega:

   ```cshtml
   @section Scripts
   {
       <script src="~/lib/jquery-validate/jquery.validate.min.js"></script>
       <script src="~/lib/jquery-validation-unobtrusive/jquery.validate.unobtrusive.min.js"></script>
       <script src="~/js/wanted-ad-post.js"></script>
   }
   ```

### Tarea 5: Ejecutar la aplicación

1. Selecciona **File > Save All**.
2. Establece **Server** como proyecto de inicio.
3. Inícialo con **Debug > Start Without Debugging**.
4. Establece **Client** como proyecto de inicio.
5. Inícialo con **Debug > Start Without Debugging**.
6. En la primera ventana de Edge, haz clic en **Submit Job Application**.
7. En **Job title**, selecciona **Manager**.
8. En **First name**, escribe tu nombre.
9. En **Last name**, escribe tu apellido.
10. En **Phone number**, escribe algunos dígitos.
11. En **Email**, escribe una dirección plausible.
12. En **Address**, escribe una dirección de ejemplo y haz clic en **Apply For a Job**.
13. Cierra Microsoft Edge.
14. En Visual Studio, selecciona **File > Exit**.

**Resultado:** El cliente consulta las vacantes mediante GET y envía una candidatura mediante POST a la API web.
