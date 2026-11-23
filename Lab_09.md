# Lab 09: Desarrollo del lado del cliente

## Escenario

Se te ha pedido crear una aplicación web de helados para los clientes de tu organización. Debe incluir una página que muestre todos los tipos de helado disponibles y otra que permita comprarlos. Para dar estilo a la aplicación, usarás Bootstrap y Sass. Usarás gulp para compilar, minimizar y agrupar archivos.

## Ejercicio 1: Usar gulp para ejecutar tareas

En este ejercicio instalarás gulp con npm y crearás `gulpfile.js`. Después escribirás tareas para agrupar y minimizar archivos JavaScript y una tarea de vigilancia para detectar cambios en los archivos de la carpeta `Scripts`.

Tareas: instalar gulp con npm; copiar un archivo JavaScript; ejecutar la tarea; minimizar un archivo JavaScript; agrupar y minimizar los archivos JavaScript de una carpeta; agregar una tarea de vigilancia; ejecutar las tareas.

### Tarea 1: Usar npm para instalar gulp

1. En el Explorador de archivos, ve a `D:\Allfiles\Mod09\Labfiles\01_IceCreamCompany_begin` y copia la dirección de la barra de direcciones.
2. Selecciona el botón **Start** y escribe `cmd`.
3. En **Best match**, haz clic derecho en **Command Prompt** y selecciona **Run as administrator**.
4. En **User Account Control**, haz clic en **Yes**.
5. En la ventana **Administrator: Command Prompt**, escribe el siguiente comando y presiona **Enter**:

   ```text
   cd {copied folder path}
   ```

   **Nota:** Si la ruta copiada está en otra unidad, escribe primero `{disk drive}:` para cambiar de unidad y luego ejecuta `cd {copied folder path}`.
6. En la misma ventana, escribe el siguiente comando y presiona **Enter**:

   ```text
   npm install
   ```

   **Nota:** Puedes ignorar los mensajes de advertencia que aparezcan.
7. Cierra la ventana.
8. En el Explorador de archivos, ve a `D:\Allfiles\Mod09\Labfiles\01_IceCreamCompany_begin` y abre `IceCreamCompany.sln` con doble clic. **Nota:** Si aparece **Security Warning for IceCreamCompany**, desmarca **Ask me for every project in this solution** y haz clic en **OK**.
9. En Visual Studio, abre **Tools > Options**.
10. En **Search Options**, escribe `Web Package Management` y presiona **Enter**.
11. En **Locations of external tools**, selecciona `$(PATH)`, pulsa la flecha **Up** hasta que esté al principio de la lista y haz clic en **OK**.
12. En **Solution Explorer**, abre `package.json`.
13. Examina el contenido de `package.json`. **Nota:** `gulp`, `gulp-concat`, `gulp-cssmin`, `gulp-sass` y `gulp-uglify` aparecen en `devDependencies`; `bootstrap`, `hoek`, `jquery`, `lodash` y `popper.js` aparecen en `Dependencies`.

### Tarea 2: Escribir una tarea para copiar un archivo JavaScript

1. En **Solution Explorer**, haz clic derecho en **IceCreamCompany** y selecciona **Add > New Item…**.
2. En **Add New Item - IceCreamCompany**, haz clic en **Web** y luego en **JavaScript File**.
3. En **Name**, escribe `gulpfile` y haz clic en **Add**.
4. En `gulpfile.js`, escribe:

   ```javascript
   var gulp = require('gulp');

   var paths = {
       webroot: "./wwwroot/",
       nodeModules: "./node_modules/"
   };

   paths.jqueryjs = paths.nodeModules + "jquery/dist/jquery.js";
   paths.destinationjsFolder = paths.webroot + "scripts/";

   gulp.task("copy-js-file", function() {
       return gulp.src(paths.jqueryjs)
           .pipe(gulp.dest(paths.destinationjsFolder));
   });
   ```

### Tarea 3: Ejecutar la tarea

1. En Visual Studio, selecciona **File > Save All**.
2. En **Solution Explorer**, haz clic derecho en `gulpfile.js` y selecciona **Task Runner Explorer**. **Nota:** Si **Tasks** no muestra `copy-js-file`, haz clic en **Refresh**.
3. En **Task Runner Explorer > Tasks**, haz clic derecho en **copy-js-file** y selecciona **Run**. **Nota:** En **Solution Explorer > wwwroot** aparecerá la carpeta `scripts` con el archivo `jquery.js`.

