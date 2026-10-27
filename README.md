# Laboratorio -- Implementación del CRUD de Productos en Laravel

> **Universidad Tecnológica de Panamá**\
> Facultad de Ingeniería de Sistemas Computacionales\
> Campus Víctor Levi Sasso\
> **Curso:** Desarrollo Web **Unidad:** I. Modelo Vista Controlador
> (MVC)

------------------------------------------------------------------------

## 1. Introducción

En este laboratorio se configuró un proyecto web utilizando Laravel y el
patrón Modelo--Vista--Controlador (MVC). El objetivo fue preparar el
entorno de desarrollo, conectar Laravel con una base de datos MySQL,
ejecutar las migraciones y generar las funciones de registro e inicio de
sesión mediante Laravel UI y Bootstrap.

Durante la práctica se trabajó con PHP, Composer, Laravel, WampServer,
MySQL y herramientas de frontend. También se atendieron problemas de
configuración y compatibilidad que aparecieron durante la instalación y
ejecución.

A partir de esa configuración se desarrolló un CRUD de productos, con
operaciones para listar, consultar, crear, editar y eliminar registros.
Se configuró el acceso a estas funciones para usuarios autenticados y se
personalizó la interfaz con una paleta de colores azul petróleo y
turquesa.

## 2. Objetivos

-   Comprender la organización de un proyecto Laravel bajo el patrón
    MVC.
-   Configurar el entorno local y la conexión a MySQL.
-   Crear y utilizar una tabla de productos para almacenar información.
-   Implementar las operaciones CRUD (crear, leer, actualizar y
    eliminar).
-   Generar y comprobar las rutas y vistas de registro e inicio de
    sesión.
-   Proteger las rutas de productos para que requieran autenticación.
-   Personalizar las vistas del CRUD y documentar las dificultades
    encontradas y las soluciones aplicadas.

## 3. Requisitos previos y tecnologías

| Tecnología | Imagen | Versión / uso |
|---|---|---|
| Sistema operativo | <img width="72" height="72" alt="Windows 11" src="https://github.com/user-attachments/assets/afa15f10-4a84-4051-ba2e-10db79b467e4" /> | Windows 11 |
| PHP | <img width="72" height="72" alt="PHP" src="https://github.com/user-attachments/assets/de4bf4b1-e945-416b-9c4b-ed839fae3949" /> | 8.3.28 (CLI) |
| Composer | <img width="72" height="72" alt="Composer" src="https://github.com/user-attachments/assets/1c66f7fb-1414-4386-a282-aeef404ca7d1" /> | 2.10.3 |
| Laravel | <img width="72" height="72" alt="Laravel" src="https://github.com/user-attachments/assets/9b3ef0aa-141a-4ee0-982d-2105d4cb9e7a" /> | Framework 13.38.0 |
| Laravel Installer | <img width="72" height="72" alt="Laravel Installer" src="https://github.com/user-attachments/assets/71e84d73-ab0b-45e5-bdc2-848b4488c6cd" /> | 5.32.0 |
| WampServer | <img width="72" height="72" alt="WampServer" src="https://github.com/user-attachments/assets/1a9f05eb-0552-46e7-a28e-39391986111d" /> | Entorno local; Apache y MySQL |
| MySQL | <img width="72" height="72" alt="MySQL" src="https://github.com/user-attachments/assets/b395cdea-040d-4bc6-8970-1bbf0d243993" /> | Base de datos local `crud` |
| Laravel UI | <img width="72" height="72" alt="Laravel UI" src="https://github.com/user-attachments/assets/ee9c975d-8709-4565-8146-ed266ef04938" /> | 4.6.3 |
| Bootstrap | <img width="72" height="72" alt="Bootstrap" src="https://github.com/user-attachments/assets/9f486304-11c6-4c27-a280-164e2831e420" /> | Scaffolding de interfaz generado con Laravel UI |
| Node.js / npm | <img width="72" height="72" alt="Node.js / npm" src="https://github.com/user-attachments/assets/71d380d5-5155-411e-a9b7-b17f5cfdc9e0" /> | Instalación y compilación de recursos frontend |
| Editor | <img width="72" height="72" alt="Editor" src="https://github.com/user-attachments/assets/c594ebd9-9e47-4cc4-bc6c-a8850db3b6d4" /> | VS Code, Notepad |

