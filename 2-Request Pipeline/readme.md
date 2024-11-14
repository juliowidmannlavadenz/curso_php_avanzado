# Request pipeline
## Concepto

Request Pipeline (o "pipeline de solicitudes") es el conjunto de pasos o procesos que una solicitud HTTP sigue desde que es recibida por el servidor hasta que se genera una respuesta y se envía de vuelta al cliente. 

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

**Laravel Blade formatter:** [https://marketplace.visualstudio.com/items?itemName=shufo.vscode-blade-formatter](https://marketplace.visualstudio.com/items?itemName=shufo.vscode-blade-formatter)  
**Laravel Blade Snippets:** [https://marketplace.visualstudio.com/items?itemName=onecentlin.laravel-blade](https://marketplace.visualstudio.com/items?itemName=onecentlin.laravel-blade)  
**Laravel goto view:** [https://marketplace.visualstudio.com/items?itemName=codingyu.laravel-goto-view](https://marketplace.visualstudio.com/items?itemName=codingyu.laravel-goto-view)  
**Laravel Snippets:** [https://marketplace.visualstudio.com/items?itemName=onecentlin.laravel5-snippets](https://marketplace.visualstudio.com/items?itemName=onecentlin.laravel5-snippets)  
**PHP Intelephense:** [https://marketplace.visualstudio.com/items?itemName=bmewburn.vscode-intelephense-client](https://marketplace.visualstudio.com/items?itemName=bmewburn.vscode-intelephense-client)  


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
    request-laravel/
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

### Explicación:

* Este archivo centraliza la configuración de los middlewares en el grupo web. Aquí hemos añadido ```LogRequestMiddleware``` y ```LogResponseMiddleware``` para interceptar y procesar la solicitud y respuesta globalmente.


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

### Explicación:

* Ambos middlewares registran respectivamente la solicitud y respuesta. Laravel aplicará estos middlewares automáticamente al grupo ```web```, gracias a la configuración en ```http.php```.
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

### Explicación:

* Aquí definimos una ruta simple que llama al método showExample del controlador ExampleController.

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

### Explicación:

* El controlador ExampleController envía los datos necesarios a la vista para construir la respuesta.

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
### Explicación:

* La vista response.blade.php muestra la respuesta que se envía al usuario, utilizando los datos preparados en el controlador.

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
/middleware
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
**Archivo:** ```users.php```

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

### Explicación:

* Este archivo simula una base de datos de usuarios. Cada usuario tiene un nombre, una contraseña y un rol (usuario o administrador).
* En un entorno de producción, las contraseñas deben ser almacenadas de forma segura utilizando funciones de hashing como ```password_hash```.

### 3. Crear el Middleware de Autenticación
**Archivo:** ```middleware/AuthMiddleware.php```

```php
<?php

namespace Middleware;

class AuthMiddleware
{
    public function handle()
    {
        session_start();
        if (!isset($_SESSION['user'])) {
            header('Location: /pages/login.php?error=not_authenticated');
            exit();
        }
    }
}
```

### Explicación:

* Este middleware se encarga de verificar si un usuario ha iniciado sesión.
* Si el usuario no está autenticado, redirige a la página de inicio de sesión y muestra un mensaje de error.
* Se utiliza ```session_start()``` para acceder a la sesión actual.

### 4. Crear el Middleware de Roles
**Archivo:** ```middleware/RoleMiddleware.php```

```php
<?php

namespace Middleware;

class RoleMiddleware
{
    private $allowedRoles;

    public function __construct($allowedRoles = [])
    {
        $this->allowedRoles = $allowedRoles;
    }

    public function handle()
    {
        session_start();

        if (!empty($this->allowedRoles) && !in_array($_SESSION['role'], $this->allowedRoles)) {
            header('Location: /pages/login.php?error=forbidden');
            exit();
        }
    }
}
```

### Explicación:

* Este middleware verifica si el usuario tiene un rol que le permite acceder a una página específica.
* Se pasa un array de roles permitidos al constructor. Si el rol del usuario no está en esa lista, se redirige a la página de inicio de sesión con un mensaje de error.
* Al igual que el middleware de autenticación, utiliza session_start() para acceder a la información del usuario almacenada en la sesión.

### 5. Crear la Página de Inicio de Sesión
**Archivo:** ```pages/login.php```

```php
<?php
session_start();
$error = isset($_GET['error']) ? $_GET['error'] : '';
?>

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Inicio de Sesión</title>
</head>
<body>
    <h1>Iniciar Sesión</h1>
    <?php if ($error): ?>
        <p style="color:red;">
            <?php
            if ($error == 'not_authenticated') {
                echo "Debes iniciar sesión primero.";
            } elseif ($error == 'forbidden') {
                echo "No tienes permiso para acceder a esta página.";
            }
            ?>
        </p>
    <?php endif; ?>
    <form method="POST" action="login_process.php">
        <input type="text" name="username" placeholder="Usuario" required>
        <input type="password" name="password" placeholder="Contraseña" required>
        <button type="submit">Iniciar Sesión</button>
    </form>
</body>
</html>
```

### Explicación:

* Esta es la página de inicio de sesión donde los usuarios pueden ingresar sus credenciales.
* Si hay un error (como no estar autenticado o no tener permiso), se muestra un mensaje correspondiente.
* El formulario envía los datos a ```login_process.php``` para procesar el inicio de sesión.

### Vista en el navegador:

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/login_middleware.png?raw=true" alt="Imagen width="400" style />
</p>
<br>

### 6. Procesar el Inicio de Sesión
**Archivo:** ```login_process.php```

```php
<?php
session_start();
$users = require __DIR__ . '/../users.php';  // Ruta corregida para cargar correctamente users.php

$username = $_POST['username'] ?? '';
$password = $_POST['password'] ?? '';

if (isset($users[$username]) && $users[$username]['password'] === $password) {
    $_SESSION['user'] = $username;
    $_SESSION['role'] = $users[$username]['role'];
    header('Location: /pages/protected_page.php');
    exit();
} else {
    header('Location: /pages/login.php?error=invalid_credentials');
    exit();
}
```

### Explicación:

* Este archivo procesa las credenciales enviadas desde ```login.php```.
* Verifica si el usuario existe y si la contraseña es correcta. Si es así, establece las variables de sesión para el usuario y su rol.
* Si las credenciales son incorrectas, redirige de nuevo a la página de inicio de sesión con un mensaje de error.

### 7. Crear Páginas Protegidas
**Archivo:** ```pages/protected_page.php```

```php
<?php

require_once __DIR__ . '/../middleware/AuthMiddleware.php';
use Middleware\AuthMiddleware;

$authMiddleware = new AuthMiddleware();
$authMiddleware->handle();
$userRole = $_SESSION['role'] ?? null;

?>

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Página Protegida</title>
</head>
<body>
    <h1>Bienvenido a la Página Protegida</h1>
    
    <?php if ($userRole === 'admin'): ?>
        <p>Como administrador, tienes acceso a todas las páginas.</p>
        <a href="/pages/user_page.php">Ir a la página de usuario</a>
        <br>
        <a href="/pages/admin_page.php">Ir a la página de administración</a>
    <?php elseif ($userRole === 'user'): ?>
        <p>Como usuario, solo tienes acceso a tu página.</p>
        <a href="/pages/user_page.php">Ir a tu página de usuario</a>
    <?php else: ?>
        <p>No tienes permiso para acceder a esta página.</p>
    <?php endif; ?>

    <a href="/logout.php">Cerrar sesión</a>
</body>
</html>
```

### Explicación:

* Esta página está protegida por el middleware de autenticación, que verifica si el usuario ha iniciado sesión.
* Si el usuario está autenticado, puede ver el contenido de la página.
* Se proporcionan enlaces a otras páginas, incluyendo la página de usuario y la página de administración.

### Vista en el navegador:

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/protected_middleware.png?raw=true" alt="Imagen width="400" style />
</p>
<br>

### 8. Crear Página de Usuario
**Archivo:** ```pages/user_page.php```

```php
<?php

require_once __DIR__ . '/../middleware/AuthMiddleware.php';
use Middleware\AuthMiddleware;

$authMiddleware = new AuthMiddleware();
$authMiddleware->handle();

?>

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Página de Usuario</title>
</head>
<body>
    <h1>Bienvenido a la Página de Usuario</h1>
    <p>Solo los usuarios autenticados pueden ver esta página.</p>
    <a href="/logout.php">Cerrar sesión</a>
</body>
</html>
```

### Explicación:

* Esta página también está protegida por el middleware de autenticación, permitiendo solo el acceso a usuarios autenticados.
* Si el usuario tiene una sesión activa, puede ver el contenido de la página.

### Vista en el navegador:

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/usuario_middleware.png?raw=true" alt="Imagen width="400" style />
</p>
<br>

### 9. Crear Página de Administración
**Archivo:** ```pages/admin_page.php```

```php
<?php

require_once __DIR__ . '/../middleware/AuthMiddleware.php';
use Middleware\AuthMiddleware;

$authMiddleware = new AuthMiddleware();

$authMiddleware->handle();

?>

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Página de Administración</title>
</head>
<body>
    <h1>Bienvenido a la Página de Administración</h1>
    <p>Solo los administradores pueden ver esta página.</p>
    <a href="/logout.php">Cerrar sesión</a>
</body>
</html>
```

### Explicación:

* Esta página requiere tanto autenticación como un rol específico (en este caso, "admin").
* Si el usuario no es un administrador, será redirigido a la página de inicio de sesión.

### Vista en el navegador:

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/admin_middleware.png?raw=true" alt="Imagen width="400" style />
</p>
<br>

### 10. Cerrar Sesión
**Archivo:** ```logout.php```

```php
<?php
session_start();
session_destroy();
header('Location: /pages/login.php');
exit();
```

### Explicación:

* Este archivo maneja el cierre de sesión.
* Utiliza session_start() para acceder a la sesión y luego la destruye, eliminando todas las variables de sesión.
* Después de cerrar la sesión, redirige al usuario a la página de inicio de sesión.

### Conclusiones:

Este sistema de autenticación y autorización utiliza middlewares para proteger el acceso según el rol del usuario. A continuación, se explica cada parte clave:

**1. Middleware de Autenticación (```AuthMiddleware```):**

* La función handle() verifica si el usuario ha iniciado sesión comprobando la existencia de la variable $_SESSION['user'].
* Si no está autenticado, redirige al usuario a la página de inicio de sesión, asegurando que solo los usuarios logueados accedan a páginas protegidas.

**2. Middleware de Autorización (```RoleMiddleware```):**

* La función ```handle()``` de ```RoleMiddleware``` revisa el rol del usuario, almacenado en ```$_SESSION['role']```, para verificar si el usuario tiene los permisos adecuados para la página solicitada.
* Por ejemplo, el middleware garantiza que solo los administradores puedan acceder a la página de administración.
  
**3. Manejo de Sesiones y Redireccionamiento:**

* La autenticación y autorización se gestionan con variables de sesión (```$_SESSION['user']``` y ```$_SESSION['role']```).
* Según el rol, los usuarios son redirigidos a páginas específicas como ```user_page.php``` o ```admin_page.php```, proporcionando una experiencia controlada y segura.

# Patrón de diseño pipeline

También conocido como ```Pipeline Pattern``` o ```Chain of Responsibility```, es útil cuando deseas procesar datos en una serie de pasos secuenciales o en una cadena de operaciones. Este patrón permite dividir una tarea en etapas independientes, donde cada etapa realiza una operación específica y pasa los datos al siguiente paso.

En PHP, un Pipeline típico funciona mediante clases que procesan datos y pasan los resultados al siguiente procesador en la secuencia. Esto es particularmente útil cuando quieres construir sistemas extensibles y modulares donde las etapas del procesamiento pueden añadirse, quitarse o cambiarse sin afectar otras partes del sistema.

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/patron_pipeline.png?raw=true" alt="Imagen width="400" style />
</p>
<br>

## Características principales:

### 1. Encadenamiento de Operaciones:
Cada etapa del proceso toma una entrada, realiza una operación y produce una salida que se utiliza en el siguiente paso.

```PHP```

```php
class Pipeline {
    public static function make($input) {
        return (new static)->step1($input)->step2()->step3();
    }
    public function step1($input) { $this->data = $input * 2; return $this; }
    public function step2() { $this->data += 3; return $this; }
    public function step3() { echo $this->data * 4; }
}
Pipeline::make(5); // Salida: 52
```
* step1 multiplica el valor de entrada por 2.
* step2 suma 3 al valor actual.
* step3 multiplica el resultado por 4 y lo muestra en pantalla.

### 2. Modularidad y Extensibilidad: 
Las etapas pueden añadirse, quitarse o cambiarse sin cambiar la estructura general.

```PHP```

```php
class Pipeline {
    public static function make($input, $operations) {
        return array_reduce($operations, fn($carry, $operation) => $operation($carry), $input);
    }
}
echo Pipeline::make(5, [fn($x) => $x * 2, fn($x) => $x + 3, fn($x) => $x * 4]); // Salida: 52
```

* La función ```make``` toma un valor de entrada (```$input```) y una lista de operaciones (```$operations```).
* Cada operación se aplica de manera secuencial usando ```array_reduce```, permitiendo agregar o modificar fácilmente cualquier operación.
* El resultado final de las operaciones encadenadas es ```52``` con el valor inicial ``` 5 ```.


### 3. Mantenimiento:
El código es más fácil de mantener porque cada etapa tiene su propia responsabilidad.

```PHP```

```php
class Pipeline { public static function make($input, $stages) { foreach ($stages as $stage) $input = (new $stage)->handle($input); return $input; }}
class MultiplyByTwo { public function handle($input) { return $input * 2; }}
class AddThree { public function handle($input) { return $input + 3; }}
echo Pipeline::make(5, [MultiplyByTwo::class, AddThree::class]); // Salida: 13
```

* La clase ```Pipeline``` acepta un valor de entrada y una lista de clases de "etapas" (```$stages```).
* Cada clase de etapa (```MultiplyByTwo```, ```AddThree```, etc.) tiene un método ```handle``` que aplica una operación.
* Es fácil mantener y extender el pipeline agregando nuevas clases para operaciones adicionales.


### 4. Inmutabilidad (Opcional): 
Cada operación no altera el estado global, sino que opera sobre los datos que se le pasan.

```PHP```

```php
class Pipeline {
    public static function make($input, $operations) {
        return array_reduce($operations, fn($carry, $operation) => $operation($carry), $input);
    }
}
echo Pipeline::make(5, [fn($x) => $x * 2, fn($x) => $x + 3, fn($x) => $x * 4]); // Salida: 52
```

* Cada operación toma el resultado del paso anterior sin modificarlo directamente.
* Usamos ```array_reduce``` para aplicar cada operación de forma inmutable.
* El resultado es ```52```, y el pipeline asegura que cada paso produce un nuevo valor sin alterar el original.

## Ejemplo de un pipeline

Veamos un ejemplo de un pipeline en PHP para procesar una cadena de texto. Este pipeline realiza tres pasos:
1. Convertir el texto a minúsculas.
2. Eliminar espacios en blanco adicionales.
3. Reemplazar ciertas palabras en el texto.

> Este ejemplo incluye la clase principal Pipeline y las clases de procesamiento que definen cada operación.

**Archivo:** ```pipeline/index.php```

```php
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pipeline de Procesamiento de Texto</title>
</head>
<body>
    <h2>Ingrese una palabra o frase para procesar</h2>
    <form method="POST" action="">
        <label for="inputText">Texto:</label>
        <input type="text" id="inputText" name="inputText" required>
        <button type="submit">Procesar</button>
    </form>

    <?php
    if ($_SERVER["REQUEST_METHOD"] === "POST") {
        // Incluimos la lógica del Pipeline aquí
        interface StageInterface {
            public function handle(string $input): string;
        }

        class ConvertToLowercase implements StageInterface {
            public function handle(string $input): string {
                return strtolower($input);
            }
        }

        class RemoveExtraSpaces implements StageInterface {
            public function handle(string $input): string {
                return preg_replace('/\s+/', ' ', trim($input));
            }
        }

        class ReplaceWords implements StageInterface {
            protected $search;
            protected $replace;

            public function __construct(array $search, array $replace) {
                $this->search = $search;
                $this->replace = $replace;
            }

            public function handle(string $input): string {
                return str_replace($this->search, $this->replace, $input);
            }
        }

        class Pipeline {
            private $stages = [];

            public function addStage(StageInterface $stage): self {
                $this->stages[] = $stage;
                return $this;
            }

            public function process(string $input): string {
                foreach ($this->stages as $stage) {
                    $input = $stage->handle($input);
                }
                return $input;
            }
        }

        // Obtenemos el texto ingresado
        $inputText = $_POST['inputText'];

        // Configuramos el Pipeline con las etapas
        $pipeline = (new Pipeline())
            ->addStage(new ConvertToLowercase())
            ->addStage(new RemoveExtraSpaces())
            ->addStage(new ReplaceWords(['hello', 'world', 'name'], ['hola', 'mundo', 'nombre']));

        // Procesamos el texto
        $result = $pipeline->process($inputText);

        echo "<h3>Texto procesado:</h3>";
        echo "<p>" . htmlspecialchars($result) . "</p>";
    }
    ?>
</body>
</html>
```

### Explicación del código

1. **Formulario HTML:**

* Contiene un campo de texto (inputText) donde el usuario ingresa una palabra o frase y un botón "Procesar" para enviar el formulario.

2. **Lógica de procesamiento en PHP:**

* El procesamiento del pipeline se define en PHP. Primero, verifica que el formulario fue enviado (```$_SERVER["REQUEST_METHOD"] === "POST"```).
* Define las clases ```ConvertToLowercase```, ```RemoveExtraSpaces```, y ```ReplaceWords```, junto con la clase ```Pipeline```.
  
3. **Pipeline:**

* Se crea una instancia del Pipeline, y se añaden las etapas de procesamiento en el orden deseado.
* Se procesa el texto ingresado y se muestra el resultado.

4. **Salida:**
* Muestra el texto procesado, que ha pasado por todas las etapas del pipeline.

Este código permite que el usuario ingrese un texto, lo procese y vea el resultado directamente en la página web.

### Vista en el navegador:

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/pipeline_texto.png?raw=true" alt="Imagen width="400" style />
</p>
<br>

# Routing en php
## Concepto 

El routing (o enrutamiento) en PHP es el proceso de dirigir las solicitudes HTTP a funciones, controladores o archivos específicos en función de la URL solicitada. Esto permite crear aplicaciones web donde cada URL apunta a una lógica específica, facilitando la organización del código y permitiendo URLs amigables.

En una aplicación sin routing, cada página se asocia directamente con un archivo PHP. Con routing, en cambio, existe un punto de entrada (generalmente ```index.php```) que decide qué lógica ejecutar según la URL solicitada, organizando mejor la aplicación y permitiendo URLs más limpias y personalizables.

## Tipos de Routing en PHP

### 1. Routing estático: 
Cada ruta se asocia con una URL fija. Es el tipo más sencillo, donde cada URL está definida explícitamente. 

```PHP```

```php
$uri = $_SERVER['REQUEST_URI'];
if ($uri == '/home') echo "Bienvenido a la página de inicio";
elseif ($uri == '/about') echo "Acerca de nosotros";
```
* Este código redirige según la URL (```/home```, ```/about```) y muestra contenido específico para cada ruta.

### 2. Routing dinámico: 
Permite rutas que contienen variables, adaptándose a URLs que pueden cambiar en tiempo de ejecución. Ejemplo: /products/{id}, donde {id} puede ser cualquier identificador de producto.

```PHP```

```php
$routes = ['home' => 'Inicio', 'about' => 'Acerca de nosotros'];
$uri = trim($_SERVER['REQUEST_URI'], '/');
echo $routes[$uri] ?? "Página no encontrada";
```

* Este código define rutas dinámicas en un array y muestra el contenido según la URL ingresada, o un mensaje de error si la ruta no existe.

### 3. Routing basado en controladores: 
Se asignan rutas a métodos en controladores, ideal para aplicaciones grandes, donde la organización en controladores facilita el mantenimiento y la escalabilidad.

```PHP```

```php
$routes = ['home' => 'HomeController@index', 'about' => 'AboutController@show'];
$uri = trim($_SERVER['REQUEST_URI'], '/');
list($controller, $method) = explode('@', $routes[$uri] ?? 'ErrorController@notFound');
(new $controller)->$method();
```

* Este código llama al controlador y método correspondiente según la URL, o al controlador de error si la ruta no existe.

## Pasos para Implementar Routing en PHP

### 1. Definir el punto de entrada: 
Crear un archivo index.php como punto de entrada de todas las solicitudes.

### 2. Capturar la solicitud: 
Obtener la URL solicitada mediante ```$_SERVER['REQUEST_URI']``` o funciones específicas del framework (si se usa uno).

### 3. Configurar las rutas:
Crear una lista de rutas, con sus correspondientes controladores o funciones, almacenándola en un arreglo o una estructura más compleja.

### 4. Determinar la ruta correspondiente: 
Buscar la URL en el arreglo de rutas y ejecutar la lógica asociada (puede ser una función o un método del controlador).

### 5. Manejar rutas dinámicas:
Utilizar expresiones regulares o patrones para reconocer rutas que contienen variables.

## Ejemplo completo de routing en PHP

Este ejemplo implementa un sistema de Kardex de productos, utilizando enrutamiento básico y estructurado en archivos individuales para mantener la claridad y la organización del código. 

* El sistema permite agregar productos y visualizar una lista de ellos, o "Kardex".
* Usa rutas para procesar y redirigir solicitudes.
* Controladores para manejar la lógica.
* Un formulario para ingresar productos a la base de datos MySQL.

### Configuraciones iniciales:
Para nuestro desarrollo estamos utilizando el servidor local ```Laragon```.

* **Confirmar el DocumentRoot correcto en Laragon:**
Es importante que el ```DocumentRoot``` esté configurado para que Apache sirva la aplicación desde la carpeta ```public```.

1. Vamos a **Menu > Apache > sites-enabled** y localizamos el archivo de configuración que corresponde a nuestro ejemplo de desarrollo.
2. Asegúrate de que esté configurado así:

```php
<VirtualHost *:80> 
    DocumentRoot "C:/laragon/www/routingPHP/public"
    ServerName routingPHP.test
    ServerAlias *.routingPHP.test
    <Directory "C:/laragon/www/routingPHP/public">
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

* **Crear el archivo .htaccess en la carpeta public:**
Creamos el archivo ```.htaccess``` dentro de la carpeta public para redirigir todas las solicitudes a ```index.php```, como sigue:

```php
Options -MultiViews
RewriteEngine On

# Redirige todas las solicitudes a index.php
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^ index.php [QSA,L]
```

### 1. Estructura de archivos del Proyecto

```php
routingPHP/
├── public/
│   └── index.php         # Punto de entrada
├── src/
│   ├── Controllers/
│   │   ├── ProductController.php
│   └── Database.php      # Conexión a la base de datos
├── views/
│   ├── add_product.php   # Formulario para agregar productos
│   └── kardex.php        # Página para ver el kardex de productos
└── routes.php            # Definición de rutas
```
### 2. Configuración de la base de datos MySQL

```php
CREATE DATABASE routingphp;
USE routingphp;

CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    quantity INT DEFAULT 0,
    price DECIMAL(10, 2) NOT NULL
);
```

### 3. Punto de entrada
**Archivo:** ```public/index.php```

```php
<?php

require_once __DIR__ . '/../src/Database.php';
require_once __DIR__ . '/../routes.php';

$uri = parse_url($_SERVER['REQUEST_URI'], PHP_URL_PATH);

route($uri, $_SERVER['REQUEST_METHOD']);
```

### Explicación:

* **Rol:** Punto de entrada principal de la aplicación. Captura la solicitud del usuario, determina la URI (parte de la URL que viene después del dominio) y redirige la solicitud a la ruta correspondiente.
* **Funcionamiento:** Usa parse_url para obtener la URI y $_SERVER['REQUEST_METHOD'] para conocer el método HTTP usado. Llama a la función route de routes.php con esta información para procesar la solicitud.

### 4. Configuración de las rutas  
**Archivo:** ```routes.php```

```php
<?php
// routes.php

require_once __DIR__ . '/src/Controllers/ProductController.php';

function route($uri, $method) {
    if ($uri === '/' && $method === 'GET') {
        // Redirige automáticamente a la página kardex o muestra la página principal
        header("Location: /kardex");
        exit();
    } elseif ($uri === '/add-product' && $method === 'GET') {
        (new ProductController())->showAddProductForm();
    } elseif ($uri === '/add-product' && $method === 'POST') {
        (new ProductController())->addProduct();
    } elseif (preg_match('/^\/kardex$/', $uri)) {
        (new ProductController())->showKardex();
    } else {
        echo "404 Not Found";
    }
}
```
### Explicación:

* **Rol:** Define las rutas de la aplicación y asigna una acción específica a cada ruta según la URI y el método HTTP.
* **Funcionamiento:** Evalúa la URI recibida y el método HTTP. Usa expresiones regulares y condicionales ```if-else``` para verificar qué ruta debe ejecutarse. Las rutas posibles son:
  
    * ```GET /add-product```: Muestra el formulario de productos.
    * ```POST /add-product```: Envía los datos del formulario a la base de datos.
    * ```GET /kardex```: Muestra la vista del Kardex con los productos.

### 5. Creación del controlador
**Archivo:** ```src/Controllers/ProductController.php```

```php
<?php

require_once __DIR__ . '/../Database.php';

class ProductController {
    private $db;

    public function __construct() {
        $this->db = new Database();
    }

    public function showAddProductForm() {
        require_once __DIR__ . '/../../views/add_product.php';
    }

    public function addProduct() {
        $name = $_POST['name'];
        $quantity = $_POST['quantity'];
        $price = $_POST['price'];

        $stmt = $this->db->getConnection()->prepare("INSERT INTO products (name, quantity, price) VALUES (?, ?, ?)");
        $stmt->bind_param("sid", $name, $quantity, $price);
        $stmt->execute();

        header("Location: /kardex");
    }

    public function showKardex() {
        $result = $this->db->getConnection()->query("SELECT * FROM products");
        $products = $result->fetch_all(MYSQLI_ASSOC);

        require_once __DIR__ . '/../../views/kardex.php';
    }
}
```
### Explicación:

* **Rol:** Contiene la lógica de negocio relacionada con los productos, como agregar productos y mostrar el Kardex.
* **Funcionamiento:** Tiene tres métodos principales:
  
    * ```showAddProductForm```: Carga la vista ```add_product.php``` para agregar productos.
    * ```addProduct```: Procesa los datos del formulario, inserta el producto en la base de datos y redirige al Kardex.
    * ```showKardex```: Recupera todos los productos de la base de datos y carga la vista ```kardex.php``` para mostrarlos.

### 6. Conexion a la base de datos
**Archivo:** ```src/Database.php```

```php
<?php

class Database {
    private $connection;

    public function __construct() {
        $this->connection = new mysqli("localhost", "root", "", "routingphp");
        if ($this->connection->connect_error) {
            die("Connection failed: " . $this->connection->connect_error);
        }
    }

    public function getConnection() {
        return $this->connection;
    }
}
```

### Explicación:

* **Rol:** Establece la conexión con la base de datos MySQL.
* **Funcionamiento:** Usa la extensión ```mysqli``` de PHP para conectarse a una base de datos llamada "kardex" en el servidor local. Si la conexión falla, muestra un mensaje de error. Proporciona un método ```getConnection``` para obtener esta conexión en los controladores.

### 7. Formulario para agregar productos
**Archivo:** ```views/add_product.php```

```php
<!DOCTYPE html>
<html>
<head><title>Agregar Producto</title></head>
<body>
    <h1>Agregar Producto</h1>
    <form action="/add-product" method="POST">
        <label>Nombre del Producto:</label><br>
        <input type="text" name="name" required><br><br>

        <label>Cantidad:</label><br>
        <input type="number" name="quantity" required><br><br>

        <label>Precio:</label><br>
        <input type="text" name="price" required><br><br>

        <input type="submit" value="Agregar">
    </form>
</body>
</html>
```

### Explicación:

* **Rol:** Formulario HTML que permite al usuario ingresar un nuevo producto.
* **Funcionamiento:** Incluye un formulario que envía los datos al controlador ```ProductController.php``` a través de la ruta ```/add-product``` utilizando el método POST.

### Vista en el navegador:

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/agregar_producto_routing.png?raw=true" alt="Imagen width="400" style />
</p>
<br>

### 8. Vista del kardex
**Archivo:** ```views/kardex.php```

```php
<!DOCTYPE html>
<html>
<head><title>Kardex de Productos</title></head>
<body>
    <h1>Kardex de Productos</h1>
    <table border="1">
        <tr>
            <th>ID</th>
            <th>Nombre</th>
            <th>Cantidad</th>
            <th>Precio</th>
        </tr>
        <?php foreach ($products as $product): ?>
            <tr>
                <td><?= $product['id'] ?></td>
                <td><?= $product['name'] ?></td>
                <td><?= $product['quantity'] ?></td>
                <td><?= $product['price'] ?></td>
            </tr>
        <?php endforeach; ?>
    </table>
</body>
</html>
```

### Explicación:

* **Rol:** Muestra el Kardex de productos en una tabla HTML.
* **Funcionamiento:** Recibe los datos de productos (obtenidos del controlador) y los presenta en una tabla. Cada fila muestra el ID, nombre, cantidad y precio de cada producto en la base de datos.

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/kardex_routing.png?raw=true" alt="Imagen width="400" style />
</p>
<br>

### 9. Acceso a las paginas:
Para ver la pagina ```Agregar Producto``` vamos a la siguiente URL:

```php
http://routingphp.test/add-product
```

Para ver la pagina ```Kardex de Productos``` vamos a la siguiente URL:

```php
http://routingphp.test/kardex
```

### Resumen:

Este proyecto demuestra cómo implementar un sistema de enrutamiento básico y controlador en PHP puro, sin un framework. Consta de los siguientes elementos:

1. **Punto de Entrada:** ```public/index.php``` gestiona la entrada de todas las solicitudes y las envía al enrutador.
2. **Rutas:** ```routes.php``` define el mapeo de rutas URI y métodos HTTP para determinar qué controlador debe responder.
3. **Controlador:** ```ProductController.php``` maneja la lógica de negocio (agregar productos, ver Kardex).
4. **Base de Datos:** ```Database.php``` se conecta a la base de datos.
5. **Vistas:** Formularios y tablas que muestran la interfaz de usuario para agregar y ver productos.

### Conclusiones:
> * Este ejemplo logra estructurar una aplicación de PHP puro utilizando enrutamiento y controladores de forma sencilla, ideal para proyectos pequeños.
> * La separación de responsabilidades permite que cada archivo tenga un propósito claro, facilitando el mantenimiento y la expansión futura del proyecto.
> * Este esquema también es una introducción útil para aprender sobre arquitecturas de controladores y rutas en PHP antes de avanzar a frameworks más robustos como Laravel.

# Expresiones regulares
## ¿Qué son las expresiones regulares?

Las expresiones regulares (```regex``` o ```regexp```) son secuencias de caracteres que forman un patrón de búsqueda. Este patrón se utiliza para realizar coincidencias, búsqueda y manipulación de texto en cadenas. Las expresiones regulares son una herramienta poderosa para validar, encontrar y reemplazar texto de forma eficiente y flexible.

## ¿Como funcionan?

Una expresión regular se compone de símbolos, caracteres y operadores que representan patrones específicos. Al aplicar una expresión regular a un texto, el motor de búsqueda compara el patrón con el texto y busca coincidencias, lo que permite:

### 1. Validar entradas: 
* Comprobar si una cadena sigue un formato específico, como un correo electrónico o un número de teléfono.

### 2. Buscar y extraer información: 
* Localizar palabras o patrones específicos dentro de un texto.

### 3. Reemplazar texto: 
* Cambiar partes del texto que coinciden con el patrón por otro valor.

## Ejemplos comunes de expresiones regulares

* **Validar correo electrónico:** ```^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$```
* **Buscar números:** ```\d+``` (encuentra secuencias de uno o más dígitos)
* **Extraer palabras:** ```\b\w+\b``` (encuentra palabras completas en un texto)

## Sintaxis básica ```regex```

Cuadro que describe algunas de las expresiones regulares más comunes:

| Expresión | Descripción                              | Ejemplo           | Coincidencia                               |
|-----------|------------------------------------------|-------------------|--------------------------------------------|
| `.`       | Cualquier carácter (excepto nueva línea) | `c.t`            | `cat`, `cut`, `cot`                       |
| `^`       | Inicio de línea                          | `^Hola`          | Coincide con `Hola` solo al inicio de una línea |
| `$`       | Fin de línea                             | `adios$`         | Coincide con `adios` solo al final de una línea |
| `*`       | Cero o más repeticiones                  | `a*`             | `a`, `aa`, `aaa`, o vacío                 |
| `+`       | Una o más repeticiones                   | `a+`             | `a`, `aa`, `aaa`                          |
| `?`       | Cero o una repetición                    | `colou?r`        | `color`, `colour`                         |
| `{n}`     | Exactamente n repeticiones               | `a{3}`           | `aaa`                                     |
| `{n,}`    | n o más repeticiones                     | `a{2,}`          | `aa`, `aaa`, `aaaa`, etc.                 |
| `{n,m}`   | Entre n y m repeticiones                 | `a{2,4}`         | `aa`, `aaa`, `aaaa`                       |
| `[]`      | Conjunto de caracteres                   | `[abc]`          | `a`, `b`, o `c`                           |
| `[^]`     | Negación de un conjunto                  | `[^abc]`         | Cualquier carácter excepto `a`, `b`, o `c`|
| `|`       | Alternancia (OR)                         | `a|b`            | `a` o `b`                                 |
| `\d`      | Dígito                                   | `\d{3}`          | Tres dígitos seguidos, como `123`         |
| `\D`      | No es un dígito                          | `\D`             | Cualquier carácter excepto dígitos        |
| `\w`      | Caracter alfanumérico                    | `\w+`            | `hola`, `123`, `abc123`                   |
| `\W`      | No es alfanumérico                       | `\W`             | Cualquier símbolo o espacio               |
| `\s`      | Espacio en blanco                        | `\s+`            | Uno o más espacios                        |
| `\S`      | No es un espacio en blanco               | `\S`             | Cualquier carácter que no sea espacio     |

## Expresiones regulares en PHP

En PHP, las expresiones regulares se pueden manejar utilizando dos tipos principales de funciones:

### 1. PCRE (Perl Compatible Regular Expressions): 
Usan funciones como ```preg_match```, ```preg_match_all```, ```preg_replace```, y son las más comunes por su compatibilidad y flexibilidad.

### 2. POSIX: 
Aunque existen funciones de POSIX como ```ereg```, están obsoletas en versiones modernas de PHP y no se recomiendan.

## Ejemplos y Funciones Comunes de PCRE

Algunos ejemplos y las funciones más usadas:

### 1. ```preg_match``` (Coincidir patrón en una cadena)
Esta función verifica si una cadena cumple con un patrón de expresión regular.

```php
<?php
$cadena = "hola@example.com";
$patron = "/^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/";

if (preg_match($patron, $cadena)) {
    echo "El correo es válido";
} else {
    echo "El correo no es válido";
}
?>
```

### 2. ```preg_match_all``` (Coincidir todas las apariciones de un patrón en una cadena)

Esta función encuentra todas las coincidencias de un patrón en una cadena y las almacena en un array.

```php
<?php
$cadena = "123-456-7890 y 987-654-3210 son números de ejemplo.";
$patron = "/\d{3}-\d{3}-\d{4}/";

preg_match_all($patron, $cadena, $coincidencias);
print_r($coincidencias[0]);  // Muestra todos los números encontrados
?>
```

### 3. ```preg_replace``` (Reemplazar texto con expresiones regulares)

Esta función busca coincidencias de un patrón y las reemplaza con otra cadena.

```php
<?php
$texto = "Visita mi sitio web en http://miweb.com.";
$patron = "/http:\/\/[a-zA-Z0-9.-]+/";

$resultado = preg_replace($patron, "https://example.com", $texto);
echo $resultado;  // Reemplaza la URL original por "https://example.com"
?>
```

### 4. ```preg_split``` (Dividir una cadena con expresiones regulares)

Esta función divide una cadena en un array usando un patrón de expresión regular.

```php
<?php
$cadena = "uno, dos, tres, cuatro";
$patron = "/,\s*/";

$array = preg_split($patron, $cadena);
print_r($array);  // Devuelve ["uno", "dos", "tres", "cuatro"]
?>
```

## Caracteres Especiales en Expresiones Regulares de PHP

Algunos de los caracteres más usados en las expresiones regulares de PHP son:

| Expresión | Descripción                              | Ejemplo           | Coincidencia                               |
|-----------|------------------------------------------|-------------------|--------------------------------------------|
| `.`       | Cualquier carácter (excepto nueva línea) | `c.t`            | `cat`, `cut`, `cot`                       |
| `^`       | Inicio de línea                          | `^Hola`          | Coincide con `Hola` solo al inicio de una línea |
| `$`       | Fin de línea                             | `adios$`         | Coincide con `adios` solo al final de una línea |
| `*`       | Cero o más repeticiones                  | `a*`             | `a`, `aa`, `aaa`, o vacío                 |
| `+`       | Una o más repeticiones                   | `a+`             | `a`, `aa`, `aaa`                          |
| `?`       | Cero o una repetición                    | `colou?r`        | `color`, `colour`                         |
| `{n}`     | Exactamente n repeticiones               | `a{3}`           | `aaa`                                     |
| `{n,}`    | n o más repeticiones                     | `a{2,}`          | `aa`, `aaa`, `aaaa`, etc.                 |
| `{n,m}`   | Entre n y m repeticiones                 | `a{2,4}`         | `aa`, `aaa`, `aaaa`                       |
| `[]`      | Conjunto de caracteres                   | `[abc]`          | `a`, `b`, o `c`                           |
| `[^]`     | Negación de un conjunto                  | `[^abc]`         | Cualquier carácter excepto `a`, `b`, o `c`|
| `|`       | Alternancia (OR)                         | `a|b`            | `a` o `b`                                 |
| `\d`      | Dígito                                   | `\d{3}`          | Tres dígitos seguidos, como `123`         |
| `\D`      | No es un dígito                          | `\D`             | Cualquier carácter excepto dígitos        |
| `\w`      | Caracter alfanumérico                    | `\w+`            | `hola`, `123`, `abc123`                   |
| `\W`      | No es alfanumérico                       | `\W`             | Cualquier símbolo o espacio               |
| `\s`      | Espacio en blanco                        | `\s+`            | Uno o más espacios                        |
| `\S`      | No es un espacio en blanco               | `\S`             | Cualquier carácter que no sea espacio     |

## Ejemplo de expresiones regulares con PHP

Este ejemplo permite gestionar diferentes URLs y redirigirlas a funciones específicas que procesan la lógica de cada página. 

* Al utilizar expresiones regulares, el router puede identificar patrones en las URLs, lo que permite crear rutas dinámicas y amigables para el usuario.
* Este enfoque modular facilita la organización del código y mejora la mantenibilidad de las aplicaciones web.

### 1. Configurar el archivo ```.htaccess```

Creamos un archivo llamado ```.htaccess```, este archivo es necesario para permitir que el servidor reescriba las URLs correctamente.

```php
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteBase /
    RewriteRule ^index\.php$ - [L]
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule . /index.php [L]
</IfModule>
```

### 2. Creación del archivo index.php
**Archivo:** ```regex.test/index.php```

```php
<?php

class Router {
    private $rutas = [];

    public function registrarRuta($patron, $accion) {
        $this->rutas[$patron] = $accion;
    }

    public function resolver($url) {
        foreach ($this->rutas as $patron => $accion) {
            if (preg_match($patron, $url, $matches)) {
                array_shift($matches); 
                call_user_func_array($accion, $matches); 
                return;
            }
        }
      
        $this->mostrarError404();
    }

    private function mostrarError404() {
        http_response_code(404);
        echo "Ruta no encontrada: 404";
    }
}


$router = new Router();

$router->registrarRuta("/^\/$/", function() {
    echo "<h1>Página de inicio</h1>";
});

$router->registrarRuta("/^\/productos\/(\d+)$/", function($id) {
    echo "<h1>Página de producto con ID: $id<h1>";
});

$router->registrarRuta("/^\/categorias\/(\w+)$/", function($categoria) {
    echo "<h1>Página de categoría: $categoria<h1>";
});

$router->registrarRuta("/^\/contacto$/", function() {
    echo "<h1>Página de contacto<h1>";
});

$url = parse_url($_SERVER['REQUEST_URI'], PHP_URL_PATH);
$router->resolver($url);

?>
```
### 3. Explicación de las Funciones

1. ```registrarRuta($patron, $accion)```
* Propósito: Permite registrar una nueva ruta en el router.
* Parámetros:

    * ```$patron:``` Una expresión regular que define el patrón de la URL
    * ```$accion:``` Una función de callback que se ejecutará si la URL coincide con el patrón.

* **Función:** Esta función añade el patrón y su correspondiente acción al array ```$rutas```, que se utiliza posteriormente para resolver las rutas.

2. ```resolver($url)```

* **Propósito:** Determina la acción correspondiente a la URL solicitada y la ejecuta.
* **Parámetros:**

    * ```$url```: La URL que se va a analizar para encontrar una coincidencia.
      
* **Función:** Esta función itera sobre las rutas registradas, verifica si hay coincidencias utilizando ```preg_match```, y si encuentra una coincidencia, ejecuta la acción asociada a esa ruta. Si no hay coincidencias, llama a ```mostrarError404()```.

3. ```mostrarError404()```

* ```Propósito:``` Muestra un mensaje de error cuando la URL solicitada no coincide con ninguna de las rutas registradas.
* ```Función:``` Esta función establece el código de estado HTTP en 404 y muestra el mensaje "Ruta no encontrada: 404" al usuario. Esto es útil para informar que la página solicitada no existe.

### 4. Acceder a las Rutas:
Abrimos el navegador y probamos las siguientes URLs para ver cómo la clase Router resuelve las rutas:

1. **Página de inicio:**

```php
http://regex.test
```
2. **Producto específico:**

```php
http://regex.test/productos/123
```
* Debería mostrar: "Página de producto con ID: 123".

3. **Categoría específica:**

```php
http://regex.test/categorias/electronica
```
* Debería mostrar: "Página de categoría: electronica".

4.  **Página de contacto:**

```php
http://regex.test/contacto
```
* Debería mostrar: "Página de contacto".

5.  **Ruta no Encontrada:**
Si accedes a una ruta no definida, como:

```php
http://regex.test/no-existe
```
* El router mostrará Ruta no encontrada: ```404``` y devolverá el código de respuesta ```404``` para indicar que la página no existe.

### 4. Expresiones regulares utilizadas en el routing:

Cada expresión regular se utiliza para definir patrones específicos que coinciden con las URLs solicitadas.

1. ```/^\/$/```

* **Descripción:** Esta expresión regular se utiliza para coincidir con la raíz del sitio (homepage).
* **Componentes:**

    * ```^```: Marca el inicio de la cadena.
    * ```\/```: Coincide con el carácter / (la barra inclinada). Se escapa con \ para indicar que es un carácter literal.
    * ```$```: Marca el final de la cadena.
  
* **Coincidencia:** Coincide únicamente con la URL ```/```. Por lo tanto, si un usuario accede a la raíz del dominio (por ejemplo, ```http://regex.test/```), esta expresión se activa y se ejecuta la función correspondiente.

2. ```/^\/productos\/(\d+)$/```
   
* **Descripción:** Esta expresión regular se utiliza para coincidir con las URLs que representan páginas de productos específicos.
* **Componentes:**

    * ```^```: Marca el inicio de la cadena.
    * ```\/```: Coincide con el carácter /.
    * ```productos```: Coincide con la palabra "productos".
    * ```\/```: Coincide con otro carácter /.
    * ```(\d+)```: Captura uno o más dígitos. \d representa cualquier dígito (equivalente a [0-9]) y el signo + indica que debe haber al menos uno o más dígitos.
    * ```$```: Marca el final de la cadena.
  
* Coincidencia: Coincide con URLs como /productos/45, donde 45 sería el ID del producto. El ID del producto se captura y se pasa como parámetro a la función asociada.

3. ```/^\/categorias\/(\w+)$/```

* **Descripción:** Esta expresión regular se utiliza para coincidir con las URLs que representan páginas de categorías específicas.

* **Componentes:**
  
    * ```^```: Marca el inicio de la cadena.
    * ```\/```: Coincide con el carácter ```/```.
    * ```categorias```: Coincide con la palabra "categorias".
    * ```\/```: Coincide con otro carácter ```/```.
    * ```(\w+)```: Captura uno o más caracteres de palabra. ```\w``` representa letras, números y guiones bajos, y el signo ```+``` indica que debe haber al menos uno o más caracteres.
    * ```$```: Marca el final de la cadena.
  
* **Coincidencia:** Coincide con URLs como ```/categorias/electronica```, donde ```electronica``` es el nombre de la categoría. El nombre de la categoría se captura y se pasa como parámetro a la función asociada.

3. ```/^\/contacto$/```

* **Descripción:** Esta expresión regular se utiliza para coincidir con la URL de la página de contacto.
* **Componentes:**

    * ```^```: Marca el inicio de la cadena.
    * ```\/```: Coincide con el carácter /.
    * ```contacto```: Coincide con la palabra "contacto".
    * ```$```: Marca el final de la cadena.

* **Coincidencia:** Coincide únicamente con la URL /contacto. Si un usuario accede a esta URL, la función asociada se ejecutará.

### Resumen:

El código implementa un sistema de routing básico en PHP que permite gestionar diferentes rutas utilizando expresiones regulares. Con la clase Router, se pueden registrar rutas, resolver la acción correspondiente a una URL solicitada y manejar errores de forma efectiva. 







