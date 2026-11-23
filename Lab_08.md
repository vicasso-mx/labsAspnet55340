# Módulo 8: Uso de diseños, CSS y JavaScript en ASP.NET Core

# Laboratorio: Uso de diseños, CSS y JavaScript en ASP.NET Core

## Escenario

Se le ha pedido agregar a la página de inicio de una aplicación web de zoológico una presentación de diapositivas que muestre algunas fotografías de los animales. La presentación mostrará cada fotografía en tamaño grande. Sin embargo, mostrará una sola fotografía a la vez y recorrerá todas las fotografías en orden.

Quiere usar jQuery para crear esta presentación porque desea recorrer las fotografías en el navegador sin volver a cargar la página cada vez.

También se le ha pedido agregar una página de compra para que los clientes puedan adquirir boletos de adulto, niño y persona mayor para el zoológico. Usará jQuery para hacer cálculos en la página. También usará validación del lado del cliente para validar los datos introducidos por los usuarios.

## Ejercicio 1: Aplicar un diseño y vincularle vistas

### Escenario

Para construir una aplicación web con una apariencia uniforme, debe agregarse un diseño a la aplicación web. En este ejercicio creará un diseño y vinculará vistas a él.

Las tareas principales de este ejercicio son las siguientes:

- Crear un diseño.
- Agregar una vista y vincularla al diseño.
- Agregar `_ViewStart.cshtml`.
- Agregar vistas existentes a la aplicación web.
- Agregar una sección al diseño.
- Ejecutar la aplicación.

### Tarea 1: Crear un diseño

1. Vaya a `D:\Allfiles\Mod08\Labfiles\01_ZooSite_begin` y haga doble clic en `ZooSite.sln`.

   **Nota:** Si aparece el cuadro de diálogo `Security Warning for ZooSite`, compruebe que la casilla `Ask me for every project in this solution` esté desmarcada y haga clic en `OK`.

2. En la ventana `ZooSite - Microsoft Visual Studio`, en `Solution Explorer`, haga clic con el botón derecho en `Views`, señale `Add` y haga clic en `New Folder`.
3. En el cuadro `NewFolder`, escriba `Shared` y presione Entrar.
4. En la ventana `ZooSite - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Views`, haga clic con el botón derecho en `Shared`, señale `Add` y haga clic en `New Item…`.
5. En el cuadro de diálogo `Add New Item -- ZooSite`, seleccione `Razor Layout` en la lista de plantillas y haga clic en `Add`.
6. En la ventana de código de `_Layout.cshtml`, coloque el cursor después del signo `>` de la etiqueta `body`, presione Entrar y escriba el siguiente código:

   ```html
   <ul class="nav">
       <li><a href="@Url.Action("Index", "Zoo")">Attractions</a></li>
       <li><a href="@Url.Action("VisitorDetails", "Zoo")">Visitor >[!alert]</a></li>
       <li><a href="@Url.Action("BuyTickets", "Zoo")">Tickets</a></li>
   </ul>
   ```

7. Coloque el cursor después del signo `>` de la etiqueta `</ul>`, presione Entrar y escriba el siguiente código:

   ```html
   <div class="header-container">
       <h1 class="content">Welcome to Zoo Center</h1>
       <div class="slider-buttons">
           <img src="~/images/prevArrow.png" class="prev" onclick="prevImage()" />
           <img src="~/images/nextArrow.png" class="next" onclick="nextImage()" />
       </div>
   </div>
   ```

### Tarea 2: Agregar una vista y vincularla a un diseño

1. En la ventana `ZooSite - Microsoft Visual Studio`, en `Solution Explorer`, expanda `Controllers` y haga clic en `ZooController.cs`.
2. En la ventana de código de `ZooController.cs`, haga clic con el botón derecho en el siguiente código y después haga clic en `Add View…`:

   ```csharp
   public IActionResult Index()
   ```