### Tarea 4: Escribir una tarea para minimizar un archivo JavaScript

1. En `gulpfile.js`, localiza:

   ```javascript
   var gulp = require('gulp');
   ```
2. Al final de esa línea, presiona **Enter** y agrega:

   ```javascript
   var concat = require('gulp-concat');
   var uglify = require('gulp-uglify');
   ```
3. Localiza:

   ```javascript
   paths.destinationjsFolder = paths.webroot + "scripts/";
   ```
4. Al final de esa línea, presiona **Enter** y agrega:

   ```javascript
   paths.vendorjsFileName = "vendor.min.js";
   ```
5. Localiza la tarea:

   ```javascript
   gulp.task("copy-js-file", function() {
       return gulp.src(paths.jqueryjs)
           .pipe(gulp.dest(paths.destinationjsFolder));
   });
   ```
6. Después de la tarea, presiona **Enter** dos veces y agrega:

   ```javascript
   gulp.task("min-vendor:js", function() {
       return gulp.src(paths.jqueryjs)
           .pipe(concat(paths.vendorjsFileName))
           .pipe(uglify())
           .pipe(gulp.dest(paths.destinationjsFolder));
   });
   ```

### Tarea 5: Agrupar y minimizar todos los archivos JavaScript de una carpeta

1. En `gulpfile.js`, localiza:

   ```javascript
   paths.vendorjsFileName = "vendor.min.js";
   ```
2. Al final de esa línea, presiona **Enter** y agrega:

   ```javascript
   paths.jsFiles = "./Scripts/*.js";
   paths.jsFileName = "script.min.js";
   ```
3. Al final del archivo, agrega:

   ```javascript
   gulp.task("min:js", function() {
       return gulp.src(paths.jsFiles)
           .pipe(concat(paths.jsFileName))
           .pipe(uglify())
           .pipe(gulp.dest(paths.destinationjsFolder));
   });
   ```

### Tarea 6: Agregar una tarea de vigilancia

1. Al final de `gulpfile.js`, agrega:

   ```javascript
   gulp.task("js-watcher", function() {
       gulp.watch('./Scripts/*.js', gulp.series("min:js"));
   });
   ```

### Tarea 7: Ejecutar las tareas

1. En Visual Studio, selecciona **File > Save All**.
2. En **Solution Explorer**, haz clic derecho en `gulpfile.js` y selecciona **Task Runner Explorer**. **Nota:** Si **Tasks** no muestra `min:js`, `min-vendor:js` y `js-watcher`, haz clic en **Refresh**.
3. En **Task Runner Explorer > Tasks**, haz clic derecho en **min-vendor:js** y selecciona **Run**. **Nota:** En `wwwroot/scripts` aparecerá `vendor.min.js`, una versión minimizada de `jquery.js`.
4. En **Solution Explorer > Scripts**, abre `payment-calc.js`. **Nota:** La cuarta línea contiene el error intencional `form-control-mistake`.
5. En **Task Runner Explorer > Tasks**, haz clic derecho en **min:js** y selecciona **Run**. **Nota:** En `wwwroot/scripts` aparecerá `script.min.js`.
6. En **Solution Explorer > wwwroot > scripts**, abre `script.min.js`. **Nota:** Es una versión minimizada de `payment-calc.js` y contiene `form-control-mistake`.
7. En **Task Runner Explorer > Tasks**, haz clic derecho en **js-watcher** y selecciona **Run**.
8. En **Solution Explorer > Scripts**, abre `payment-calc.js`.
9. En `payment-calc.js`, selecciona:

   ```javascript
   $('.form-control-mistake')
   ```
10. Reemplaza la selección por:

    ```javascript
    $('.form-control')
    ```
11. Guarda `payment-calc.js`. **Nota:** La tarea `min:js` puede ejecutarse automáticamente en la ventana **js-watcher (running)** debido a la relación entre `payment-calc.js` y `script.min.js`.
12. En **Solution Explorer > wwwroot > scripts**, abre `script.min.js` y verifica que `form-control-mistake` se haya reemplazado por `form-control`.

**Resultado:** Usaste gulp para copiar, agrupar y minimizar archivos JavaScript y agregaste tareas de vigilancia para mantenerlos actualizados.

