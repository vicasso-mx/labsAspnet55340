# Módulo 6: Desarrollo de modelos

# Laboratorio: Desarrollo de modelos

## Escenario

Está planeando crear y programar un modelo Modelo-Vista-Controlador (MVC) que se utilizará en una aplicación de tienda de mariposas. El modelo incluye propiedades que describen una mariposa. El modelo debe permitir que la aplicación almacene las mariposas cargadas.

## Ejercicio 1: Agregar un modelo

### Escenario

En este ejercicio:

- Agregará modelos a la aplicación web y los usará en las vistas.
- Agregará una acción `Create` (GET).
- Agregará una acción `Create` (POST).

Las tareas principales de este ejercicio son las siguientes:

- Crear un modelo nuevo.
- Usar el modelo en una vista.
- Pasar el modelo del controlador a una vista.
- Ejecutar la aplicación.
- Escribir una acción GET.
- Escribir una acción POST que acepte el modelo.

### Tarea 1: Crear un modelo nuevo

1. Vaya a `D:\Allfiles\Mod06\Labfiles\01_ButterfliesShop_begin` y haga doble clic en `ButterfliesShop.sln`.

   **Nota:** Si aparece el cuadro de diálogo `Security Warning for ButterfliesShop`, compruebe que la casilla `Ask me for every project in this solution` esté desmarcada y, a continuación, haga clic en `OK`.

2. En la ventana `ButterfliesShop - Microsoft Visual Studio`, en `Solution Explorer`, expanda `Models` y haga clic en `Butterfly.cs`.
3. En el bloque de código de `Butterfly.cs`, observe que contiene el siguiente código que define el modelo `Butterfly`:

   ```csharp
   public int Id { get; set; }
   public string CommonName { get; set; }
   public Family? ButterflyFamily { get; set; }
   public int? Quantity { get; set; }
   public string Characteristics { get; set; }
   public DateTime CreatedDate { get; set; }
   public IFormFile PhotoAvatar { get; set; }
   public string ImageName { get; set; }
   public byte[] PhotoFile { get; set; }
   public string ImageMimeType { get; set; }
   ```

4. En la ventana `ButterfliesShop - Microsoft Visual Studio`, en `Solution Explorer`, haga clic con el botón derecho en `Models`, señale `Add` y haga clic en `Class…`.
5. En el cuadro de diálogo `Add New Item - ButterfliesShop`, en el cuadro `Name`, escriba `IndexViewModel` y haga clic en `Add`.
6. En el bloque de código de `IndexViewModel.cs`, coloque el cursor del mouse después del segundo signo `{` (llave de apertura), presione Entrar y escriba el siguiente código:

   ```csharp
   public List<Butterfly> Butterflies { get; set; }
   ```

### Tarea 2: Usar el modelo en una vista

1. En la ventana `ButterfliesShop - Microsoft Visual Studio`, en `Solution Explorer`, expanda `Views` y después `Butterfly`.
2. En la ventana `ButterfliesShop - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Views` y `Butterfly`, haga clic en `Index.cshtml`.
3. En la ventana de código de `Index.cshtml`, coloque el cursor al principio del documento, escriba el siguiente código y presione Entrar:

   ```html
   @model ButterfliesShop.Models.IndexViewModel
   ```

4. En el elemento `body` de la ventana de código de `Index.cshtml`, escriba el siguiente código:

   ```html
   <div class="container">
       <h1 class="main-title">Butterflies Shop</h1>
       <p class="into">Welcome to our Web Store, Enjoy a Wide Variety of Butterflies</p>
       <p class="into">Our Butterflies in the Shop</p>
       <button type="button" onclick="location.href='@Url.Action("Create", "Butterfly")'">Add Butterflies</button>
   </div>
   ```

5. Coloque el cursor al final del elemento `button` que acaba de crear, presione Entrar y escriba el siguiente código:

   ```html
   <div class="img-container">
       @foreach (var item in Model.Butterflies)
       {
       }
   </div>
   ```

6. Coloque el cursor dentro del bloque de código `foreach`, presione Entrar y escriba el siguiente código:

   ```html
   <div class="photo-index-card">
           <p>@item.CommonName</p>
           <p>@item.ButterflyFamily</p>
           <p>@item.Quantity</p>
           <p>@item.CreatedDate</p>
   </div>
   ```