3. En el cuadro de diálogo `Add New Scaffolded Item`, asegúrese de que esté seleccionada la plantilla `Razor View` (no la plantilla `Empty`) y haga clic en `Add`.
4. En el cuadro de diálogo `Add Razor View`, asegúrese de que el valor del cuadro de texto `View name` sea `Index`.
5. En el cuadro de diálogo `Add Razor View`, asegúrese de que la casilla `Create as a partial view` esté desmarcada y de que la casilla `Use a layout page` esté marcada; después haga clic en el botón `…` (examinar).
6. En el cuadro de diálogo `Select a Layout Page`, bajo `Project folders`, expanda `Views` y haga clic en `Shared`.
7. En el cuadro de diálogo `Select a Layout Page`, bajo `Contents of folder`, haga clic en `_Layout.cshtml` y después en `OK`.
8. En el cuadro de diálogo `Add Razor View`, haga clic en `Add`.
9. En la ventana de código de `Index.cshtml`, coloque el cursor al principio del documento, escriba el siguiente código y presione Entrar:

   ```csharp
   @model IEnumerable<ZooSite.Models.Photo>
   ```

10. En la ventana de código de `Index.cshtml`, seleccione el siguiente código:

    ```html
    <h1>Index</h1>
    ```

11. Reemplace el código seleccionado por el siguiente:

    ```html
    <h1 class="main-title">Zoo Attractions</h1>
    <div class="container">
    </div>
    ```

12. En la ventana de código de `Index.cshtml`, dentro del elemento `DIV`, escriba el siguiente código:

    ```html
    @foreach (var item in Model)
    {
        <div class="photo-index-card">
            @if (item.PhotoFileName != null)
            {
                <div class="image-wrapper">
                    <img class="photo-display-img" src="@Url.Action("GetImage", "Zoo", new { PhotoId = item.PhotoID })" />
                </div>
            }
            <h3 class="display-picture-title">
                @Html.DisplayFor(modelItem => item.Title)
            </h3>
            <div>
                <span class="display">
                    @Html.DisplayFor(model => item.Description)
                </span>
            </div>
        </div>
    }
    ```

### Tarea 3: Agregar _ViewStart.cshtml

1. En la ventana `ZooSite - Microsoft Visual Studio`, en `Solution Explorer`, haga clic con el botón derecho en `Views`, señale `Add` y haga clic en `New Item…`.
2. En el cuadro de diálogo `Add New Item -- ZooSite`, en el panel de la lista de plantillas, haga clic en `Razor View Start` y después en `Add`.
3. En la ventana `ZooSite - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Views`, expanda `Zoo` y haga clic en `Index.cshtml`.
4. En la ventana de código de `Index.cshtml`, elimine el siguiente código:

   ```csharp
   Layout = "~/Views/Shared/_Layout.cshtml";
   ```

### Tarea 4: Agregar vistas existentes a la aplicación web

1. En la ventana `ZooSite - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Views`, haga clic con el botón derecho en `Zoo`, señale `Add` y haga clic en `Existing Item…`.
2. En el cuadro de diálogo `Add Existing Item - ZooSite`, vaya a `D:\Allfiles\Mod08\Labfiles\ZooViews`, seleccione todos los archivos `.cshtml` y haga clic en `Add`.

### Tarea 5: Agregar una sección al diseño

1. En la ventana `ZooSite - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Views` y `Shared`, haga clic en `_Layout.cshtml`.
2. En la ventana de código de `_Layout.cshtml`, localice el siguiente código:

   ```html
   <div>
       @RenderBody()
   </div>
   ```

3. Coloque el cursor después del signo `>` de la etiqueta `</div>`, presione Entrar y escriba el siguiente código:

   ```html
   @RenderSection("Scripts", false)
   ```

### Tarea 6: Ejecutar la aplicación

1. En la ventana `ZooSite -- Microsoft Visual Studio`, en el menú `File`, haga clic en `Save All`.
2. En la ventana `ZooSite - Microsoft Visual Studio`, en el menú `Debug`, haga clic en `Start Without Debugging`.

   **Nota:** El navegador muestra el contenido del archivo `Index.cshtml`, pero el contenido HTML todavía no tiene el estilo de un archivo CSS.

3. En la barra de menús, haga clic en `Visitor >[!alert]`.

   **Nota:** Examine el contenido del navegador.

4. En la barra de menús, haga clic en `Tickets`.

   **Nota:** Examine el contenido del navegador.

5. En Microsoft Edge, haga clic en `Close`.

**Resultados:** Después de completar este ejercicio, habrá agregado un diseño y vinculado vistas a él. También habrá usado el archivo `_ViewStart` en la aplicación web.

## Ejercicio 2: Usar CSS

### Escenario

