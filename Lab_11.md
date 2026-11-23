# Lab 11: Administración de la seguridad

## Escenario

Se te ha pedido crear una aplicación web de biblioteca para los clientes de tu organización. Debe mostrar libros recomendados y permitir el inicio de sesión y el registro de usuarios. Los usuarios autenticados podrán tomar libros prestados y quienes tengan el rol `admin` podrán agregar libros a la biblioteca. La aplicación también incluirá una demostración de un ataque de falsificación de solicitudes entre sitios (CSRF).

## Ejercicio 1: Usar Identity

En este ejercicio configurarás ASP.NET Core Identity con Entity Framework Core, agregarás el inicio de sesión y el registro, y restringirás el acceso a las páginas de libros mediante `[Authorize]`.

### Tarea 1: Ejecutar la aplicación inicial

1. En el Explorador de archivos, ve a `D:\Allfiles\Mod11\Labfiles\01_Library_begin` y abre `Library.sln` con doble clic. **Nota:** Si aparece **Security Warning for Library**, desmarca **Ask me for every project in this solution** y haz clic en **OK**.
2. En Visual Studio, selecciona **Debug > Start Debugging**. **Nota:** El navegador muestra la página de inicio con una lista de libros recomendados.
3. En la barra de menú, haz clic en **Books** y después en uno de los enlaces **Borrow a Book**. **Nota:** Más adelante restringirás estas páginas a los usuarios autorizados; la página de inicio seguirá disponible para visitantes anónimos.
4. Cierra la ventana de Microsoft Edge.
5. En Visual Studio, selecciona **Tools > NuGet Package Manager > Manage NuGet Packages for Solution…**.
6. En la pestaña **Installed**, comprueba que ya estén instalados `Microsoft.AspNetCore.Identity.UI`, `Microsoft.AspNetCore.Identity.EntityFrameworkCore` y `Microsoft.EntityFrameworkCore.Sqlite`.

### Tarea 2: Habilitar Identity

1. En **Solution Explorer**, abre `Program.cs`.
2. Localiza `using Microsoft.EntityFrameworkCore;`.
3. Debajo, agrega:

   ```csharp
   using Microsoft.AspNetCore.Identity;
   ```

4. Localiza el registro del contexto:

   ```csharp
   builder.Services.AddDbContext<LibraryContext>(options =>
       options.UseSqlite("Data Source=library.db"));
   ```

5. Debajo, agrega:

   ```csharp
   builder.Services.AddDefaultIdentity<IdentityUser>(
       options => options.SignIn.RequireConfirmedAccount = true)
       .AddEntityFrameworkStores<LibraryContext>();
   ```

6. Localiza `app.UseRouting();`.
7. Debajo, agrega:

   ```csharp
   app.UseAuthentication();
   app.UseAuthorization();
   ```

8. Localiza `app.Run();`.
9. Antes de esa línea, agrega `app.MapRazorPages();`.
10. En **Solution Explorer**, abre `Data/LibraryContext.cs`.
11. Observa que la clase actualmente hereda de `DbContext`:

    ```csharp
    public class LibraryContext : DbContext
    ```

12. Cambia la clase base a `IdentityDbContext`:

    ```csharp
    public class LibraryContext : IdentityDbContext
    ```

13. Al comienzo del archivo, comprueba que estén presentes estos espacios de nombres:

    ```csharp
    using Microsoft.AspNetCore.Identity;
    using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
    ```

14. En `LibraryContext.cs`, localiza `base.OnModelCreating(modelBuilder);`. **Nota:** El método `OnModelCreating` ya inserta los datos iniciales de libros.
15. Después de esa línea, agrega el siguiente código para crear usuarios y un rol iniciales:

    ```csharp
    // set up some initial users
    var password = new PasswordHasher<IdentityUser>();
    var users = new List<IdentityUser>();
    var usernames = new List<string>() { "bill", "mary", "mike", "tom" };
    foreach (var name in usernames)
    {
        var user = new IdentityUser
        {
            Email = name + "@foo.com",
            NormalizedEmail = name.ToUpper() + "@FOO.COM",
            UserName = name + "@foo.com",
            NormalizedUserName = name.ToUpper() + "@FOO.COM",
            PhoneNumber = "+123456789",
            EmailConfirmed = true,
            PhoneNumberConfirmed = true
        };
        var passwordHash = password.HashPassword(user, "FooBar11!");
        user.PasswordHash = passwordHash;
        users.Add(user);
    }

    // Add the initial users
    modelBuilder.Entity<IdentityUser>().HasData(users);

    // Add a role "admin"
    var adminRole = new IdentityRole
    {
        Name = "admin",
        NormalizedName = "ADMIN"
    };
    modelBuilder.Entity<IdentityRole>().HasData(adminRole);

    // Assign role to the first user in the list
    modelBuilder.Entity<IdentityUserRole<string>>().HasData(
        new IdentityUserRole<string>
        {
            RoleId = adminRole.Id,
            UserId = users[0].Id
        }
    );
    ```