### Tarea 3: Pasar el modelo del controlador a una vista

1. En la ventana `ButterfliesShop - Microsoft Visual Studio`, en `Solution Explorer`, expanda `Controllers` y haga clic en `ButterflyController.cs`.
2. Coloque el cursor al final del bloque de código del método `InitializeButterfliesData`, presione Entrar dos veces y escriba el siguiente código:

   ```csharp
   public IActionResult Index()
   {
       IndexViewModel indexViewModel = new IndexViewModel();
       indexViewModel.Butterflies = _data.ButterfliesList;
       return View(indexViewModel);
   }
   ```

### Tarea 4: Ejecutar la aplicación

1. En la ventana `ButterfliesShop - Microsoft Visual Studio`, en el menú `File`, haga clic en `Save All`.
2. En la ventana `ButterfliesShop - Microsoft Visual Studio`, en el menú `Debug`, haga clic en `Start Without Debugging`.

   **Nota:** El navegador muestra el resultado de la acción `Index` dentro del controlador `Butterfly`.

3. En Microsoft Edge, haga clic en `Close`.

### Tarea 5: Escribir una acción GET

1. En la ventana `ButterfliesShop - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Controllers`, haga clic en `ButterflyController.cs`.
2. Coloque el cursor al final del bloque de código de la acción `GetImage`, presione Entrar dos veces y escriba el siguiente código:

   ```csharp
   [HttpGet]
   public IActionResult Create()
   {
       return View();
   }
   ```

### Tarea 6: Escribir una acción POST que acepte el modelo

1. En la ventana de código de `ButterflyController.cs`, asegúrese de que el cursor esté al final del bloque de código de la acción `Create` con el verbo HTTP GET, presione Entrar y escriba el siguiente código (ignore el error del método `Create`; se corregirá en los pasos siguientes):

   ```csharp
   [HttpPost]
   public IActionResult Create(Butterfly butterfly)
   {
   }
   ```

2. En el bloque de código de la acción `Create` creada con el verbo HTTP POST, escriba el siguiente código y presione Entrar:

   ```csharp
   Butterfly lastButterfly = _data.ButterfliesList.LastOrDefault();
   butterfly.CreatedDate = DateTime.Today;
   if (butterfly.PhotoAvatar != null && butterfly.PhotoAvatar.Length > 0)
   {
       butterfly.ImageMimeType = butterfly.PhotoAvatar.ContentType;
       butterfly.ImageName = Path.GetFileName(butterfly.PhotoAvatar.FileName);
       butterfly.Id = lastButterfly.Id + 1;
       _butterfliesQuantityService.AddButterfliesQuantityData(butterfly);
       using (var memoryStream = new MemoryStream())
       {
           butterfly.PhotoAvatar.CopyTo(memoryStream);
           butterfly.PhotoFile = memoryStream.ToArray();
       }
       _data.AddButterfly(butterfly);
       return RedirectToAction("Index");
   }
   ```

3. En el bloque de código de la acción `Create` creada con el verbo HTTP POST, inmediatamente después del código que acaba de agregar, presione Entrar y escriba el siguiente código:

   ```csharp
   return View(butterfly);
   ```

**Resultados:** Después de completar este ejercicio, habrá creado un modelo, pasado el modelo de una acción a una vista y usado el modelo en la vista.

## Ejercicio 2: Trabajar con formularios

### Escenario

En este ejercicio:

- Agregará anotaciones de datos `Display` y `Edit` a un modelo `Butterfly`.
- Agregará asistentes de visualización a `Index.cshtml`.
- Agregará asistentes de formularios y de edición a `Create.cshtml`.

Las tareas principales de este ejercicio son las siguientes:

- Agregar anotaciones de datos de visualización y edición a un modelo.
- Actualizar una acción para que devuelva `FileContentResult`.
- Agregar asistentes de visualización.
- Agregar asistentes de formularios.
- Agregar asistentes de edición.
- Ejecutar la aplicación.

### Tarea 1: Agregar anotaciones de datos de visualización y edición a un modelo

