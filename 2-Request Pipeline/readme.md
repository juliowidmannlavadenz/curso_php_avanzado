# Request pipeline
## Concepto

Request Pipeline (o "pipeline de solicitudes") es el conjunto de pasos o procesos que una solicitud HTTP sigue desde que es recibida por el servidor hasta que se genera una respuesta y se envía de vuelta al cliente. 

## Componentes Clave del Request Pipeline
Presentaremos un ejemplo de cada componente clave del Request Pipeline en Laravel 11.

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

## Frameworks Web que usan Request Pipeline

### 1. ASP.NET Core: 
* Utiliza el middleware pipeline, donde cada solicitud HTTP atraviesa una serie de middlewares. Este flujo se configura en el archivo Startup.cs (o ahora, en .NET 6+, en el archivo de configuración inicial de la aplicación).

### 2. Laravel (PHP): 
* Laravel 11, entre otras versiones, tiene un Request Lifecycle que incluye middleware globales y específicos de ruta. Cada middleware procesa la solicitud antes de que llegue al controlador y, si es necesario, puede rechazarla o modificarla.

### 3. Express.js (Node.js): 
* En Express, cada solicitud pasa por una serie de middlewares definidos en el código, donde se puede manejar la autenticación, la validación de datos y otras funciones antes de pasar al controlador.

### 4. Django (Python): 
* Django utiliza una serie de middlewares que procesan la solicitud antes y después de que llegue a la vista. Estos middlewares se registran en la configuración (settings.py), y procesan o rechazan la solicitud según se requiera.

### 5. Ruby on Rails: 
* Rails implementa el pipeline mediante filtros y middlewares que se ejecutan antes de la llegada de la solicitud al controlador, permitiendo controlar el flujo de la solicitud.

### 6. Flask (Python): 
* Aunque es más minimalista, Flask permite definir middlewares que actúan como filtros para gestionar y modificar solicitudes y respuestas.

```php
require 'Router.php'; 

$router = new Router(); 
$router->route($_SERVER['REQUEST_URI']); 
```

# Concepto de middleware
# Patrón de diseño pipeline
# Routing en php
# Expresiones regulares