Para mejorar la apariencia de la aplicación web, debe usarse una hoja de estilos CSS. En este ejercicio agregará un archivo CSS a la aplicación web y un vínculo desde el diseño al archivo CSS.

Las tareas principales de este ejercicio son las siguientes:

- Agregar un archivo CSS existente al proyecto.
- Vincular el diseño al archivo CSS.
- Aplicar estilo al menú.
- Aplicar estilo a la sección de fotografías de `Index.cshtml`.
- Aplicar estilo a un formulario de `BuyTickets.cshtml`.
- Ejecutar la aplicación.

### Tarea 1: Agregar un archivo CSS existente al proyecto

1. En la ventana `ZooSite - Microsoft Visual Studio`, en `Solution Explorer`, haga clic con el botón derecho en `wwwroot`, señale `Add` y haga clic en `New Folder`.
2. En el cuadro `NewFolder`, escriba `css` y presione Entrar.
3. En la ventana `ZooSite - Microsoft Visual Studio`, en `Solution Explorer`, bajo `wwwroot`, haga clic con el botón derecho en `css`, señale `Add` y haga clic en `Existing Item…`.
4. En el cuadro de diálogo `Add Existing Item - ZooSite`, vaya a `D:\Allfiles\Mod08\Labfiles\ZooCSS`, haga clic en `zoo-style.css` y después en `Add`.
5. En la ventana `ZooSite - Microsoft Visual Studio`, en `Solution Explorer`, bajo `wwwroot` y `css`, haga clic en `zoo-style.css`.

   **Nota:** Examine el contenido del archivo.

6. En la ventana `ZooSite - Microsoft Visual Studio`, en `Solution Explorer`, haga clic en `Program.cs`.
7. En la ventana de código de `Program.cs`, compruebe que contenga la siguiente línea de código:

   ```csharp
   app.UseStaticFiles();
   ```

### Tarea 2: Vincular el diseño al archivo CSS

1. En la ventana `ZooSite - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Views` y `Shared`, haga clic en `_Layout.cshtml`.
2. En la ventana de código de `_Layout.cshtml`, localice el siguiente código:

   ```html
   <title>@ViewBag.Title</title>
   ```

3. Coloque el cursor después del signo `>` de la etiqueta `</title>`, presione Entrar y escriba el siguiente código:

   ```html
   <link type="text/css" rel="stylesheet" href="~/css/zoo-style.css"/>
   ```

### Tarea 3: Aplicar estilo al menú

1. En la ventana `ZooSite - Microsoft Visual Studio`, en `Solution Explorer`, bajo `wwwroot` y `css`, haga clic en `zoo-style.css`.
2. Al final del archivo `zoo-style.css`, agregue el siguiente código:

   ```css
   .nav {
       list-style-type: none;
       margin: 0;
       padding: 0;
       overflow: hidden;
       background-color: #85754e;
       position: fixed;
       top: 0;
       left: 0;
       width: 100%;
   }

   .nav li {
       float: left;
   }

   .nav li a {
       display: block;
       color: white;
       text-align: center;
       padding: 14px 16px;
       text-decoration: none;
   }

   .nav li a:hover:not(.active) {
       background-color: #016b6b;
   }

   .active {
       background-color: #008484;
       color: #fff;
   }
   ```

### Tarea 4: Aplicar estilo a la sección de fotografías de Index.cshtml

1. En la ventana de código de `zoo-style.css`, agregue el siguiente código al final del archivo:

   ```css
   .photo-index-card {
       background-color: #ffffff;
       padding: 0;
       margin: 10px 5px 15px 18px;
       padding-bottom: 25px;
       width: 355px;
       border: 1px solid #d6d4d4;
       border-radius: 10px;
       overflow: hidden;
   }
   ```

### Tarea 5: Aplicar estilo a un formulario de BuyTickets.cshtml

1. En la ventana de código de `zoo-style.css`, agregue el siguiente código al final del archivo:

   ```css
   .>[!alert] .form-field {
       text-align:left;
       clear: both;
   }

   .>[!alert] .form-field div {
       width: 172px;
       text-align: right;
       float: right;
   }

   .>[!alert] label {
       width: 118px;
       display: inline-block;
       margin-bottom: 10px;
   }

   .>[!alert] input{
       border-radius: 2px;
       line-height: 20px;
       border: 1px solid #ccc6c6;
       background-color: #f9f6f6;
       width: 100%;
   }
   ```