1. En la ventana `ButterfliesShop - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Models`, haga clic en `Butterfly.cs`.
2. En la ventana de código de `Butterfly.cs`, coloque el cursor al principio de la línea de código que declara la propiedad `CommonName`, presione Entrar e inserte el siguiente código:

   ```csharp
   [Display(Name = "Common Name:")]
   ```

3. Del mismo modo, agregue el siguiente atributo a la propiedad `ButterflyFamily`:

   ```csharp
   [Display(Name = "Butterfly Family:")]
   ```

4. Del mismo modo, agregue el siguiente atributo a la propiedad `Quantity`:

   ```csharp
   [Display(Name = "Butterflies Quantity:")]
   ```

5. Agregue el siguiente atributo a la propiedad `Characteristics`:

   ```csharp
   [Display(Name = "Characteristics:")]
   ```

6. Agregue los siguientes atributos a la propiedad `CreatedDate`:

   ```csharp
   [DataType(DataType.DateTime)]
   [Display(Name = "Updated on:")]
   [DisplayFormat(DataFormatString = "{0:dd/MM/yy}")]
   ```

7. Agregue el siguiente atributo a la propiedad `PhotoAvatar`:

   ```csharp
   [Display(Name = "Butterflies Picture:")]
   ```

### Tarea 2: Actualizar una acción para que devuelva FileContentResult

1. En la ventana `ButterfliesShop - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Controllers`, haga clic en `ButterflyController.cs`.
2. En el bloque de código de la acción `GetImage`, localice el siguiente código:

   ```csharp
   Butterfly requestedButterfly = _data.GetButterflyById(id);
   if (requestedButterfly != null)
   {
       return null;
   }
   else
   {
       return NotFound();
   }
   ```

3. En el bloque de código de la instrucción `if`, seleccione el siguiente código:

   ```csharp
   return null;
   ```

4. Reemplace el código seleccionado por el siguiente:

   ```csharp
   string webRootpath = _environment.WebRootPath;
   string folderPath = "\\images\\";
   string fullPath = webRootpath + folderPath + requestedButterfly.ImageName;
   if (System.IO.File.Exists(fullPath))
   {
       FileStream fileOnDisk = new FileStream(fullPath, FileMode.Open);
       byte[] fileBytes;
       using (BinaryReader br = new BinaryReader(fileOnDisk))
       {
           fileBytes = br.ReadBytes((int)fileOnDisk.Length);
       }
       return File(fileBytes, requestedButterfly.ImageMimeType);
   }
   else
   {
       if (requestedButterfly.PhotoFile.Length > 0)
       {
           return File(requestedButterfly.PhotoFile, requestedButterfly.ImageMimeType);
       }
       else
       {
           return NotFound();
       }
   }
   ```

### Tarea 3: Agregar asistentes de visualización

1. En la ventana `ButterfliesShop - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Views` y `Butterfly`, haga clic en `Index.cshtml`.
2. En la ventana de código de `Index.cshtml`, seleccione el siguiente código:

   ```html
   <p>@item.CommonName</p>
   <p>@item.ButterflyFamily</p>
   <p>@item.Quantity</p>
   <p>@item.CreatedDate</p>
   ```

3. Reemplace el código seleccionado por el siguiente (puede ajustar el código pegado con la tecla Tab para alinearlo con el resto):

   ```html
   <h3 class="display-picture-title">
       "@Html.DisplayFor(modelItem => item.CommonName)"
   </h3>
   @if (item.ImageName != null)
   {
       <div class="photo-display">
           <img class="photo-display-img" src="@Url.Action("GetImage", "Butterfly", new { Id = item.Id })" />
       </div>
   }
   ```

