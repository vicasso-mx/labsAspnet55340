# Lab 12: Rendimiento y comunicación

## Escenario

Crearás una tienda web de productos eléctricos con ofertas del día, productos por categoría, lista de compras y un chat. Usarás un tag helper de caché en una vista, caché en memoria en un controlador, sesiones y SignalR para la comunicación en tiempo real.

## Ejercicio 1: Implementar una estrategia de caché

Agregarás un tag helper de caché a una vista y almacenarás productos en caché de memoria.

### Tarea 1: Agregar un tag helper de caché a una vista

1. En el Explorador de archivos, ve a `D:\Allfiles\Mod12\Labfiles\01_ElectricStore_begin` y abre `ElectricStore.sln`. **Nota:** Si aparece una advertencia de seguridad, desmarca **Ask me for every project in this solution** y confirma. El texto original menciona “Library” en esta advertencia, aunque el proyecto es **ElectricStore**.
2. En **Solution Explorer**, abre `Views/Shared/_Layout.cshtml`.
3. Localiza:

   ```html
   <ul class="navbar-nav" id="nav-content">
   </ul>
   ```

4. Antes de `</ul>`, agrega:

   ```cshtml
   <cache vary-by-route="RefreshCache">
       @await Component.InvokeAsync("NavbarMenu")
   </cache>
   ```

### Tarea 2: Insertar los datos que almacenará el tag helper

1. En **Solution Explorer**, haz clic derecho en el proyecto **ElectricStore** y selecciona **Add > New Folder**.
2. Nombra la carpeta `ViewComponents`.
3. Haz clic derecho en `ViewComponents` y selecciona **Add > Class…**.
4. Nombra la clase `NavbarMenuViewComponent` y haz clic en **Add**.
5. Al comienzo de `NavbarMenuViewComponent.cs`, agrega:

   ```csharp
   using ElectricStore.Data;
   using Microsoft.AspNetCore.Mvc;
   ```

6. Localiza `public class NavbarMenuViewComponent`.
7. Agrega la clase base `: ViewComponent` a la declaración.
8. Dentro de la clase, agrega:

   ```csharp
   private StoreContext _context;

   public NavbarMenuViewComponent(StoreContext context)
   {
       _context = context;
   }

   public IViewComponentResult Invoke()
   {
       var categories = _context.menuCategories.OrderBy(c => c.Name).ToList();
       return View("MenuCategories", categories);
   }
   ```

9. En `Views/Shared`, crea una carpeta con **Add > New Folder**.
10. Nómbrala `Components`.
11. Dentro de `Components`, crea otra carpeta.
12. Nómbrala `NavbarMenu`.
13. Haz clic derecho en `NavbarMenu` y selecciona **Add > New Item…**.
14. En **Web**, selecciona **Razor View - Empty**.
15. Nombra el archivo `MenuCategories` y haz clic en **Add**.
16. Reemplaza todo el contenido de `MenuCategories.cshtml` por:

   ```cshtml
   @model IEnumerable<ElectricStore.Models.MenuCategory>

   <li class="nav-item">
       <a class="nav-link" href="@Url.Action("Index", "Products")">Home<span class="sr-only">(current)</span></a>
   </li>

   <li class="nav-item dropdown">
       <a class="nav-link dropdown-toggle" href="#" id="navbarDropdownMenuLink" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
           Electronics
       </a>
       <div class="dropdown-menu" aria-labelledby="navbarDropdownMenuLink">
           @foreach (var item in Model)
           {
               <a class="dropdown-item" asp-action="GetByCategory" asp-controller="Products" asp-route-id="@item.Id">@Html.DisplayFor(modelItem => item.Name)</a>
           }
       </div>
   </li>

   <li class="nav-item">
       <a class="nav-link" href="@Url.Action("AddToShoppingList", "Products")">Add to Shopping List</a>
   </li>
   <li class="nav-item">
       <a class="nav-link" href="@Url.Action("Index", "ShoppingCart")">My Shopping Cart</a>
   </li>
   <li class="nav-item">
       <div class="display-cached-time">The content cached Since: @DateTime.Now</div>
   </li>
   ```