16. En **Solution Explorer**, abre `Views/Shared/_Layout.cshtml`.
17. Localiza el bloque del menú de navegación que contiene los enlaces **Home** y **Books**:

    ```html
    <div class="navbar-collapse collapse d-sm-inline-flex justify-content-between">
        <ul class="navbar-nav flex-grow-1">
            <li class="nav-item">
                <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Index">Home</a>
            </li>
            <li class="nav-item">
                <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="GetBooksByGenre">Books</a>
            </li>
        </ul>
    </div>
    ```

18. Antes del último `</div>` de ese bloque, inserta el control de inicio de sesión:

    ```html
    <partial name="_LoginPartial" />
    ```

### Tarea 3: Ejecutar la aplicación

1. En Visual Studio, selecciona **File > Save All**.
2. En **Solution Explorer**, elimina `library.db`, `library.db-shm` y `library.db-wal`, si existen, y confirma el aviso. **Nota:** La base de datos se recreará con el nuevo esquema en la siguiente ejecución.
3. Selecciona **Debug > Start Debugging**. **Nota:** El sitio muestra **Register** y **Login**; por ahora, **Books** sigue siendo accesible sin iniciar sesión.
4. Haz clic en **Login** e inicia sesión con `mary@foo.com` y `FooBar11!`. **Nota:** El correo del usuario inicial aparece en la parte superior.
5. Haz clic en **Logout**. **Nota:** Aún puedes acceder a **Books** sin iniciar sesión.
6. Haz clic en **Register** y registra `harry@foo.com` con la contraseña `FooBar11!`. **Nota:** Se aplican los requisitos de complejidad de contraseña y se crea el usuario en la base de datos.
7. Haz clic en **Click here to confirm your account**. **Nota:** El laboratorio simula la confirmación; una aplicación de producción necesitaría configurar el envío de correo.
8. Haz clic en **Login** e inicia sesión con la cuenta recién creada. Comprueba que aparezcan el correo y **Logout**.
9. Haz clic en **Logout**. **Nota:** Todavía es posible abrir **Books** sin autenticarse.
10. Cierra Microsoft Edge.

### Tarea 4: Restringir el acceso a las páginas de préstamo

1. En **Solution Explorer**, abre `Controllers/HomeController.cs`.
2. Al comienzo del archivo, agrega:

   ```csharp
   using Microsoft.AspNetCore.Authorization;
   ```

3. Localiza el método `GetBooksByGenre()`:

   ```csharp
   public IActionResult GetBooksByGenre()
   {
       var booksGenreQuery = from b in _context.Books
                             orderby b.Genre.Name
                             select b;

       return View(booksGenreQuery);
   }
   ```

4. Inmediatamente antes del método, agrega `[Authorize]`.
5. Localiza el método `LendingBook(int id)`:

   ```csharp
   public IActionResult LendingBook(int id)
   {
       Book book = _context.Books.FirstOrDefault(b => b.Id == id);
       if (book == null)
       {
           return NotFound();
       }
       return View(book);
   }
   ```

6. Inmediatamente antes del método, agrega `[Authorize]`.
7. Localiza la acción `LendingBookPost(int id)`:

   ```csharp
   [HttpPost, ActionName("LendingBook")]
   public async Task<IActionResult> LendingBookPost(int id)
   {
       var bookToUpdate = _context.Books.FirstOrDefault(b => b.Id == id);
       bookToUpdate.Available = false;
       if (await TryUpdateModelAsync<Book>(
           bookToUpdate,
           "",
           b => b.Available))
       {
           _context.SaveChanges();
           return RedirectToAction(nameof(Index));
       }
       return View(bookToUpdate);
   }
   ```

8. Inmediatamente antes de esa acción, agrega `[Authorize]`. **Nota:** Aunque el texto original menciona `LibraryContext.cs` en los pasos 3, 5 y 7, los métodos mostrados pertenecen a `HomeController.cs`, abierto en el paso 1.

### Tarea 5: Ejecutar la aplicación