## 4. Instalación y configuración del proyecto

### 4.1 Ubicación del proyecto

El proyecto se trabajó en el directorio de WampServer:

``` bat
cd C:\wamp64\www\Crud
```

El nombre de la carpeta del proyecto es `Crud`. Para ejecutar los
comandos de Artisan, primero se debe estar dentro de esa carpeta.

### 4.2 Verificación de herramientas

Se utilizaron PHP, Composer y Laravel en el entorno local. Las versiones
se pueden comprobar con:

``` bat
php -v
composer -V
php artisan --version
```

Para iniciar el servidor de desarrollo de Laravel:

``` bat
php artisan serve
```

Luego se abre en el navegador la dirección local que indique la
terminal, normalmente `http://127.0.0.1:8000`.

## 5. Base de datos y estructura de productos

### 5.1 Configuración de MySQL

Se utilizó la base de datos `crud` en MySQL mediante
WampServer/phpMyAdmin. La conexión se configura en el archivo `.env` del
proyecto. El ejemplo siguiente corresponde a una instalación local
típica; la contraseña debe coincidir con la configuración de WAMP:

``` dotenv
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=crud
DB_USERNAME=root
DB_PASSWORD=
```

Después de cambiar los valores de conexión, se puede limpiar la
configuración almacenada en caché:

``` powershell
php artisan config:clear
```

### 5.2 Tabla de productos

La tabla de productos almacena los siguientes campos:

  -----------------------------------------------------------------------
  Campo                               Descripción
  ----------------------------------- -----------------------------------
  `id`                                Identificador del producto

  `description`                       Descripción del producto

  `price`                             Precio del producto

  `stock`                             Cantidad disponible en inventario

  `created_at`                        Fecha y hora de creación,
                                      gestionada por Laravel

  `updated_at`                        Fecha y hora de última
                                      actualización, gestionada por
                                      Laravel
  -----------------------------------------------------------------------

La tabla debe existir en la base de datos `crud` y corresponder con los
campos que utiliza el modelo `Product`. No se debe ejecutar una
migración destructiva sobre una base de datos con información que se
quiera conservar.

## 6. Implementación del patrón MVC

### 6.1 Modelo `Product`

El modelo se encuentra en `app/Models/Product.php` y representa los
productos almacenados en la base de datos. Se configuraron como
asignables los campos utilizados por el formulario:

``` php
protected $fillable = [
    'description',
    'price',
    'stock',
];
```

La propiedad `$fillable` permite que Laravel asigne estos campos de
forma masiva cuando se crea o actualiza un producto mediante los datos
validados del formulario.

### 6.2 Controlador `ProductController`

El controlador se encuentra en
`app/Http/Controllers/ProductController.php` y centraliza las
operaciones del CRUD. Se trabaja con las acciones habituales de un
controlador de recursos:

  Método        Operación
  ------------- ----------------------------------------------
  `index()`     Mostrar el listado de productos
  `create()`    Mostrar el formulario para crear un producto
  `store()`     Validar y guardar un producto nuevo
  `show()`      Mostrar los detalles de un producto
  `edit()`      Mostrar el formulario de edición
  `update()`    Actualizar un producto existente
  `destroy()`   Eliminar un producto

El controlador debe enviar el producto correspondiente a las vistas de
detalle y edición, y redirigir al listado después de las operaciones de
guardado o eliminación.

### 6.3 Rutas

En `routes/web.php` se registraron las rutas de autenticación de Laravel
UI y se protegieron con el middleware `auth` la página de inicio y las
rutas del CRUD:

``` php
use App\Http\Controllers\ProductController;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\Route;

Auth::routes();

Route::get('/home', [App\Http\Controllers\HomeController::class, 'index'])
    ->name('home')
    ->middleware('auth');

Route::resource('products', ProductController::class)
    ->middleware('auth');
```