## Ejercicio 2: Dar estilo con Sass

Crearás `main.scss` y una tarea gulp para compilar Sass a CSS. También crearás una tarea de vigilancia que compile el archivo automáticamente cuando cambie.

Tareas: agregar un archivo Sass al proyecto; agregar tareas gulp para los archivos Sass; ejecutar las tareas.

### Tarea 1: Agregar un archivo Sass al proyecto

1. En **Solution Explorer**, haz clic derecho en **IceCreamCompany** y selecciona **Add > New Folder**.
2. En **NewFolder**, escribe `Styles` y presiona **Enter**.
3. Haz clic derecho en **Styles** y selecciona **Add > New Item…**.
4. En el cuadro de búsqueda de **Add New Item - IceCreamCompany**, escribe `scss` y presiona **Enter**.
5. Selecciona **SCSS Style Sheet (SASS)**.
6. En **Name**, escribe `main` y haz clic en **Add**.
7. En `main.scss`, selecciona:

   ```css
   body {
   }
   ```
8. Reemplaza la selección por:

   ```scss
   $highlights: #124eab;

   @mixin normalized-text() {
       font-family: "Playfair Display", Arial, Tahoma, sans-serif;
       text-align: center;
   }

   @mixin normalized-image() {
       width: 100%;
       height: auto;
   }
   ```
9. Al final de `main.scss`, agrega:

   ```scss
   div {
       h1 {
           @include normalized-text();
           font-size: 45px;
           line-height: 50px;
           font-weight: 400;
           letter-spacing: 1px;
           color: #736454;
           margin: 60px;
       }
   }

   .main-title {
       background-image: url("/images/banner-1.jpg");
       width: 100%;
       background-size: cover;
       background-position: center center;
       min-height: 400px;
       display: flex;
       flex-direction: column;
       justify-content: center;
       align-items: center;

       h1 {
           @include normalized-text();
           color: $highlights;
           font-size: 50px;
           text-shadow: 0px 2px 5px #aba8a8;
           font-weight: bolder;
           text-align: center;
       }
       button {
           @include normalized-text();
           transition: none;
           color: lighten(#ffffff,90%);
           text-align: inherit;
           line-height: 13px;
           border: 1px solid #d3c0c0;
           margin: 0px;
           padding: 12px 24px;
           letter-spacing: 0px;
           font-weight: 400;
           font-size: 16px;
           font-weight: bold;
           background-color: #736454;
       }
   }
   ```
10. Después de la última llave de cierre, presiona **Enter** y agrega:

    ```scss
    .img-container {
        display: flex;
        flex-wrap: wrap;
        justify-content: space-around;
        align-items: flex-end;

        .item {
            color: white;
            width: 200px;
            display: flex;
            flex-direction: column;
            justify-content: space-between;

            h3 {
                @include normalized-text();
                color: #736454;
                font-size: 20px;
            }

            div {
                img {
                    @include normalized-image();
                }
            }

            div {
                p {
                    @include normalized-text();
                    color: #736454;
                    font-size: 20px;
                    margin: 70px;
                }
            }
        }
    }

    .container {
        .checkout {
            border: 1px solid #ccc;
            box-shadow: 0 0 5px #ccc;
            padding: 20px;
            width: 800px;
            margin: 0 auto;
            border-radius: 4px;
            background-color: #f9f9f9;

            .row justify-content-center intro-row {
                font-weight: bold;
            }
        }
    }
    ```
11. Después de la última llave de cierre, presiona **Enter** y agrega:

    ```scss
    .justify-content-center {
        justify-content: center !important;
        align-items: center;
    }

    nav {
        width: 450px;
    }

    img {
        height: 35px;
        width: 35px;
    }

    .navbar-nav > li {
        float: left;
        position: relative;
    }

    .row {
        margin: 10px;
    }

    .imageDisplay {
        @include normalized-image();
    }
    ```

### Tarea 2: Agregar tareas gulp para los archivos Sass

1. En **Solution Explorer**, abre `gulpfile.js`.
2. Localiza:

   ```javascript
   var gulp = require('gulp');
   var concat = require('gulp-concat');
   var uglify = require('gulp-uglify');
   ```