1. Selecciona **File > Save All**.
2. Selecciona **Debug > Start Debugging**. **Nota:** Sin iniciar sesión, debes poder abrir **Home** y la página **Privacy** desde el pie de página.
3. Haz clic en **Books**. Debe aparecer la pantalla de inicio de sesión.
4. Inicia sesión con `mary@foo.com` y `FooBar11!`. Ahora debe mostrarse la página de libros.
5. Abre uno de los enlaces **Borrow a Book** y comprueba que puedes tomar prestado un libro.
6. Cierra Microsoft Edge.

**Resultado:** Identity quedó configurado y `[Authorize]` limita el acceso al contenido indicado.

## Ejercicio 2: Agregar autorización

Agregarás `[Authorize]` a las acciones de `LibrarianController` y después limitarás esas acciones al rol `admin`.

### Tarea 1: Habilitar la autorización

1. En **Solution Explorer**, abre `Program.cs`.
2. Localiza el registro de `AddDefaultIdentity<IdentityUser>` que configura `RequireConfirmedAccount = true`.
3. Al final de esa configuración, agrega `.AddRoles<IdentityRole>()`.
4. Comprueba que el bloque completo quede así:

   ```csharp
   builder.Services.AddDefaultIdentity<IdentityUser>(
       options => options.SignIn.RequireConfirmedAccount = true)
       .AddRoles<IdentityRole>()
       .AddEntityFrameworkStores<LibraryContext>();
   ```

5. Abre `Views/Shared/_Layout.cshtml`.
6. Localiza el elemento de navegación **Books**:

   ```html
   <li class="nav-item">
       <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="GetBooksByGenre">Books</a>
   </li>
   ```

7. Después, agrega el elemento **Admin**, visible únicamente para el rol `admin`:

   ```cshtml
   @if (User.IsInRole("admin"))
   {
       <li class="nav-item">
           <a class="nav-link text-dark" asp-area="" asp-controller="Librarian" asp-action="Index">Admin</a>
       </li>
   }
   ```

8. Abre `Controllers/LibrarianController.cs`.
9. Antes de `public IActionResult Index()`, agrega `[Authorize]`. **Nota:** Antes de esta protección, un visitante podría intentar abrir `/Librarian/Index` directamente.
10. Antes de `public IActionResult AddBook()`, agrega `[Authorize]`.
11. Antes de `public IActionResult AddBookPost(Book book)`, agrega `[Authorize]`.

### Tarea 2: Ejecutar la aplicación

1. Selecciona **File > Save All**.
2. Selecciona **Debug > Start Without Debugging**.
3. En el navegador, haz clic en **Login**.
4. Inicia sesión con `mary@foo.com` y `FooBar11!`. **Nota:** Mary no ve **Admin**, porque solo Bill recibió ese rol en los datos iniciales.
5. Cambia la ruta del navegador de `https://localhost:7143/` a `https://localhost:7143/Librarian/Index`. **Nota:** Mary puede acceder a la página de administración escribiendo la dirección directamente; corregirás esto en la siguiente tarea.
6. Cierra Microsoft Edge.

### Tarea 3: Agregar el rol a los atributos Authorize

1. Abre `Controllers/LibrarianController.cs`.
2. Localiza el atributo `[Authorize]` colocado antes de `Index()`.
3. Cámbialo por `[Authorize(Roles = "admin")]`:

   ```csharp
   [Authorize(Roles = "admin")]
   public IActionResult Index()
   {
       return View();
   }
   ```

4. Aplica el mismo cambio a los otros dos atributos `[Authorize]` de `LibrarianController.cs`.

### Tarea 4: Ejecutar la aplicación

1. Selecciona **File > Save All**.
2. Selecciona **Debug > Start Debugging**.
3. Haz clic en **Login**.
4. Inicia sesión con `mary@foo.com` y `FooBar11!`.
5. Abre `https://localhost:7143/Librarian/Index`. **Nota:** Ahora debe aparecer **Access denied** porque Mary no tiene el rol `admin`.
6. Haz clic en **Logout**.
7. Inicia sesión con `bill@foo.com` y `FooBar11!`. **Nota:** Bill sí debe ver **Admin** en el menú.
8. Haz clic en **Admin** y comprueba que se abra **Admin Portal**.
9. Haz clic en **Add Book to Library**.
10. En **Genre**, selecciona **Mystery**.
11. En **Name**, escribe `The Riverside Villas Murder`.
12. En **Author**, escribe `Kingsley Amis`.
13. En **Date Published**, selecciona el 1 de septiembre de 1973 o cualquier otra fecha.
14. En **Photo**, importa `D:\Allfiles\Mod11\Labfiles\Image\book.jpg` y haz clic en **Add a Book**.
15. Haz clic en **Back to Our Books**. Comprueba que el libro aparezca en la lista.
16. Haz clic en **Logout**.
17. Cierra Microsoft Edge.