`Route::resource` registra las siete rutas de un CRUD de recursos. El
middleware `auth` exige que el usuario inicie sesión antes de acceder a
las rutas de productos.

Para verificar las rutas registradas:

``` powershell
php artisan route:list --path=products -v
```

Las rutas de recursos corresponden a estas operaciones:

  Método HTTP   Ruta                         Acción
  ------------- ---------------------------- --------------------------------
  GET           `/products`                  Listar productos
  GET           `/products/create`           Mostrar formulario de creación
  POST          `/products`                  Guardar producto nuevo
  GET           `/products/{product}`        Mostrar detalle
  GET           `/products/{product}/edit`   Mostrar formulario de edición
  PUT/PATCH     `/products/{product}`        Actualizar producto
  DELETE        `/products/{product}`        Eliminar producto

## 7. Vistas y diseño de la interfaz

Las vistas del CRUD se encuentran en `resources/views/products/` y se
implementan con Blade.

  -----------------------------------------------------------------------
  Archivo                             Función
  ----------------------------------- -----------------------------------
  `index.blade.php`                   Lista los productos y muestra las
                                      acciones disponibles

  `create.blade.php`                  Formulario para crear productos

  `show.blade.php`                    Detalle de un producto

  `edit.blade.php`                    Formulario para modificar un
                                      producto
  -----------------------------------------------------------------------

### 7.1 Paleta de colores

Se personalizó la interfaz utilizando una combinación de azul petróleo,
turquesa y fondos claros:

  Elemento                                Color
  --------------------------------------- -----------
  Color principal / barra de navegación   `#0f4c5c`
  Color principal al pasar el cursor      `#093642`
  Color secundario / botones de edición   `#2a9d8f`
  Fondo de página                         `#edf4f5`
  Fondo de encabezados                    `#dcebed`
  Bordes                                  `#c8dadd`
  Texto                                   `#20343a`

La tabla utiliza un encabezado azul petróleo y filas con tonos claros
para facilitar la lectura. Los botones distinguen las acciones: mostrar,
editar y eliminar. El botón de eliminación utiliza un tono coral para
advertir que se trata de una acción destructiva.

### 7.2 Vistas de detalle y edición

Durante la revisión se identificó que los archivos `show.blade.php` y
`edit.blade.php` estaban vacíos, por lo que las acciones **Mostrar** y
**Editar** no podían presentar sus páginas. Se preparó contenido Blade
para ambas vistas:

-   `show.blade.php` presenta el identificador, descripción, precio y
    stock del producto, además de enlaces para volver al listado o
    editar.
-   `edit.blade.php` presenta un formulario con los valores actuales,
    protección CSRF y el método `PUT` necesario para actualizar el
    recurso.

Para que los enlaces y formularios funcionen, los nombres de rutas deben
coincidir con las rutas de recursos y el controlador debe pasar la
variable `$product` a las vistas. Se debe guardar el contenido en los
archivos correspondientes y probar las acciones en el navegador.

## 8. Autenticación y control de acceso

Se instaló Laravel UI y se generó el scaffolding de Bootstrap con
autenticación mediante:

``` powershell
composer require laravel/ui
php artisan ui bootstrap --auth
```

Laravel UI genera las vistas y rutas para registro, inicio de sesión y
cierre de sesión. Las rutas del CRUD se dejaron detrás del middleware
`auth`, por lo que un usuario no autenticado debe iniciar sesión antes
de administrar productos.

### 8.1 Compatibilidad con Laravel 13

Durante el trabajo se encontró incompatibilidad con el uso antiguo de
`$this->middleware()` dentro de los constructores de algunos
controladores generados por Laravel UI. Se retiraron esos constructores
antiguos y se aplicó el control de acceso explícitamente en las rutas de
`web.php`. La configuración de las rutas se comprobó con
`php artisan route:list --path=products -v`, donde las rutas de
productos aparecieron con el middleware `auth`.

## 9. Pruebas del CRUD

Las siguientes comprobaciones permiten verificar el funcionamiento de la
aplicación:

