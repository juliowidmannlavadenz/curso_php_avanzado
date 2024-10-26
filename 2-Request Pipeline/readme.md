# Request pipeline
## Concepto

Request Pipeline (o "pipeline de solicitudes") es el conjunto de pasos o procesos que una solicitud HTTP sigue desde que es recibida por el servidor hasta que se genera una respuesta y se envía de vuelta al cliente. 

## Frameworks Web que usan Request Pipeline

1. **Laravel (PHP):** Tiene un ciclo de vida de solicitudes que incluye middlewares globales y específicos de ruta.
2. **ASP.NET Core:** Utiliza un middleware pipeline que configura el flujo de solicitudes en el archivo Startup.cs.
3. **Express.js (Node.js):** Cada solicitud pasa por middlewares para manejar autenticación y validación antes del controlador.
4. **Django (Python):** Procesa solicitudes con middlewares que se registran en settings.py antes y después de llegar a la vista.
5. **Ruby on Rails:** Implementa filtros y middlewares que controlan el flujo de solicitudes antes de llegar al controlador.
6. **Flask (Python):** Permite definir middlewares que gestionan y modifican solicitudes y respuestas.

## Explorando el Request Pipeline con Laravel 11

Para desarrollar el concepto de Request Pipeline, utilizaremos el framework Laravel 11, que ofrece una estructura robusta y flexible para gestionar el flujo de solicitudes HTTP. 

### ¿Que es Laravel?

Laravel es un framework de desarrollo web de código abierto basado en PHP, diseñado para facilitar la creación de aplicaciones web robustas y escalables. Con una arquitectura basada en el patrón MVC (Modelo-Vista-Controlador), Laravel ofrece herramientas como el sistema de enrutamiento, Eloquent ORM para interactuar con bases de datos, y una amplia colección de paquetes que simplifican tareas comunes como la autenticación, validación, y manejo de formularios. Su sintaxis elegante y enfoque en la productividad lo hacen muy popular entre desarrolladores que buscan eficiencia y claridad en el desarrollo backend.

### Conceptos clave de Laravel:

* **Middlewares:** Son filtros que interceptan solicitudes HTTP antes o después de que lleguen al controlador, permitiendo tareas como autenticación o manipulación de respuestas.
* **Enrutamiento (Routing):** Es el sistema que asocia URLs específicas con controladores, gestionando a qué parte de la aplicación se dirige cada solicitud.
* **Controladores (Controllers):** Son clases que manejan la lógica de las solicitudes, procesando datos y retornando respuestas adecuadas (como vistas o JSON).

## Componentes principales del request pipeline

### 1. Recepción de la Solicitud: 
El servidor recibe una solicitud HTTP desde el cliente (navegador, aplicación móvil, etc.).

```Laravel 11```

```php
public function handle(Request $request, Closure $next)
{
    \Log::info("Solicitud recibida: " . $request->path()); 
    return $next($request);
}
```

* Este middleware registra en los logs el camino de cada solicitud recibida antes de enviarla al siguiente paso del pipeline.

### 2. Middlewares: 
La solicitud pasa a través de varios middlewares, que son filtros o capas de software que pueden modificar la solicitud o respuesta. Estos pueden autenticar al usuario, validar datos, registrar logs, entre otros.

```Laravel 11```

```php
public function handle(Request $request, Closure $next)
{
    return $request->user()->role === 'admin' ? $next($request) : redirect('home');
}
```

* Este middleware verifica si el usuario tiene el rol de "admin" antes de permitirle acceder, redirigiéndolo a "home" si no cumple con la condición.

### 3. Enrutamiento (Routing): 
El pipeline pasa la solicitud al sistema de enrutamiento, que determina qué controlador o recurso manejará la solicitud según la URL y el método HTTP.

```Laravel 11```

```php
Route::get('/dashboard', [DashboardController::class, 'index'])->middleware('auth');
```
* Este enrutamiento dirige la solicitud al método index del controlador ```DashboardController``` y aplica el middleware ```auth``` para asegurar que solo usuarios autenticados puedan acceder.

### 4. Controladores (Controllers): 
El controlador maneja la lógica de negocio, interactuando con modelos, bases de datos o servicios, para cumplir con la solicitud del cliente.

```Laravel 11```

```php
public function show(Request $request)
{
    return view('profile', ['user' => $request->user()]);
}
```

* Este controlador maneja la solicitud y devuelve la vista ```profile```, pasando los datos del usuario autenticado a la vista.


### 5. Generación de la Respuesta: 
El controlador crea una respuesta, que puede ser una página HTML, JSON, XML, o cualquier otro formato solicitado.

```Laravel 11```

```php
public function show()
{
    return response()->json(['message' => 'Solicitud procesada con éxito'], 200);
}
```

* Este código genera una respuesta en formato JSON con un mensaje de éxito y el código de estado HTTP ```200```.