2. En la ventana de código de `zoo-style.css`, agregue el siguiente código al final del archivo:

   ```css
   input.submit-btn {
       width: 100px;
       margin-top: 12px;
       height: 29px;
       background-color: orange;
       font-weight: bold;
       box-shadow: inset 0px 0px 4px #b77006;
       border: 1px solid #a59797;
   }

   input.submit-btn[disabled] {
       opacity: 0.8;
       background-color: whitesmoke;
       box-shadow: none;
   }
   ```

### Tarea 6: Ejecutar la aplicación

1. En la ventana `ZooSite -- Microsoft Visual Studio`, en el menú `File`, haga clic en `Save All`.
2. En la ventana `ZooSite - Microsoft Visual Studio`, en el menú `Debug`, haga clic en `Start Without Debugging`.

   **Nota:** El navegador muestra el contenido del archivo `Index.cshtml`, con el contenido HTML correctamente estilizado por el archivo CSS.

3. En la página `Zoo Attractions`, en el encabezado `Welcome to Zoo Center`, haga clic en la flecha derecha.

   **Nota:** Por ahora, hacer clic en el botón no tiene efecto porque todavía no se ha agregado código JavaScript a la aplicación web.

4. En la barra de menús, haga clic en `Visitor >[!alert]`.

   **Nota:** Examine el contenido del navegador para comprobar que tenga el estilo correcto.

5. En la barra de menús, haga clic en `Tickets`.
6. En `Step 1 - Choose Tickets`, en cada uno de los cuadros `Adult`, `Child` y `Senior`, seleccione uno o más boletos.

   **Nota:** Por ahora, el costo total de los boletos no se actualiza en la página porque todavía no se ha agregado código JavaScript a la aplicación web.

7. En `Step 2 - Buy Tickets`, escriba su nombre en los cuadros `First Name` y `Last Name`.
8. En `Step 2 - Buy Tickets`, en el cuadro `Address`, escriba su dirección (o invente una).
9. En `Step 2 - Buy Tickets`, en el cuadro `Email`, escriba `abcd`.
10. En `Step 2 - Buy Tickets`, en el cuadro `Phone`, escriba un número al azar.

    **Nota:** Por ahora no puede comprar los boletos (el botón `Buy` está deshabilitado) y no hay validación porque la funcionalidad todavía no se ha aplicado.

11. En Microsoft Edge, haga clic en `Close`.

**Resultados:** Después de completar este ejercicio, habrá agregado un archivo CSS existente a una aplicación web y un vínculo desde un diseño al archivo CSS. También habrá agregado selectores CSS a un archivo CSS.

## Ejercicio 3: Usar JavaScript

### Escenario

Para calcular el costo total de los boletos, se le ha pedido agregar una función en JavaScript. En este ejercicio agregará un archivo JavaScript y un vínculo desde una vista al archivo JavaScript.

Las tareas principales de este ejercicio son las siguientes:

- Agregar un archivo JavaScript.
- Vincular una vista al archivo JavaScript.
- Escribir el código del archivo JavaScript.

### Tarea 1: Agregar un archivo JavaScript

1. En la ventana `ZooSite - Microsoft Visual Studio`, en `Solution Explorer`, haga clic con el botón derecho en `wwwroot`, señale `Add` y haga clic en `New Folder`.
2. En el cuadro `NewFolder`, escriba `js` y presione Entrar.
3. En la ventana `ZooSite -- Microsoft Visual Studio`, en `Solution Explorer`, bajo `wwwroot`, haga clic con el botón derecho en `js`, señale `Add` y haga clic en `New Item…`.
4. En el cuadro de diálogo `Add New Item -- ZooSite`, en el panel de la lista de plantillas, haga clic en `JavaScript File`.
5. En el cuadro de diálogo `Add New Item -- ZooSite`, en el cuadro de texto `Name:`, escriba `form-functions` y haga clic en `Add`.

### Tarea 2: Vincular una vista al archivo JavaScript

1. En la ventana `ZooSite - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Views` y `Zoo`, haga clic en `BuyTickets.cshtml`.
2. En la ventana de código de `BuyTickets.cshtml`, localice el siguiente código:

   ```html
   @section Scripts {
   }
   ```

3. Coloque el cursor después del signo `{` (llave de apertura), presione Entrar y escriba el siguiente código:

   ```html
   <script src="~/js/form-functions.js"></script>
   ```