### Tarea 3: Ejecutar la aplicación

1. Selecciona **File > Save All**.
2. Selecciona **Debug > Start Debugging**.
3. Desde el icono de Microsoft Edge en la barra de tareas, abre **New Window**.
4. En esa segunda ventana, visita `http://localhost:50672`. **Nota:** La hora mostrada en la barra de menú debe coincidir en ambas ventanas porque ese contenido se almacenó en caché.
5. Abre una tercera ventana de Edge.
6. Visita `http://localhost:50672/products/index/1/RefreshCache`. **Nota:** La hora debe diferir de la de las dos ventanas anteriores porque `RefreshCache` cambia la ruta usada para la caché.
7. Cierra las ventanas de Edge.

### Tarea 4: Insertar productos en una caché de memoria

1. En **Solution Explorer**, abre `Controllers/ProductsController.cs`.
2. Localiza `using Microsoft.EntityFrameworkCore;`.
3. Debajo, agrega `using Microsoft.Extensions.Caching.Memory;`.
4. Localiza los campos `_context` y `_environment`.
5. Después de ellos, agrega:

   ```csharp
   private IMemoryCache _memoryCache;
   private const string PRODUCT_KEY = "Products";
   ```

6. Selecciona el constructor actual `ProductsController(StoreContext context, IWebHostEnvironment environment)`.
7. Reemplázalo por:

   ```csharp
   public ProductsController(StoreContext context, IWebHostEnvironment environment, IMemoryCache memoryCache)
   {
       _context = context;
       _environment = environment;
       _memoryCache = memoryCache;
   }
   ```

8. Selecciona el método `Index()` que únicamente devuelve `View()`.
9. Reemplázalo por:

   ```csharp
   public IActionResult Index()
   {
       List<Product> products;
       if (!_memoryCache.TryGetValue(PRODUCT_KEY, out products))
       {
           products = _context.Products.ToList();
           products.Select(c => { c.LoadedFromDatabase = DateTime.Now; return c; }).ToList();
           MemoryCacheEntryOptions cacheOptions = new MemoryCacheEntryOptions();
           cacheOptions.SetPriority(CacheItemPriority.High);
           _memoryCache.Set(PRODUCT_KEY, products, cacheOptions);
       }
       return View(products);
   }
   ```

10. En **Solution Explorer**, abre `Views/Products/Index.cshtml`.
11. Localiza el encabezado de la oferta:

    ```html
    <h2 class="title">
        <img src="~/images/sale-banner.jpg" />
        <span class="span-col-s">Sale of The Day</span>
    </h2>
    ```

12. Después de `</h2>`, agrega:

    ```cshtml
    <div class="page-container">
        @foreach (var item in Model)
        {
            <div class="item">
                <h3>
                    @Html.DisplayFor(modelItem => item.ProductName)
                </h3>
                @if (item.PhotoFileName != null)
                {
                    <div>
                        <img src="@Url.Action("GetImage", "Products", new { productId = item.Id })" />
                    </div>
                }
                <div>
                    <p>@Html.DisplayFor(model => item.Description)</p>
                    <p>@Html.DisplayFor(model => item.Price)</p>
                </div>
                <div>
                    <p>@Html.DisplayNameFor(model => item.LoadedFromDatabase)</p>
                    <p>@Html.DisplayFor(model => item.LoadedFromDatabase)</p>
                </div>
            </div>
        }
    </div>
    ```

### Tarea 5: Ejecutar la aplicación

1. Selecciona **File > Save All**.
2. Selecciona **Debug > Start Debugging** y observa el dato **Last retrieved on** de los productos.
3. Abre otra ventana de Edge.
4. Visita `http://localhost:50672/`. **Nota:** En ambas ventanas debe coincidir la hora **Last retrieved on** de los productos de **Sale of The Day**, lo que confirma que se recuperaron de la caché.
5. Cierra todas las ventanas de Edge.