4. Coloque el cursor al final del bloque de código `if`, presione Entrar y escriba el siguiente código:

   ```html
   <div>
       <p class="display-label">
           @Html.DisplayNameFor(model => item.ButterflyFamily)
       </p>
       <br />
       <p class="display-field">
           @Html.DisplayFor(model => item.ButterflyFamily)
       </p>
   </div>
   <div class="display-info">
       <p class="display-label">
           @Html.DisplayNameFor(model => item.Characteristics)
       </p>
       <p class="display-field">
           @Html.DisplayFor(model => item.Characteristics)
       </p>
   </div>
   <div>
       <p class="display-label">
           @Html.DisplayNameFor(model => item.Quantity)
       </p>
       <p class="display-field">
           @Html.DisplayFor(model => item.Quantity)
       </p>
   </div>
   <div>
       <p class="display-label">
           @Html.DisplayNameFor(model => item.CreatedDate)
       </p>
       <p class="display-field">
           @Html.DisplayFor(model => item.CreatedDate)
       </p>
   </div>
   ```

### Tarea 4: Agregar asistentes de formularios

1. En la ventana `ButterfliesShop - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Views` y `Butterfly`, haga clic en `Create.cshtml`.
2. En la ventana de código de `Create.cshtml`, coloque el cursor al principio del documento, escriba el siguiente código y presione Entrar:

   ```html
   @using ButterfliesShop.Models
   @model ButterfliesShop.Models.Butterfly
   ```

3. En el elemento `BODY` de la ventana de código de `Create.cshtml`, escriba el siguiente código:

   ```html
   <div class="container">
       <h1 class="main-title">Add Butterflies to the Shop</h1>
       <form method="post" enctype="multipart/form-data" asp-action="Create">
       </form>
   </div>
   ```

### Tarea 5: Agregar asistentes de edición

1. En el elemento `FORM` de la ventana de código de `Create.cshtml`, escriba el siguiente código:

   ```html
   <div class="form-field">
       <label asp-for="CommonName"></label>
       <input asp-for="CommonName" />
   </div>
   <div class="form-field">
       <label asp-for="ButterflyFamily"></label>
       <select asp-for="ButterflyFamily" asp-items="Html.GetEnumSelectList<Family>()">
           <option selected="selected" value="">Select</option>
       </select>
   </div>
   <div class="form-field">
       <label asp-for="Characteristics"></label>
       <textarea asp-for="Characteristics"></textarea>
   </div>
   <div class="form-field">
       <label asp-for="Quantity"></label>
       <input asp-for="Quantity" />
   </div>
   <div class="form-field">
       <label asp-for="PhotoAvatar"></label>
       <input asp-for="PhotoAvatar" type="file" />
   </div>
   <div class="form-field">
       <input type="submit" value="Submit" />
   </div>
   ```

### Tarea 6: Ejecutar la aplicación

1. En la ventana `ButterfliesShop - Microsoft Visual Studio`, en el menú `File`, haga clic en `Save All`.
2. En la ventana `ButterfliesShop - Microsoft Visual Studio`, en el menú `Debug`, haga clic en `Start Without Debugging`.

   **Nota:** El navegador muestra el resultado de la acción `Index` dentro del controlador `Butterfly`.

3. En Microsoft Edge, haga clic en `Add Butterflies`.

   **Nota:** El navegador muestra el resultado de la acción `Create` con el verbo HTTP GET dentro del controlador `Butterfly`.

4. En la página `Add Butterflies to The Shop`, en el cuadro `Common Name`, escriba `Red Admiral`.
5. En la página `Add Butterflies to The Shop`, en el cuadro `Butterfly Family`, seleccione `Nymphalidae`.
6. En la página `Add Butterflies to The Shop`, en el cuadro `Characteristics`, escriba lo siguiente:

   ```text
   Common throughout Europe, Asia, North Africa, and North America in open ground, marshes, woods, and fields from May to October.
   ```

7. En la página `Add Butterflies to The Shop`, en el cuadro `Butterflies Quantity`, escriba `25`.
8. En la página `Add Butterflies to The Shop`, en el cuadro `Butterflies Picture`, haga clic en `Choose File`, seleccione la imagen `D:\AllFiles\Mod06\Labfiles\Images\red-admiral.jpg` y haga clic en `Submit`.

   **Nota:** Compruebe que los detalles de la mariposa enviada sean válidos y que se haya agregado una mariposa nueva a la página de inicio.

9. En Microsoft Edge, haga clic en `Close`.

**Resultados:** Después de completar este ejercicio, podrá agregar anotaciones de datos a la clase de modelo `Butterfly` de la aplicación y agregar asistentes de etiquetas a las vistas.