1.  Iniciar sesión con un usuario registrado.
2.  Abrir `/products` y comprobar que se muestre el listado de
    productos.
3.  Utilizar la opción de crear para registrar un producto con
    descripción, precio y stock.
4.  Abrir la opción de mostrar y comprobar los datos del producto.
5.  Abrir la opción de editar, modificar uno o más campos y guardar los
    cambios.
6.  Comprobar que los cambios se reflejen en el listado.
7.  Probar la eliminación de un producto de prueba y verificar que
    desaparezca del listado.
8.  Cerrar sesión e intentar acceder a `/products`; la aplicación debe
    exigir autenticación.

> Registra aquí los resultados y agrega capturas de pantalla reales de
> tu aplicación una vez que hayas comprobado cada operación. No se
> incluyen capturas nuevas porque no fueron proporcionadas en la
> información disponible.

## 10. Dificultades encontradas y soluciones

### Dificultad 1: PowerShell bloqueó la ejecución de npm

**Problema:** al ejecutar `npm install`, PowerShell podía bloquear
`npm.ps1` por la política de ejecución de scripts.

**Solución utilizada en el laboratorio de referencia:** ejecutar npm
mediante el archivo CMD:

``` powershell
npm.cmd install
npm.cmd run dev
```

Estos comandos se incluyen como referencia del entorno y solo son
necesarios si se requiere instalar o compilar los recursos frontend.

### Dificultad 2: Configuración de conexión a MySQL

**Problema:** Laravel puede intentar conectarse a una base de datos
distinta si los valores del archivo `.env` no coinciden con la base de
datos creada.

**Solución:** revisar las variables `DB_` del archivo `.env`, establecer
`DB_DATABASE=crud` y ejecutar:

``` powershell
php artisan config:clear
```

### Dificultad 3: Incompatibilidad del middleware en controladores

**Problema:** algunos controladores generados con el scaffolding antiguo
intentaban usar `$this->middleware()` en el constructor y se producía un
error de método no definido en el entorno Laravel 13.

**Solución aplicada:** retirar el uso incompatible en los controladores
afectados y definir el middleware de autenticación directamente en las
rutas correspondientes de `routes/web.php`.

### Dificultad 4: Las acciones Mostrar y Editar no abrían correctamente

**Problema:** al revisar `resources/views/products/show.blade.php` y
`resources/views/products/edit.blade.php`, los archivos estaban vacíos.

**Solución:** preparar las vistas Blade de detalle y edición, incluyendo
la variable `$product`, los enlaces con nombres de rutas correctos y el
formulario de actualización con `@csrf` y `@method('PUT')`. Después de
guardar los archivos, se deben probar nuevamente ambas acciones.

## 11. Respaldo de la base de datos

Se recomienda guardar un respaldo SQL de la base de datos `crud` en el
repositorio. Desde phpMyAdmin:

1.  Seleccionar la base de datos `crud`.
2.  Abrir la pestaña **Exportar**.
3.  Seleccionar el formato SQL.
4.  Descargar el archivo y guardarlo, por ejemplo, como
    `database/backup/crud.sql`.

El archivo de respaldo debe generarse desde la base de datos real del
proyecto antes de entregarlo. No se debe sustituir por un archivo SQL
inventado.

## 12. Referencias

Verifica y completa las fuentes que realmente consultaste durante el
desarrollo. Estas referencias oficiales sirven como puntos de partida:

1.  Laravel. (s. f.). *Laravel documentation*. https://laravel.com/docs
2.  Laravel. (s. f.). *Laravel UI*. GitHub.
    https://github.com/laravel/ui
3.  Composer. (s. f.). *Composer documentation*.
    https://getcomposer.org/doc/
4.  WampServer. (s. f.). *WampServer*. https://www.wampserver.com/

## 13. Fecha de entrega

-   **Fecha:** completar según la fecha de entrega asignada.

## 14. Autor

Completa los datos del estudiante y del instructor correspondientes a
esta entrega:

-   **Nombre:** completar
-   **Correo:** completar
-   **Curso:** Desarrollo Web
-   **Instructor del laboratorio:** completar