### 6. Salida a través de Middlewares: 
Antes de enviarse al cliente, la respuesta puede pasar de nuevo a través de middlewares que pueden, por ejemplo, comprimir los datos o aplicar medidas de seguridad.

```Laravel 11```

```php
public function handle(Request $request, Closure $next)
{
    $response = $next($request);
    $response->headers->set('X-Custom-Header', 'ValorEjemplo'); // Añadir un encabezado personalizado
    return $response;
}
```

* Este middleware modifica la respuesta añadiendo un encabezado personalizado antes de enviarla al cliente.

### 7. Envío de la Respuesta: 
Finalmente, el servidor envía la respuesta generada de vuelta al cliente.

```Laravel 11```

```php
// En cualquier controlador
public function index()
{
    return view('welcome'); // Enviar la vista 'welcome' como respuesta
}
```

* Este código envía la vista ```welcome``` como respuesta a la solicitud del usuario.

## Configuracion inicial para el desarrollo con Laravel 

### 1. Extensiones recomendadas de Laravel para Visual Studio Code

**Laravel Blade formatter:** https://marketplace.visualstudio.com/items?itemName=shufo.vscode-blade-formatter  
**Laravel Blade Snippets:** https://marketplace.visualstudio.com/items?itemName=onecentlin.laravel-blade  
**Laravel goto view:** https://marketplace.visualstudio.com/items?itemName=codingyu.laravel-goto-view  
**Laravel Snippets:** https://marketplace.visualstudio.com/items?itemName=onecentlin.laravel5-snippets  
**PHP Intelephense:** https://marketplace.visualstudio.com/items?itemName=bmewburn.vscode-intelephense-client  

### 2. Instalando Laragon.
Laragon es un entorno de desarrollo local para Windows que facilita la configuración de servidores web. Muy usado por desarrolladores PHP, incluye herramientas como Apache o Nginx, PHP, MySQL o MariaDB, y soporte para Node.js, Python y Ruby, permitiendo desarrollar aplicaciones con varias tecnologías sin configuraciones complicadas.

* **Descargar Laragon desde la siguiente dirección:** https://laragon.org/download
* **Actualizar PHP a la version mas reciente:** Laravel 11 requiere la version 8.2 de PHP como minimo  (Laragon viene con la version 8.1)
  decargamos PHP (version 8.2 o superior) desde: https://windows.php.net/download/ 
* **Copiar el archivo descargado:** Copiamos la nueva version de PHP en el directorio: C:\laragon\bin\php.

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/laragon.png?raw=true" alt="Imagen width="400" style />
</p>
<br>

* **Activar la nueva versión de PHP:** Clic derecho y seleccionamos la version de PHP que vamos a usar (8.3), reiniciamos todos los servicios.

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/laragon_php3.png?raw=true" alt="Imagen width="400" style />
</p>
<br>

* **Habilitar la extensión zip en PHP:** 
  
    * Abrimos el archivo php.ini:
    * Buscamos la línea que contiene ``` ;extension=zip``` .
    * Quitamos el punto y coma ```(;)``` al inicio de la línea para habilitar la extensión.

```php
C:\laragon\bin\php\php-8.3.13-nts-Win32-vs16-x64\php.ini
``` 

```php
extension=zip
```

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/zip1.png?raw=true" alt="Imagen width="400" style />
</p>
<br>

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/zip2.png?raw=true" alt="Imagen width="400" style />
</p>
<br>

* **Crear un nuevo proyecto de Laravel 11 en Laragon:**

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/laragon3.png?raw=true" alt="Imagen width="400" style />
</p>
<br>

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/laragon4.png?raw=true" alt="Imagen width="400" style />
</p>
<br>

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/laragon5.png?raw=true" alt="Imagen width="400" style />
</p>
<br>

## Ejemplo completo de request pipeline en Laravel 11

El proyecto simula un pipeline completo mediante el uso de middlewares personalizados, un controlador básico y una vista para responder a una solicitud. La aplicación sigue los pasos que normalmente atravesaría una solicitud HTTP en Laravel:

* **Recepción y Registro de la Solicitud:** Un middleware inicial intercepta y registra los datos de la solicitud (como URL, método y datos enviados).
* **Enrutamiento:** La solicitud se dirige a un controlador específico según la ruta solicitada.
* **Procesamiento en el Controlador:** El controlador maneja la lógica de la solicitud y prepara los datos para la vista.
* **Generación de la Respuesta:** Laravel utiliza una vista Blade para generar una respuesta HTML que se mostrará al usuario.
* **Registro de la Respuesta:** Un middleware final intercepta y registra la respuesta antes de enviarla.
* **Envío de la Respuesta:** Laravel entrega la respuesta generada al cliente, completando el flujo de la solicitud.