### Tarea 3: Escribir el código del archivo JavaScript

1. En la ventana `ZooSite -- Microsoft Visual Studio`, en `Solution Explorer`, bajo `wwwroot` y `js`, haga clic en `form-functions.js`.
2. En la ventana de código de `form-functions.js`, escriba el siguiente código:

   ```javascript
   function calculateSum() {
       var rows = document.querySelectorAll("#totalAmount tr .sum");
       var sum = 0;

       for (var i = 0; i < rows.length; i++) {
           sum = sum + parseFloat(parseFloat(rows[i].innerHTML.substring(1, rows[i].innerHTML.length)).toFixed(2));
       }

       document.getElementById("sum").innerHTML = "Total: $" + sum;
   }
   ```

**Resultados:** Después de completar este ejercicio, habrá agregado un archivo JavaScript nativo a la aplicación.

## Ejercicio 4: Usar jQuery

### Escenario

Se le ha pedido controlar eventos de clic, modificar elementos y cambiar su estilo. También se le ha pedido aplicar validación del lado del cliente en la aplicación web. En este ejercicio agregará varios paquetes del lado del cliente a la aplicación web y los usará para agregar funcionalidad del lado del cliente.

Las tareas principales de este ejercicio son las siguientes:

- Agregar una copia de la biblioteca jQuery al proyecto.
- Usar jQuery para agregar controladores de eventos.
- Usar jQuery para modificar elementos.
- Usar jQuery para la validación del lado del cliente.
- Ejecutar la aplicación.

### Tarea 1: Agregar la biblioteca JavaScript de jQuery

1. En la ventana `ZooSite - Microsoft Visual Studio`, en `Solution Explorer`, haga clic con el botón derecho en `ZooSite`, señale `Add` y haga clic en `Client-Side Library…`.
2. En el cuadro de diálogo `Add Client-Side Library`, en el cuadro de búsqueda, escriba `jq` y presione Entrar.
3. En el cuadro de diálogo `Add Client-Side Library`, elija `jquery` en los resultados de búsqueda para obtener la última versión estable de la biblioteca jQuery.
4. En el cuadro de diálogo `Add Client-Side Library`, compruebe que `Target Location` esté establecido en `wwwroot/lib/jquery/` y haga clic en `Install`.
5. En la ventana `ZooSite - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Views` y `Shared`, haga clic en `_Layout.cshtml`.
6. En la ventana de código de `_Layout.cshtml`, localice el siguiente código:

   ```html
   <link type="text/css" rel="stylesheet" href="~/css/zoo-style.css"/>
   ```

7. Coloque el cursor después del signo `>`, presione Entrar y escriba el siguiente código:

   ```html
   <script src="~/lib/jquery/jquery.min.js"></script>
   ```

### Tarea 2: Usar jQuery para agregar controladores de eventos

1. En la ventana `ZooSite -- Microsoft Visual Studio`, en `Solution Explorer`, bajo `wwwroot` y `js`, haga clic en `form-functions.js`.
2. En la ventana de código de `form-functions.js`, localice el siguiente código:

   ```javascript
   function calculateSum() {
       var rows = document.querySelectorAll("#totalAmount tr .sum");
       var sum = 0;
       for (var i = 0; i < rows.length; i++) {
           sum = sum + parseFloat(parseFloat(rows[i].innerHTML.substring(1, rows[i].innerHTML.length)).toFixed(2));
       }
       document.getElementById("sum").innerHTML = "Total: $" + sum ;
   }
   ```

3. Inserte una línea que contenga `$(function() {` antes de este código y agregue una línea que contenga `});` después. El resultado debe verse de la siguiente manera:

   ```javascript
   $(function() {
       function calculateSum() {
           var rows = document.querySelectorAll("#totalAmount tr .sum");
           var sum = 0;
           for (var i = 0; i < rows.length; i++) {
               sum = sum + parseFloat(parseFloat(rows[i].innerHTML.substring(1, rows[i].innerHTML.length)).toFixed(2));
           }
           document.getElementById("sum").innerHTML = "Total: $" + sum ;
       }
   });
   ```

4. En la ventana de código de `form-functions.js`, localice el siguiente código:

   ```javascript
   $(function() {
   ```