3. Al final de ese código, presiona **Enter** y agrega:

   ```javascript
   var sass = require('gulp-sass')(require('sass'));
   var cssmin = require('gulp-cssmin');
   ```
4. Localiza:

   ```javascript
   paths.jsFileName = "script.min.js";
   ```
5. Al final de esa línea, presiona **Enter** y agrega:

   ```javascript
   paths.sassFiles = "./Styles/*.scss";
   paths.compiledCssFileName = "main.min.css";
   paths.destinationCssFolder = paths.webroot + "css/";
   ```
6. Localiza:

   ```javascript
   gulp.task("min:js", function() {
       return gulp.src(paths.jsFiles)
           .pipe(concat(paths.jsFileName))
           .pipe(uglify())
           .pipe(gulp.dest(paths.destinationjsFolder));
   });
   ```
7. Después de ese código, presiona **Enter** dos veces y agrega:

   ```javascript
   gulp.task("min:scss", function() {
       return gulp.src(paths.sassFiles)
           .pipe(sass().on('error', sass.logError))
           .pipe(concat(paths.compiledCssFileName))
           .pipe(cssmin())
           .pipe(gulp.dest(paths.destinationCssFolder));
   });
   ```
8. Localiza:

   ```javascript
   gulp.task("js-watcher", function() {
       gulp.watch('./Scripts/*.js', gulp.series("min:js"));
   });
   ```
9. Después de ese código, presiona **Enter** dos veces y agrega:

   ```javascript
   gulp.task("sass-watcher", function() {
       gulp.watch('./Styles/*.scss', gulp.series("min:scss"));
   });
   ```

### Tarea 3: Ejecutar las tareas

1. En Visual Studio, selecciona **File > Save All**.
2. En **Solution Explorer**, haz clic derecho en `gulpfile.js` y selecciona **Task Runner Explorer**. **Nota:** Si **Tasks** no muestra `min:scss` y `sass-watcher`, haz clic en **Refresh**.
3. En **Task Runner Explorer > Tasks**, haz clic derecho en **min:scss** y selecciona **Run**. **Nota:** En `wwwroot/css` aparecerá `main.min.css`.
4. En **Task Runner Explorer > Tasks**, haz clic derecho en **sass-watcher** y selecciona **Run**. **Nota:** A partir de ahora, cada cambio en `main.scss` actualizará `main.min.css` automáticamente.

**Resultado:** Creaste archivos Sass y tareas gulp para compilarlos, agruparlos y minimizarlos.

## Ejercicio 3: Usar Bootstrap

Actualizarás `min-vendor:js` para incluir los archivos JavaScript de Bootstrap y agregarás una tarea para su CSS. Después generarás `vendor.min.css`, actualizarás `vendor.min.js`, aplicarás Bootstrap al diseño y crearás la vista de compra de helados.

Tareas: actualizar `gulpfile.js` para Bootstrap; ejecutar las tareas; aplicar Bootstrap a la aplicación; ejecutar la aplicación.

### Tarea 1: Actualizar gulpfile.js para Bootstrap

1. En **Solution Explorer**, abre `gulpfile.js`.
2. Localiza:

   ```javascript
   paths.jqueryjs = paths.nodeModules + "jquery/dist/jquery.js";
   ```
3. Al final de esa línea, presiona **Enter** y agrega:

   ```javascript
   paths.popperjs = paths.nodeModules + "popper.js/dist/umd/popper.js";
   paths.bootstrapjs = paths.nodeModules + "bootstrap/dist/js/bootstrap.js";
   paths.vendorjs = [paths.jqueryjs, paths.popperjs, paths.bootstrapjs];
   ```
4. Selecciona:

   ```javascript
   gulp.task("min-vendor:js", function() {
       return gulp.src(paths.jqueryjs)
   ```
5. Reemplaza la selección por:

   ```javascript
   gulp.task("min-vendor:js", function() {
       return gulp.src(paths.vendorjs)
   ```
6. Localiza:

   ```javascript
   paths.destinationCssFolder = paths.webroot + "css/";
   ```
7. Al final de esa línea, presiona **Enter** y agrega:

   ```javascript
   paths.bootstrapCss = paths.nodeModules + "bootstrap/dist/css/bootstrap.css";
   paths.vendorCssFileName = "vendor.min.css";
   ```