**Resultado:** La vista usa un tag helper de caché y el controlador almacena productos en memoria.


## Ejercicio 2: Administrar el estado

HTTP no conserva estado entre solicitudes. En este ejercicio usarás sesiones del servidor para recordar los datos del cliente y su lista de compras mientras navega por la aplicación.

### Tarea 1: Habilitar las sesiones

1. En **Solution Explorer**, abre `Program.cs`.
2. Localiza el registro de `StoreContext`:

   ```csharp
   builder.Services.AddDbContext<StoreContext>(options =>
       options.UseSqlite("Data Source=electricStore.db"));
   ```

3. Después, agrega:

   ```csharp
   builder.Services.AddSession(
       options => options.IdleTimeout = TimeSpan.FromSeconds(60));
   ```

4. Localiza `app.UseStaticFiles();`.
5. Después, agrega `app.UseSession();`.

### Tarea 2: Guardar valores en la sesión

1. En **Solution Explorer**, abre `Controllers/ProductsController.cs`.
2. Localiza `using Microsoft.Extensions.Caching.Memory;`.
3. Debajo, agrega `using System.Text.Json;`.
4. Selecciona la acción actual `AddToShoppingListPost(Customer customer)`, que guarda el cliente en `_context.Customers`.
5. Reemplázala por:

   ```csharp
   [HttpPost, ActionName("AddToShoppingList")]
   public IActionResult AddToShoppingListPost(Customer customer)
   {
       if (ModelState.IsValid)
       {
           HttpContext.Session.SetString("CustomerFirstName", customer.FirstName);
           HttpContext.Session.SetString("CustomerLastName", customer.LastName);
           HttpContext.Session.SetString("CustomerEmail", customer.Email);
           HttpContext.Session.SetString("CustomerAddress", customer.Address);
           HttpContext.Session.SetInt32("CustomerPhoneNumber", customer.PhoneNumber);

           if (HttpContext.Session.GetString("CustomerProducts") != null)
           {
               List<int> productsListId =
                   JsonSerializer.Deserialize<List<int>>(
                       HttpContext.Session.GetString("CustomerProducts"));
               customer.SelectedProductsList.AddRange(productsListId);
           }

           var serialisedDate = JsonSerializer.Serialize(customer.SelectedProductsList);
           HttpContext.Session.SetString("CustomerProducts", serialisedDate);
           return RedirectToAction(nameof(Index));
       }

       PopulateProductsList(customer.SelectedProductsList);
       return View(customer);
   }
   ```

### Tarea 3: Recuperar valores de la sesión

1. En `ProductsController.cs`, localiza la acción GET `AddToShoppingList()`.
2. Selecciona el método actual, que llama a `PopulateProductsList()` y devuelve `View()`.
3. Reemplázalo por:

   ```csharp
   [HttpGet]
   public IActionResult AddToShoppingList()
   {
       if (HttpContext.Session.GetString("CustomerFirstName") != null)
       {
           Customer sessionCustomer = new Customer()
           {
               FirstName = HttpContext.Session.GetString("CustomerFirstName"),
               LastName = HttpContext.Session.GetString("CustomerLastName"),
               Email = HttpContext.Session.GetString("CustomerEmail"),
               Address = HttpContext.Session.GetString("CustomerAddress"),
               PhoneNumber = HttpContext.Session.GetInt32("CustomerPhoneNumber").Value,
           };
           PopulateProductsList();
           return View(sessionCustomer);
       }

       PopulateProductsList();
       return View();
   }
   ```

4. En **Solution Explorer**, abre `Controllers/ShoppingCartController.cs`.
5. Localiza `using Microsoft.AspNetCore.Mvc;`.
6. Debajo, agrega `using System.Text.Json;`.
7. Localiza la acción `Index()` que solo devuelve `View()`.
8. Antes de la acción, agrega los campos y el constructor:

   ```csharp
   private StoreContext _context;
   private List<Product> products;
   private SessionStateViewModel sessionModel;

   public ShoppingCartController(StoreContext context)
   {
       _context = context;
   }
   ```