### 1. Estructura del Proyecto

```php
    project_root/
    ├── app/
    │   ├── Http/
    │   │   ├── Controllers/
    │   │   │   └── ExampleController.php
    │   │   └── Middleware/
    │   │       ├── LogRequestMiddleware.php
    │   │       └── LogResponseMiddleware.php
    ├── routes/
    │   └── web.php
    └── resources/
        └── views/
            └── response.blade.php
```

### 2. Estructura y Propósito de Cada Componente

* **Middlewares** (```LogRequestMiddleware``` y ```LogResponseMiddleware```): Se configuran en el archivo ```config/http.php``` y registran la solicitud y la respuesta, brindando puntos de auditoría y control.
* **Enrutamiento** (```routes/web.php```): Define la ruta /example que se enlaza con el controlador.
* **Controlador** (```ExampleController```): Maneja la lógica principal y prepara los datos para la vista.
* **Vista** (```response.blade.php```): Genera el HTML que se enviará como respuesta.

### 3. Configuración de middlewares globales

Configuraremos los middlewares en el archivo de configuración para asignarlos globalmente.
Al crear este archivo en config/http.php, podemos definir opciones personalizadas como el grupo de middlewares que Laravel aplicará en el pipeline de solicitudes. 

**Archivo:** ```config/http.php```

Agregamos los middlewares de solicitud y respuesta en el grupo middleware:

```php
<?php

return [
    'middleware' => [
        'web' => [
            \App\Http\Middleware\LogRequestMiddleware::class,
            \App\Http\Middleware\LogResponseMiddleware::class,
        ],
    ],
];
```

* **Explicación:** Este archivo centraliza la configuración de los middlewares en el grupo web. Aquí hemos añadido ```LogRequestMiddleware``` y ```LogResponseMiddleware``` para interceptar y procesar la solicitud y respuesta globalmente.


### 4. Middlewares de registro de solicitud y respuesta

**Archivo:** ```app/Http/Middleware/LogRequestMiddleware.php```

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Log; // Añadimos esta línea 

class LogRequestMiddleware
{
    public function handle(Request $request, Closure $next)
    {
        Log::info('Request received:', [
            'url' => $request->url(),
            'method' => $request->method(),
            'data' => $request->all(),
        ]);

        return $next($request);
    }
}
```

**Archivo:** ```app/Http/Middleware/LogResponseMiddleware.php```

```php
<?php

namespace App\Http\Middleware;
use Illuminate\Support\Facades\Log; // Añadimos esta línea 
use Closure;

class LogResponseMiddleware
{
    public function handle($request, Closure $next)
    {
        $response = $next($request);

        Log::info('Response sent:', [
            'content' => $response->getContent(),
        ]);

        return $response;
    }
}
```

* **Explicación:** Ambos middlewares registran respectivamente la solicitud y respuesta. Laravel aplicará estos middlewares automáticamente al grupo ```web```, gracias a la configuración en ```http.php```.
* Importamos la clase log en nuestros archivos definiendo el namespace en el encabezado:

```php
use Illuminate\Support\Facades\Log;
```

### 5. Enrutamiento (Routing)

**Archivo:** ```routes/web.php```

```php
use Illuminate\Support\Facades\Route;
use App\Http\Controllers\ExampleController;

Route::get('/example', [ExampleController::class, 'showExample']);
```

* **Explicación:** Aquí definimos una ruta simple que llama al método showExample del controlador ExampleController.

### 6. Crear el controlador

Para crear el controlador ```ExampleController``` mediante un comando en la consola de Artisan, utilizamos el siguiente comando:

```bash```
```php
php artisan make:controller ExampleController
```

Esto generará un archivo ```ExampleController.php``` en el directorio ```app/Http/Controllers```, el archivo de controlador tendrá una estructura básica.

**Archivo:** ```app/Http/Controllers/ExampleController.php```

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class ExampleController extends Controller
{
    public function showExample(Request $request)
    {
        $data = [
            'title' => 'Laravel Request Pipeline',
            'message' => 'Este es un ejemplo de REQUEST PIPELINE en Laravel 11.',
        ];

        return view('response', $data);
    }
}
```

* **Explicación:** El controlador ExampleController envía los datos necesarios a la vista para construir la respuesta.

### 7. Generación de la respuesta

**Archivo:** ```resources/views/response.blade.php```

```php
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{ $title }}</title>
</head>
<body>
    <h1>{{ $title }}</h1>
    <p>{{ $message }}</p>
</body>
</html>
```
* **Explicación:** La vista response.blade.php muestra la respuesta que se envía al usuario, utilizando los datos preparados en el controlador.

### 8. Vista en el navegador
A través de la siguiente URL accedemos a la respuesta generada por el navegador:

```php
    http://request-laravel.test/example
```
<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/salida_blade.png?raw=true" alt="Imagen width="400" style />
</p>
<br>