## Ejercicio 3: Agregar validación

### Escenario

En este ejercicio:

- Agregará anotaciones de datos de validación a un modelo de mariposa.
- Agregará asistentes de validación a la vista `Create`.
- Agregará la propiedad `ModelState.IsValid` en `ButterflyController`.
- Agregará la validación personalizada `MaxButterflyQuantityValidation`.

Las tareas principales de este ejercicio son las siguientes:

- Agregar anotaciones de datos de validación a un modelo.
- Agregar asistentes de validación a una vista.
- Usar la propiedad `ModelState.IsValid` en un controlador.
- Ejecutar la aplicación.
- Agregar validación personalizada.
- Ejecutar la aplicación.

### Tarea 1: Agregar anotaciones de datos de validación a un modelo

1. En la ventana `ButterfliesShop - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Models`, haga clic en `Butterfly.cs`.
2. En la ventana de código de `Butterfly.cs`, en el código de la propiedad `CommonName`, coloque el cursor al final del atributo de datos `Display`, presione Entrar y escriba el siguiente código:

   ```csharp
   [Required(ErrorMessage = "Please enter the butterfly name")]
   ```

3. Del mismo modo, agregue otro atributo de datos a la propiedad `ButterflyFamily`:

   ```csharp
   [Required(ErrorMessage = "Please select the butterfly family")]
   ```

4. Agregue otro atributo de datos a la propiedad `Quantity`:

   ```csharp
   [Required(ErrorMessage = "Please select the butterfly quantity")]
   ```

5. Agregue más atributos de datos a la propiedad `Characteristics`:

   ```csharp
   [Required(ErrorMessage = "Please type the characteristics")]
   [StringLength(50)]
   ```

6. Agregue otro atributo de datos a la propiedad `PhotoAvatar`:

   ```csharp
   [Required(ErrorMessage = "Please select the butterflies picture")]
   ```

### Tarea 2: Agregar asistentes de validación a una vista

1. En la ventana `ButterfliesShop - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Views` y `Butterfly`, haga clic en `Create.cshtml`.
2. En la ventana de código de `Create.cshtml`, localice el siguiente código:

   ```html
   <form method="post" enctype="multipart/form-data" asp-action="Create">
   ```

3. Coloque el cursor al final del código localizado, presione Entrar y escriba el siguiente código:

   ```html
   <div asp-validation-summary="All"></div>
   ```

4. En la ventana de código de `Create.cshtml`, localice el siguiente código:

   ```html
   <input asp-for="CommonName" />
   ```

5. En la ventana de código de `Create.cshtml`, coloque el cursor al final del código localizado, presione Entrar y escriba el siguiente código:

   ```html
   <span asp-validation-for="CommonName"></span>
   ```

6. En la ventana de código de `Create.cshtml`, localice el siguiente código:

   ```html
   <select asp-for="ButterflyFamily" asp-items="Html.GetEnumSelectList<Family>()">
       <option selected="selected" value="">Select</option>
   </select>
   ```

7. En la ventana de código de `Create.cshtml`, coloque el cursor al final del código localizado, presione Entrar y escriba el siguiente código:

   ```html
   <span asp-validation-for="ButterflyFamily"></span>
   ```

8. En la ventana de código de `Create.cshtml`, localice el siguiente código:

   ```html
   <textarea asp-for="Characteristics"></textarea>
   ```

9. En la ventana de código de `Create.cshtml`, coloque el cursor al final del código localizado, presione Entrar y escriba el siguiente código:

   ```html
   <span asp-validation-for="Characteristics"></span>
   ```

10. En la ventana de código de `Create.cshtml`, localice el siguiente código:

    ```html
    <input asp-for="Quantity" />
    ```

11. En la ventana de código de `Create.cshtml`, coloque el cursor al final del código localizado, presione Entrar y escriba el siguiente código:

    ```html
    <span asp-validation-for="Quantity"></span>
    ```

12. En la ventana de código de `Create.cshtml`, localice el siguiente código:

    ```html
    <input asp-for="PhotoAvatar" type="file" />
    ```