8. Localiza:

   ```javascript
   gulp.task("min:scss", function() {
       return gulp.src(paths.sassFiles)
           .pipe(sass().on('error', sass.logError))
           .pipe(concat(paths.compiledCssFileName))
           .pipe(cssmin())
           .pipe(gulp.dest(paths.destinationCssFolder));
   });
   ```
9. Después de ese código, presiona **Enter** dos veces y agrega:

   ```javascript
   gulp.task("min-vendor:css", function() {
       return gulp.src(paths.bootstrapCss)
           .pipe(concat(paths.vendorCssFileName))
           .pipe(cssmin())
           .pipe(gulp.dest(paths.destinationCssFolder));
   });
   ```

### Tarea 2: Ejecutar las tareas

1. En Visual Studio, selecciona **File > Save All**.
2. En **Solution Explorer**, haz clic derecho en `gulpfile.js` y selecciona **Task Runner Explorer**. **Nota:** Si **Tasks** no muestra `min-vendor:css`, haz clic en **Refresh**.
3. En **Task Runner Explorer > Tasks**, haz clic derecho en **min-vendor:css** y selecciona **Run**. **Nota:** En `wwwroot/css` aparecerá `vendor.min.css`.
4. En **Task Runner Explorer > Tasks**, haz clic derecho en **min-vendor:js** y selecciona **Run**.
5. En el cuadro de diálogo de Visual Studio, haz clic en **Yes**. **Nota:** Se actualizará `wwwroot/scripts/vendor.min.js`.

### Tarea 3: Dar estilo a la aplicación con Bootstrap

1. En **Solution Explorer > Views > Shared**, abre `_Layout.cshtml`.
2. Localiza:

   ```html
   <title>@ViewBag.Title</title>
   ```
3. Después de `</title>`, presiona **Enter** y agrega:

   ```html
   <script src="~/scripts/vendor.min.js"></script>
   <script src="~/scripts/script.min.js"></script>
   <link href="~/css/vendor.min.css" rel="stylesheet" />
   <link href="~/css/main.min.css" rel="stylesheet" />
   ```
4. En `_Layout.cshtml`, localiza:

   ```html
   <div>
       @RenderBody()
   </div>
   ```
5. Sitúa el cursor antes del signo `<` de `<div>`, presiona **Enter** y la flecha **Up**; luego agrega:

   ```html
   <div>
       <nav class="navbar navbar-expand-lg navbar-light bg-light mx-auto">
           <a class="navbar-brand" href="@Url.Action("Index", "IceCream")">
               <img src="~/images/brand.jpg" class="d-inline-block align-top" alt="">
               Ice Cream of Dreams
           </a>
           <div class="collapse navbar-collapse" id="nav-content">
               <ul class="navbar-nav" id="nav-content">
                   <li class="nav-item active">
                       <a class="nav-link" href="@Url.Action("Index", "IceCream")">Home <span class="sr-only">(current)</span></a>
                   </li>
                   <li class="nav-item">
                       <a class="nav-link" href="@Url.Action("Buy", "IceCream")">Buy Ice Cream</a>
                   </li>
               </ul>
           </div>
       </nav>
   </div>
   <div class="main-title">
       <h1>The Best Ice Cream You Will Taste in Your Life</h1>
       <button type="button" onclick="location.href='@Url.Action("Buy", "IceCream")'">Buy Ice Cream</button>
   </div>
   ```
6. En **Solution Explorer > Controllers**, abre `IceCreamController.cs`.
7. Haz clic derecho en el siguiente código y selecciona **Add View…**:

   ```csharp
   [HttpGet]
   public IActionResult Buy()
   ```
8. En **Add New Scaffolded Item**, comprueba que esté seleccionada la plantilla **Razor View** (no **Empty**) y haz clic en **Add**.
9. En **Add Razor View**, comprueba que **View name** sea `Buy`.
10. Desmarca **Create as a partial view**, marca **Use a layout page** y haz clic en **Add**.
11. Al principio de `Buy.cshtml`, escribe la siguiente línea y presiona **Enter**:

    ```cshtml
    @model IceCreamCompany.Models.Customer
    ```
12. En `Buy.cshtml`, selecciona:

    ```html
    <h1>Buy</h1>
    ```