9. Dentro de `Index()`, selecciona `return View();`.
10. Reemplázalo por:

    ```csharp
    if (!string.IsNullOrEmpty(HttpContext.Session.GetString("CustomerFirstName")) &&
        !string.IsNullOrEmpty(HttpContext.Session.GetString("CustomerProducts")))
    {
        List<int> productsListId =
            JsonSerializer.Deserialize<List<int>>(
                HttpContext.Session.GetString("CustomerProducts"));
        products = new List<Product>();

        foreach (var item in productsListId)
        {
            var product = _context.Products.SingleOrDefault(p => p.Id == item);
            products.Add(product);
        }

        sessionModel = new SessionStateViewModel
        {
            CustomerName = HttpContext.Session.GetString("CustomerFirstName"),
            SelectedProducts = products
        };
        return View(sessionModel);
    }

    return View();
    ```

### Tarea 4: Ejecutar la aplicación

1. Selecciona **File > Save All**.
2. Selecciona **Debug > Start Debugging**.
3. En el menú del navegador, haz clic en **My Shopping Cart**. **Nota:** El carrito está vacío.
4. Haz clic en **Add to Shopping List**.
5. En **Products List**, selecciona **TV**.
6. En **First Name**, escribe tu nombre.
7. En **Last Name**, escribe tu apellido.
8. En **Address**, escribe una dirección de ejemplo.
9. En **Email**, escribe una dirección de correo plausible.
10. En **Phone**, escribe algunos dígitos y haz clic en **Add to Shopping List**.
11. Abre **My Shopping Cart**. **Nota:** Debe mostrar el producto seleccionado y los datos de la sesión.
12. Abre **Add to Shopping List** de nuevo. **Nota:** Tus datos personales deben aparecer precargados.
13. Selecciona **Coffee Machine** y haz clic en **Add to Shopping List**.
14. Abre **My Shopping Cart**. **Nota:** Deben aparecer tanto **TV** como **Coffee Machine**.
15. Desde el icono de Edge en la barra de tareas, abre **New InPrivate window**.
16. En la ventana InPrivate, visita `http://localhost:50672`.
17. Abre **My Shopping Cart**. **Nota:** En esta sesión nueva el carrito está vacío; la información de la sesión anterior solo está disponible en su contexto original.
18. Cierra todas las ventanas de Edge.

**Resultado:** La aplicación guarda y recupera información mediante sesiones.


## Ejercicio 3: Comunicación bidireccional con SignalR

Crearás un hub de SignalR, lo registrarás en `Program.cs` y agregarás una vista de chat con un script que conecta el navegador con el servidor.

### Tarea 1: Agregar la clase ChatHub

1. En **Solution Explorer**, haz clic derecho en **ElectricStore** y selecciona **Add > New Folder**.
2. Nombra la carpeta `Hubs`.
3. Haz clic derecho en `Hubs` y selecciona **Add > Class…**.
4. Nombra la clase `ChatHub` y haz clic en **Add**.
5. Al comienzo de `ChatHub.cs`, agrega `using Microsoft.AspNetCore.SignalR;`.
6. Localiza `public class ChatHub`.
7. Añade `: Hub` a la declaración de la clase.
8. Dentro de la clase, agrega:

   ```csharp
   public async Task SendMessageAll(string user, string message)
   {
       await Clients.All.SendAsync("NewMessage", user, message);
   }
   ```

### Tarea 2: Configurar SignalR en Program.cs

1. En **Solution Explorer**, abre `Program.cs`.
2. Localiza `using Microsoft.EntityFrameworkCore;`.
3. Debajo, agrega `using ElectricStore.Hubs;`.
4. Localiza la llamada `builder.Services.AddSession(...)` con un tiempo de inactividad de 60 segundos.
5. Después, agrega `builder.Services.AddSignalR();`.
6. Localiza `app.UseSession();`.
7. Después, agrega `app.MapHub<ChatHub>("/chatHub");`.