13. En la ventana de código de `Create.cshtml`, coloque el cursor al final del código localizado, presione Entrar y escriba el siguiente código:

    ```html
    <span asp-validation-for="PhotoAvatar"></span>
    ```

### Tarea 3: Usar la propiedad ModelState.IsValid en un controlador

1. En la ventana `ButterfliesShop - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Controllers`, haga clic en `ButterflyController.cs`.
2. En el bloque de código de la acción `Create` creada con el verbo HTTP POST, localice el siguiente código:

   ```csharp
   [HttpPost]
   public IActionResult Create(Butterfly butterfly)
   {
   ```

3. Coloque el cursor después del signo `{` (llave de apertura), presione Entrar y escriba el siguiente código:

   ```csharp
   if (ModelState.IsValid)
   {
   ```

4. En el bloque de código de la acción `Create` creada con el verbo HTTP POST, localice el siguiente código:

   ```csharp
   return View(butterfly);
   ```

5. En el bloque de código de la acción `Create` creada con el verbo HTTP POST, inmediatamente después del código localizado, presione Entrar y escriba el siguiente código:

   ```csharp
   }
   return View(butterfly);
   ```

### Tarea 4: Ejecutar la aplicación

1. En la ventana `ButterfliesShop - Microsoft Visual Studio`, en el menú `File`, haga clic en `Save All`.
2. En la ventana `ButterfliesShop - Microsoft Visual Studio`, en el menú `Debug`, haga clic en `Start Without Debugging`.

   **Nota:** El navegador muestra el resultado de la acción `Index` dentro del controlador `Butterfly`.

3. En Microsoft Edge, haga clic en `Add Butterflies`.

   **Nota:** El navegador muestra el resultado de la acción `Create` con el verbo HTTP GET dentro del controlador `Butterfly`.

4. En la página `Add Butterflies to The Shop`, deje todos los cuadros vacíos y haga clic en `Submit`.

   **Nota:** La ventana del navegador muestra la vista `Create.cshtml` con mensajes de validación debajo de todos los cuadros.

5. En la página `Add Butterflies to The Shop`, en el cuadro `Common Name`, escriba `Red Admiral`.
6. En la página `Add Butterflies to The Shop`, en el cuadro `Butterfly Family`, seleccione `Nymphalidae`.
7. En la página `Add Butterflies to The Shop`, en el cuadro `Characteristics`, escriba lo siguiente:

   ```text
   Common throughout Europe, Asia, North Africa, and North America in open ground, marshes, woods, and fields from May to October.
   ```

8. En la página `Add Butterflies to The Shop`, en el cuadro `Butterflies Quantity`, escriba `25`.
9. En la página `Add Butterflies to The Shop`, en el cuadro `Butterflies Picture`, haga clic en `Choose File`, seleccione la imagen `D:\AllFiles\Mod06\Labfiles\Images\red-admiral.jpg` y haga clic en `Submit`.

   **Nota:** Compruebe que los detalles de la mariposa enviada sean válidos y que se haya agregado una mariposa nueva a la página de inicio.

10. En Microsoft Edge, haga clic en `Close`.

### Tarea 5: Agregar validación personalizada

1. En `Solution Explorer`, haga clic con el botón derecho en `ButterfliesShop`, señale `Add` y haga clic en `New Folder`.
2. En el cuadro `NewFolder`, escriba `Validators` y presione Entrar.
3. En la ventana `ButterfliesShop - Microsoft Visual Studio`, en `Solution Explorer`, haga clic con el botón derecho en `Validators`, señale `Add` y haga clic en `Class…`.
4. En el cuadro de diálogo `Add New Item - ButterfliesShop`, en el cuadro `Name`, escriba `MaxButterflyQuantityValidation.cs` y haga clic en `Add`.
5. En la ventana de código de `MaxButterflyQuantityValidation.cs`, localice el siguiente código:

   ```csharp
   public class MaxButterflyQuantityValidation
   ```

6. Agregue una clase base `ValidationAttribute` para que se vea de la siguiente manera:

   ```csharp
   public class MaxButterflyQuantityValidation : ValidationAttribute
   ```