13. Reemplaza la selección por:

    ```html
    <div class="container">
        <h1>Choose Your Flavor</h1>
        <div class="checkout">
            <div class="row justify-content-center intro-row">
                <div class="col-4">Ice Cream Flavors</div>
                <div class="col-2">Buy Bulk(lbs)</div>
                <div class="col-2">Total Amount</div>
                <div class="col-2"></div>
            </div>
            <div class="row justify-content-center">
                <div class="col-4">
                    <select class="form-control" id="flavor">
                        <option>Select</option>
                        <option>Vanilla Ice Cream with Caramel Ripple and Almonds</option>
                        <option>Vanilla Ice Cream with Cherry Dark Chocolate Ice Cream</option>
                        <option>Vanilla Ice Cream with Pistachio</option>
                    </select>
                </div>
                <div class="col-2">
                    <select class="form-control" id="quantity">
                        <option>1</option>
                        <option>1.5</option>
                        <option>2</option>
                        <option>3</option>
                        <option>4</option>
                    </select>
                </div>
                <div class="col-2">
                    <div id="totalAmount"></div>
                </div>
                <div class="col-2">
                    <div>
                        <img class="imageDisplay" id="iceCreamImage" alt="">
                    </div>
                </div>
            </div>
        </div>
    </div>
    ```
14. Antes del signo `<` del último `</div>`, presiona **Enter** y la flecha **Up**; luego agrega:

    ```html
    <div class="row justify-content-center">
        <div class="col-5">
            <form method="post" enctype="multipart/form-data" asp-action="Buy">
                <div class="form-group row">
                    <label asp-for="FirstName" class="col-sm-4 col-form-label"></label>
                    <div class="col-sm-6">
                        <input asp-for="FirstName" type="text" class="form-control" placeholder="First Name" required>
                    </div>
                </div>
                <div class="form-group row">
                    <label asp-for="LastName" class="col-sm-4 col-form-label"></label>
                    <div class="col-sm-6">
                        <input asp-for="LastName" type="text" class="form-control" placeholder="Last Name" required>
                    </div>
                </div>
                <div class="form-group row">
                    <label asp-for="Address" class="col-sm-4 col-form-label"></label>
                    <div class="col-sm-6">
                        <input asp-for="Address" type="text" class="form-control" placeholder="Address" required>
                    </div>
                </div>
                <div class="form-group row">
                    <label asp-for="Email" class="col-sm-4 col-form-label"></label>
                    <div class="col-sm-6">
                        <input asp-for="Email" type="email" class="form-control" placeholder="email@example.com" required>
                    </div>
                </div>
                <div class="form-group row">
                    <label asp-for="PhoneNumber" class="col-sm-4 col-form-label"></label>
                    <div class="col-sm-6">
                        <input asp-for="PhoneNumber" type="number" class="form-control" placeholder="Phone Number" required>
                    </div>
                </div>
                <div class="form-group row">
                    <div class="col-sm-10">
                        <button id="formButton" type="submit" class="btn btn-outline-primary">
                            Make a Purchase
                        </button>
                    </div>
                </div>
            </form>
        </div>
    </div>
    ```

### Tarea 4: Ejecutar la aplicación

1. En Visual Studio, selecciona **File > Save All**.
2. Selecciona **Debug > Start Without Debugging**.
3. En Microsoft Edge, haz clic en **Buy Ice Cream**.
4. En la página **Buy Ice Cream**, elige una opción en **Ice Cream Flavors**.
5. En **Buy Bulk(lbs)**, selecciona un valor.
6. En **First Name**, escribe tu nombre.
7. En **Last Name**, escribe tu apellido.
8. En **Address**, escribe una dirección.
9. En **Email**, escribe `bill@foo.com`.
10. En **Phone Number**, escribe algunos números y haz clic en **Make a Purchase**.
11. En la página **Thanks for your purchase and hope you enjoy the ice cream!**, haz clic en el enlace **Home** de la parte superior y examina el contenido. **Nota:** Cambia el ancho de la ventana del navegador. Las imágenes de los helados se apilan verticalmente cuando no hay suficiente espacio para colocarlas en horizontal; esto es un ejemplo de diseño adaptable.
12. Cierra Microsoft Edge.
13. En Visual Studio, selecciona **File > Exit**.

**Resultado:** Creaste una aplicación de helados con estilos de Bootstrap en la que los usuarios pueden consultar los detalles de los helados y comprarlos.