5. Asegúrese de que el cursor esté después del signo `{` (llave de apertura), presione Entrar y escriba el siguiente código:

   ```javascript
   $('.pricing select').change(function(event) {
       var target = $(event.target);
       var value = parseInt(target.val());
       var container = target.parent();
       var price = container.prev();
       var label = price.prev();

       $("#" + label.text()).remove();
   });
   ```

### Tarea 3: Usar jQuery para modificar elementos

1. En la ventana de código de `form-functions.js`, localice el siguiente código:

   ```javascript
   $("#" + label.text()).remove();
   ```

2. Coloque el cursor al final del código localizado, presione Entrar dos veces y escriba el siguiente código:

   ```javascript
   if (value) {
       $("#summary").addClass("display-div").removeClass("hidden-div");
       var correctCost = (price.text().substring(1, price.text().length));
       var calc = parseInt(value * correctCost);
       var msg = label.text() + " ticket - " + value.toString() + "x" + price.text() + " = <span class='sum'>" +'$' + calc + '</span>';
       var row = $("<tr id='" + label.text() +"'>");
       row.append($("<td>").html(msg));
       $("#totalAmount").append(row);
   }
   if ($("#totalAmount tr").length === 0) {
       $("#summary").addClass("hidden-div").removeClass("display-div");
       $("#formButtons input").attr('disabled', 'disabled');
       $("#comment").show();
   }
   else {
       $("#formButtons input").removeAttr('disabled');
       $("#comment").hide();
   }
   calculateSum();
   ```

3. En la ventana `ZooSite -- Microsoft Visual Studio`, en `Solution Explorer`, bajo `wwwroot`, haga clic con el botón derecho en `js`, señale `Add` y haga clic en `New Item…`.
4. En el cuadro de diálogo `Add New Item -- ZooSite`, seleccione `Web` en el panel izquierdo y `JavaScript File` en el panel de la lista de plantillas.
5. En el cuadro de diálogo `Add New Item -- ZooSite`, en el cuadro de texto `Name:`, escriba `menubar-functions` y haga clic en `Add`.
6. En la ventana de código de `menubar-functions.js`, escriba el siguiente código:

   ```javascript
   $(function() {
       var path = window.location.pathname;

       $(".nav li a").each(function(index, value) {
           var href = $(value).attr('href');
           if (path === href) {
               $(this).closest('li').addClass('active');
           }
       });
   });
   ```

7. En la ventana `ZooSite -- Microsoft Visual Studio`, en `Solution Explorer`, bajo `wwwroot`, haga clic con el botón derecho en `js`, señale `Add` y haga clic en `New Item…`.
8. En el cuadro de diálogo `Add New Item -- ZooSite`, seleccione `Web` en el panel izquierdo y `JavaScript File` en el panel de la lista de plantillas.
9. En el cuadro de diálogo `Add New Item -- ZooSite`, en el cuadro de texto `Name:`, escriba `slider-functions` y haga clic en `Add`.
10. En la ventana de código de `slider-functions.js`, escriba el siguiente código:

    ```javascript
    var images = ['/images/header.jpg', '/images/waters.jpg'];
    var current = 0;

    function nextImage() {
        current++;
        if (current === images.length) {
            current = 0;
        }
        $('.header-container').css('background-image', 'url(' + images[current] + ')');
    }

    function prevImage() {
        current--;
        if (current < 0) {
            current = images.length-1;
        }
        $('.header-container').css('background-image', 'url(' + images[current] + ')');
    }
    ```

11. En la ventana `ZooSite -- Microsoft Visual Studio`, en `Solution Explorer`, bajo `Views` y `Shared`, haga clic en `_Layout.cshtml`.
12. En la ventana de código de `_Layout.cshtml`, localice el siguiente código:

    ```html
    <script src="~/lib/jquery/jquery.min.js"></script>
    ```

13. Coloque el cursor después del signo `>` de la etiqueta `</script>`, presione Entrar y escriba el siguiente código:

    ```html
    <script src="~/js/menubar-functions.js"></script>
    <script src="~/js/slider-functions.js"></script>
    ```

### Tarea 4: Validación del lado del cliente mediante jQuery