**Resultado:** La aplicación usa autorización basada en roles.

## Ejercicio 3: Demostrar la protección contra ataques CSRF

Primero crearás una página que envía una solicitud de préstamo desde otro sitio. Después comprobarás que el ataque puede realizarse y agregarás `[ValidateAntiForgeryToken]` para impedirlo.

### Tarea 1: Escribir el ataque de falsificación de solicitudes

1. En **Solution Explorer**, haz clic derecho en `CrossSiteRequestForgeryAttack` y selecciona **Add > New Folder**.
2. Nombra la carpeta `Controllers` y presiona **Enter**.
3. Haz clic derecho en `Controllers` y selecciona **Add > Controller…**.
4. En **Add New Scaffolded Item**, elige **MVC Controller - Empty** y haz clic en **Add**.
5. En **Name**, indica `HomeController.cs` y haz clic en **Add**. **Nota:** La instrucción original dice `HomeController.cshtml` en este paso, pero el archivo del controlador y el paso siguiente indican `HomeController.cs`.
6. En `HomeController.cs`, haz clic derecho en `public IActionResult Index()` y selecciona **Add View…**.
7. En **Add New Scaffolded Item**, selecciona **Razor View** y haz clic en **Add**.
8. En **Add Razor View**, verifica que el nombre sea `Index` y que estén desmarcadas **Create as partial view** y **Use a layout page**; después haz clic en **Add**.
9. En `Index.cshtml`, localiza `<title>Index</title>`.
10. Debajo, agrega:

    ```html
    <link href="~/css/style.css" rel="stylesheet" />
    ```

11. Dentro de `<body>`, agrega:

    ```html
    <h1>Cross-Site Request Forgery Attack</h1>
    <h3>Click - Submit to Perform the Book Borrowing Attack</h3>
    <form action="https://localhost:7143/Home/LendingBook" method="post">
        <input name="id" type="number" value="2" />
        <input type="submit" value="Attack">
    </form>
    ```

### Tarea 2: Ejecutar la aplicación y comprobar el ataque

1. En Visual Studio, selecciona **File > Save All**.
2. En **Solution Explorer**, haz clic derecho en `Library` y selecciona **Debug > Start New Instance**. **Nota:** El menú muestra **Login**, lo que indica que aún no has iniciado sesión.
3. En el navegador, haz clic en **Login**.
4. Inicia sesión con `mary@foo.com` y `FooBar11!`. Ahora puedes abrir **Books**.
5. En **Solution Explorer**, haz clic derecho en `CrossSiteRequestForgeryAttack` y selecciona **Debug > Start Without Debugging**. Si es necesario, muestra **Solution Explorer** desde su pestaña lateral.
6. En la página **Cross-Site Request Forgery Attack**, haz clic en **Attack**. **Nota:** El navegador abre la confirmación del préstamo y muestra que sigues conectado como `mary@foo.com`, aunque la solicitud provino de otro sitio.
7. Cierra Microsoft Edge.

### Tarea 3: Evitar el ataque CSRF

1. En el proyecto **Library**, abre `Controllers/HomeController.cs`.
2. Localiza la acción `LendingBookPost(int id)`, precedida por `[HttpPost, ActionName("LendingBook")]`.
3. Antes de esa acción, agrega `[ValidateAntiForgeryToken]`:

   ```csharp
   [ValidateAntiForgeryToken]
   [HttpPost, ActionName("LendingBook")]
   public async Task<IActionResult> LendingBookPost(int id)
   ```

### Tarea 4: Ejecutar la aplicación y comprobar que el ataque falla

1. Selecciona **File > Save All**.
2. En **Solution Explorer**, haz clic derecho en `Library` y selecciona **Debug > Start New Instance**. **Nota:** **Login** aparece en el menú porque aún no has iniciado sesión.
3. Haz clic en **Login**.
4. Inicia sesión con `mary@foo.com` y `FooBar11!`.
5. Si `CrossSiteRequestForgeryAttack` ya no se está ejecutando, haz clic derecho en el proyecto y selecciona **Debug > Start Without Debugging**.
6. En la página del ataque, haz clic en **Attack**. **Nota:** El navegador debe mostrar un error **400** y el libro no se toma prestado.
7. Cierra Microsoft Edge. Si aparece **Do you want to close all tabs?**, haz clic en **Close all**.
8. En la ventana **Library (Running)** de Visual Studio, selecciona **Debug > Stop Debugging**.

**Resultado:** Has comprobado que el sitio rechaza la falsificación de solicitudes entre sitios (XSRF/CSRF).