### Tarea 3: Agregar una vista de chat

1. En **Solution Explorer**, abre `Controllers/ShoppingCartController.cs`.
2. Después de la acción `Index()`, agrega:

   ```csharp
   public IActionResult Chat()
   {
       return View();
   }
   ```

3. Haz clic derecho en `public IActionResult Chat()` y selecciona **Add View…**.
4. En **Add New Scaffolded Item**, selecciona **Razor View** y haz clic en **Add**.
5. Comprueba que **View name** sea `Chat`.
6. Desmarca **Create as a partial view**, marca **Use a layout page** y haz clic en **Add**.
7. En `Chat.cshtml`, selecciona `<h1>Chat</h1>`.
8. Reemplázalo por:

   ```html
   <h2 class="title">
       <span>Chat with</span>
       <span class="chat-title">Online Site Visitors</span>
   </h2>
   <div class="chat">
       <div class="row justify-content-center">
           <div class="col-4 bcf">
               <div class="col-5">
                   Name:
                   <input type="text" id="inputUserName" />
               </div>
               <div class="col-sm-5">
                   Message:
                   <input type="text" id="inputMessage" />
               </div>
               <div class="col-sm-3">
                   <input class="btn btn-info" type="submit" id="sendMessageBtn" value="Send Message" />
               </div>
           </div>
           <div class="col-6 bcs">
               <ul id="messagesList"></ul>
           </div>
       </div>
   </div>
   ```

9. Después del último `</div>`, agrega la sección de scripts:

   ```cshtml
   @section Scripts
   {
       <script src="~/microsoft/signalr/dist/browser/signalr.js"></script>
   }
   ```

10. En **Solution Explorer**, abre `Views/ShoppingCart/Index.cshtml`.
11. Al final del archivo, agrega el botón que abre el chat:

    ```cshtml
    <button type="button" id="btnChat" class="btn btn-success" onclick="location.href='@Url.Action("Chat", "ShoppingCart")'">Chat with Online Visitors</button>
    ```

### Tarea 4: Agregar el JavaScript del cliente

1. Haz clic derecho en `wwwroot` y selecciona **Add > New Folder**.
2. Nombra la carpeta `js`.
3. Haz clic derecho en `wwwroot/js` y selecciona **Add > Existing Item…**.
4. Ve a `D:\Allfiles\Mod12\Labfiles\ElectricStoreJS`, selecciona `chat-hub.js` y haz clic en **Add**.
5. Abre `chat-hub.js` y revisa su contenido. **Nota:** El script usa jQuery y SignalR para enviar y recibir mensajes.
6. Abre `Views/ShoppingCart/Chat.cshtml`.
7. Localiza la sección `Scripts` con la referencia a `~/microsoft/signalr/dist/browser/signalr.js`.
8. Dentro de esa sección, después de la referencia anterior, agrega:

   ```html
   <script src="~/js/chat-hub.js"></script>
   ```

### Tarea 5: Ejecutar la aplicación

1. Selecciona **File > Save All**.
2. Selecciona **Debug > Start Debugging**.
3. En el navegador, haz clic en **My Shopping Cart**.
4. Haz clic en **Chat with Online Visitors**.
5. Abre una ventana **New InPrivate window** desde el icono de Edge en la barra de tareas.
6. En la segunda ventana, visita `http://localhost:50672/ShoppingCart/Chat`.
7. En **Name** de la segunda ventana, escribe `Bill`.
8. En **Message**, escribe un mensaje y haz clic en **Send Message**. Si es necesario, haz clic dos veces. **Nota:** El mensaje debe aparecer de inmediato en ambas ventanas.
9. En la primera ventana, escribe `Mary` en **Name**.
10. Escribe otro mensaje y haz clic en **Send Message**. **Nota:** Debe aparecer en ambas ventanas.
11. Cierra todas las ventanas de Edge.
12. En Visual Studio, selecciona **File > Exit**.

**Resultado:** La tienda permite consultar productos, agregarlos a la lista de compras y conversar en tiempo real con otros visitantes.