### 9. Flujo Completo del Request Pipeline en Laravel 11

1. **Recepción de Solicitud:** La solicitud se recibe y Laravel aplica los middlewares globales definidos en ```config/http.php```.
2. **Registro de Solicitud:** ```LogRequestMiddleware``` registra la información de la solicitud.
3. **Enrutamiento:** La solicitud es enrutada al controlador a través de ```routes/web.php```.
4. **Procesamiento en Controlador:** ```ExampleController``` maneja la solicitud y prepara los datos.
5. **Generación de Respuesta:** Laravel genera la vista y prepara la respuesta.
6. **Registro de Respuesta:** ```LogResponseMiddleware``` registra la respuesta.
7. **Envío de Respuesta:** Laravel envía la respuesta HTML generada al cliente.

# Middleware
## Concepto

Es un componente de software en aplicaciones web que actúa como una capa intermedia entre el servidor y las solicitudes del usuario. Su función principal es interceptar las solicitudes HTTP entrantes y realizar acciones específicas antes de que lleguen al controlador de la aplicación, o bien, procesar la respuesta antes de que llegue al cliente.

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/middleware.png?raw=true" alt="Imagen width="400" style />
</p>
<br>

## Funciones comunes del middleware:

### 1. Autenticación y Autorización: 
Verifica que el usuario esté autenticado y tenga los permisos necesarios.

```PHP```

```php
   if (!isset($_SESSION['user']) || $_SESSION['user'] !== 'admin') die('Acceso denegado'); 
```

* Verificar si el usuario está autenticado (```$_SESSION['user']``` está definido) y si tiene el rol de ```'admin'```. Si no cumple estos requisitos, muestra un mensaje de "Acceso denegado" y detiene la ejecución.

### 2. Registro de Actividad (Logging): 
Guarda un registro de las solicitudes o eventos para fines de auditoría.

```PHP```

```php
  file_put_contents('log.txt', date('Y-m-d H:i:s') . " - Acceso a " . $_SERVER['REQUEST_URI'] . "\n", FILE_APPEND);
```

* Agregar una entrada al archivo ```log.txt``` con la fecha, hora y la URL solicitada cada vez que alguien accede a una página, registrando así la actividad.


### 3. Manejo de Excepciones y Errores: 
Captura errores para personalizar la respuesta en caso de fallos.

```PHP```

```php
set_exception_handler(fn($e) => file_put_contents('errors.log', $e->getMessage() . "\n", FILE_APPEND));
try { /* Lógica de la aplicación aquí */ } catch (Exception $e) { throw $e; }
```
* Establecemos un manejador de excepciones que registra los errores en un archivo ```errors.log``` cuando ocurren excepciones durante la ejecución de la aplicación.

### 4. Caché y Compresión: 
Optimiza el rendimiento al servir contenido de forma más rápida o comprimida.

```PHP```

```php
header("Cache-Control: max-age=3600"); 
ob_start("ob_gzhandler"); 
```

* Este fragmento establece una cabecera para almacenar en caché la respuesta durante una hora y activa la compresión ```GZIP``` para reducir el tamaño de la respuesta enviada al cliente.

### 5. Validación de Datos: 
Verifica la estructura de los datos antes de pasarla al controlador.

```PHP```

```php
if (empty($_POST['email']) || !filter_var($_POST['email'], FILTER_VALIDATE_EMAIL)) die('Email inválido');
```

* Este fragmento verifica si el campo ```email``` está vacío o no es un correo electrónico válido. Si falla la validación, muestra un mensaje de "Email inválido" y detiene la ejecución de la aplicación.

## Ejemplo completo de autenticación con roles utilizando middlewares

Este ejemplo incluye los siguientes puntos:

* Middleware de autenticación para verificar si el usuario está autenticado.
* Middleware de autorización para verificar si el usuario tiene permisos específicos.
* Una simulación de base de datos de usuarios con roles.
* Páginas protegidas según el rol del usuario.

### 1. Estructura de archivos
Organizamos el proyecto de la siguiente manera:

```php
/mi-aplicacion
│
├── index.php
├── middleware
│   ├── AuthMiddleware.php
│   └── RoleMiddleware.php
├── pages
│   ├── admin_page.php
│   ├── user_page.php
│   ├── login.php
│   └── protected_page.php
├── users.php
└── logout.php
```
### 2. Simulación de Base de Datos de Usuarios

Creamos un archivo ```users.php``` que contendrá los usuarios y sus roles:

```php
<?php

return [
    'user1' => [
        'password' => 'password1', // Hashear en producción
        'role' => 'user',
    ],
    'admin' => [
        'password' => 'admin123', // Hashear en producción
        'role' => 'admin',
    ],
];
```



# Patrón de diseño pipeline
# Routing en php
# Expresiones regulares