1. En la ventana `ZooSite - Microsoft Visual Studio`, en `Solution Explorer`, haga clic con el botón derecho en `ZooSite`, señale `Add` y haga clic en `Client-Side Library…`.
2. En el cuadro de diálogo `Add Client-Side Library`, en el cuadro de búsqueda, escriba `jquery-valid` y presione Entrar.
3. En el cuadro de diálogo `Add Client-Side Library`, elija `jquery-validate` en los resultados de búsqueda.
4. En el cuadro de diálogo `Add Client-Side Library`, compruebe que `Target Location` esté establecido en `wwwroot/lib/jquery-validate/` y haga clic en `Install`.
5. Por segunda vez, en la ventana `ZooSite - Microsoft Visual Studio`, en `Solution Explorer`, haga clic con el botón derecho en `ZooSite`, señale `Add` y haga clic en `Client-Side Library…`.
6. En el cuadro de diálogo `Add Client-Side Library`, en el cuadro de búsqueda, escriba `jquery-valid` y presione Entrar.
7. En el cuadro de diálogo `Add Client-Side Library`, elija `jquery-validation-unobtrusive` en los resultados de búsqueda.
8. En el cuadro de diálogo `Add Client-Side Library`, compruebe que `Target Location` esté establecido en `wwwroot/lib/jquery-validation-unobtrusive/` y haga clic en `Install`.
9. En la ventana `ZooSite - Microsoft Visual Studio`, en `Solution Explorer`, bajo `Views` y `Zoo`, haga clic en `BuyTickets.cshtml`.
10. En la ventana de código de `BuyTickets.cshtml`, localice el siguiente código:

    ```html
    @section Scripts {
        <script src="~/js/form-functions.js"></script>
    }
    ```

11. Coloque el cursor después del signo `{` (llave de apertura), presione Entrar y escriba el siguiente código:

    ```html
    <script src="~/lib/jquery-validate/jquery.validate.min.js"></script>
    <script src="~/lib/jquery-validation-unobtrusive/jquery.validate.unobtrusive.min.js"></script>
    ```

### Tarea 5: Ejecutar la aplicación

1. En la ventana `ZooSite -- Microsoft Visual Studio`, en el menú `File`, haga clic en `Save All`.
2. En la ventana `ZooSite - Microsoft Visual Studio`, en el menú `Debug`, haga clic en `Start Without Debugging`.
3. En la página `Zoo Attractions`, en el encabezado `Welcome to Zoo Center`, haga clic en la flecha derecha y después en la izquierda.

   **Nota:** El navegador muestra el encabezado con la presentación, y el archivo JavaScript `slider-functions.js` aplica la funcionalidad de la presentación de imágenes principal.

4. En la barra de menús, haga clic en `Tickets`.

   **Nota:** El botón `Buy` está deshabilitado y aparece el mensaje `Please Choose Tickets` cerca del botón.

5. En `Step 1 - Choose Tickets`, elija uno o más boletos en los cuadros `Adult`, `Child` y `Senior`.

   **Nota:** Ahora el botón `Buy` está habilitado y desapareció el mensaje `Please Choose Tickets`. Además, se agregó una sección `Order Summary` que muestra el precio total de los boletos.

6. En `Step 2 - Buy Tickets`, en el cuadro `First Name`, escriba su nombre.
7. En `Step 2 - Buy Tickets`, en el cuadro `Last Name`, escriba su apellido.
8. En `Step 2 - Buy Tickets`, en el cuadro `Address`, escriba algún texto.
9. En `Step 2 - Buy Tickets`, en el cuadro `Email`, escriba `abcd` y presione Tab.

   **Nota:** La validación del lado del cliente detecta que la dirección de correo electrónico no es válida.

10. En `Step 2 - Buy Tickets`, en el cuadro `Email`, escriba `bill@foo.com` y presione Tab.

    **Nota:** El mensaje de error en el navegador desaparece.

11. En `Step 2 - Buy Tickets`, en el cuadro `Phone`, escriba algunos dígitos y haga clic en `Buy`.

    **Nota:** Debe ver la página `ThankYou.cshtml`. Si agregó letras en lugar de números, la validación fallará.

12. En Microsoft Edge, haga clic en `Close`.
13. En la ventana `ZooSite - Microsoft Visual Studio`, en el menú `File`, haga clic en `Exit`.

**Resultados:** Después de completar este ejercicio, habrá agregado jQuery a una aplicación web, modificado elementos HTML mediante jQuery, realizado validación del lado del cliente y controlado eventos JavaScript.