7. En el bloque de código de la clase `MaxButterflyQuantityValidation`, inserte el siguiente código:

   ```csharp
   private int _maxAmount;
   public MaxButterflyQuantityValidation(int maxAmount)
   {
       _maxAmount = maxAmount;
   }
   ```

8. Coloque el cursor al final del bloque de código del constructor, presione Entrar dos veces y agregue el siguiente método:

   ```csharp
   protected override ValidationResult IsValid(object value, ValidationContext validationContext)
   {
       var service = (IButterfliesQuantityService)validationContext.GetService(typeof(IButterfliesQuantityService));
       Butterfly butterfly = (Butterfly)validationContext.ObjectInstance;
       if (butterfly.ButterflyFamily != null)
       {
           int? quantity = service.GetButterflyFamilyQuantity(butterfly.ButterflyFamily.Value);
           int? sumQuantity = quantity + butterfly.Quantity;
           if (sumQuantity > _maxAmount)
           {
               return new ValidationResult(string.Format("Limit of butterflies from the same family in the store is {0} butterflies. Currently there are {1}.", _maxAmount, quantity));
           }
           return ValidationResult.Success;
       }
       return ValidationResult.Success;
   }
   ```

9. En la ventana `ButterfliesShop - Microsoft Visual Studio`, en `Solution Explorer`, expanda `Models` y haga clic en `Butterfly.cs`.
10. En la ventana de código de `Butterfly.cs`, localice la propiedad `Quantity`:

    ```csharp
    public int? Quantity { get; set; }
    ```

11. Agregue una anotación nueva con el atributo de validador personalizado:

    ```csharp
    [MaxButterflyQuantityValidation(50)]
    ```

### Tarea 6: Ejecutar la aplicación

1. En la ventana `ButterfliesShop - Microsoft Visual Studio`, en el menú `File`, haga clic en `Save All`.
2. En la ventana `ButterfliesShop - Microsoft Visual Studio`, en el menú `Debug`, haga clic en `Start Without Debugging`.

   **Nota:** El navegador muestra el resultado de la acción `Index` dentro del controlador `Butterfly`.

3. En Microsoft Edge, haga clic en `Add Butterflies`.

   **Nota:** El navegador muestra el resultado de la acción `Create` con el verbo HTTP GET dentro del controlador `Butterfly`.

4. En la página `Add Butterflies to The Shop`, en el cuadro `Common Name`, escriba `Red Admiral`.
5. En la página `Add Butterflies to The Shop`, en el cuadro `Butterfly Family`, seleccione `Nymphalidae`.
6. En la página `Add Butterflies to The Shop`, en el cuadro `Characteristics`, escriba lo siguiente:

   ```text
   Common throughout Europe, Asia, North Africa, and North America in open ground, marshes, woods, and fields from May to October.
   ```

7. En la página `Add Butterflies to The Shop`, en el cuadro `Butterflies Quantity`, escriba `60`.
8. En la página `Add Butterflies to The Shop`, en el cuadro `Butterflies Picture`, haga clic en `Choose File`, seleccione la imagen `D:\AllFiles\Mod06\Labfiles\Images\red-admiral.jpg` y haga clic en `Submit`.

   **Nota:** La ventana del navegador muestra la vista `Create.cshtml` con un mensaje de validación personalizada debajo del cuadro `Butterflies Quantity`.

9. En la página `Add Butterflies to The Shop`, en el cuadro `Butterflies Quantity`, escriba `2`.
10. En la página `Add Butterflies to The Shop`, en el cuadro `Butterflies Picture`, haga clic en `Choose File`, seleccione la imagen `D:\AllFiles\Mod06\Labfiles\Images\red-admiral.jpg` y haga clic en `Submit`.

    **Nota:** Compruebe que los detalles de la mariposa enviada sean válidos y que se haya agregado una mariposa nueva a la página de inicio.

11. En Microsoft Edge, haga clic en `Close`.
12. En la ventana `ButterfliesShop - Microsoft Visual Studio`, en el menú `File`, haga clic en `Exit`.

**Resultados:** Al final de este ejercicio, habrá creado una aplicación `ButterfliesShop` en la que los usuarios pueden enviar diversas mariposas a la tienda.
