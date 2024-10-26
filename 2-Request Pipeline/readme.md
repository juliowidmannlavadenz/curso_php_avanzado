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
use Log;

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

use Closure;
use Log;

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

* **Explicación:** Ambos middlewares funcionan igual que en versiones anteriores, registrando la solicitud y respuesta. Laravel aplicará estos middlewares automáticamente al grupo ```web```, gracias a la configuración en ```http.php```.


### 5. Enrutamiento (Routing)
### 6. Crear el controlador
### 7. Generación de la respuesta

# Concepto de middleware
# Patrón de diseño pipeline
# Routing en php
# Expresiones regulares


