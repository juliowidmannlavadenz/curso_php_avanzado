# Patron front controller
## Definición:
El Patrón Front Controller en PHP es un patrón de diseño arquitectónico que centraliza todas las peticiones entrantes a una única ubicación antes de que se distribuyan a los controladores específicos. En lugar de tener múltiples scripts que gestionen distintas partes de la aplicación, este patrón permite manejar todas las solicitudes en un solo punto de entrada **(como index.php)**, mejorando la organización y el control sobre las rutas y las acciones que se ejecutan.

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/fornt_controller.png?raw=true" alt="Imagen width="400" style />
</p>
<br>

## ¿Cómo funciona?

### **1. Único punto de entrada (Front Controller):**
El archivo index.php es el único punto de entrada para todas las peticiones. Aquí recibes todas las solicitudes HTTP y decides qué hacer con ellas.

```php
require 'Router.php'; 

$router = new Router(); 
$router->route($_SERVER['REQUEST_URI']); 
```
**Explicación:**
* Todas las peticiones que llegan a la aplicación pasan por index.php.
* En este archivo, cargamos el archivo Router.php, que se encargará de dirigir las solicitudes a los controladores correspondientes.

### **2. Enrutamiento:**
El enrutador (Router.php) se encarga de analizar la URL y determinar qué controlador y acción se deben ejecutar.

```php
class Router {
    private $routes = [
        '/' => 'HomeController@index',
        '/products' => 'ProductController@index',
        '/product/show' => 'ProductController@show'
    ];

    public function route($uri) {
        if (array_key_exists($uri, $this->routes)) {
            $action = $this->routes[$uri];
            $this->dispatch($action);
        } else {
            echo "404 - Página no encontrada";
        }
    }

    private function dispatch($action) {
        list($controller, $method) = explode('@', $action);
        $controller = new $controller();
        $controller->$method();
    }
}
```
**Explicación:**

* En este enrutador, definimos las rutas posibles en la aplicación.
* Se verifica la URL actual ($uri) y se despacha la solicitud al controlador y método apropiados.

### **3. Controladores:**
El controlador es quien maneja la lógica de negocio. Dependiendo de la acción solicitada, se ejecuta el controlador correspondiente.

```php
class HomeController {
    public function index() {
        echo "Bienvenido a la página de inicio";
    }
}

class ProductController {
    public function index() {
        echo "Lista de productos";
    }

    public function show() {
        echo "Detalles del producto";
    }
}
```

**Explicación:**

* Cuando el enrutador determina qué controlador y método ejecutar, delega la responsabilidad a ese controlador.
* En este caso, el controlador ProductController tiene dos métodos:

      - index (muestra la lista de productos).
      - show (muestra los detalles de un producto específico).

### **4. Modularidad:**
El patrón Front Controller permite centralizar la funcionalidad común, como manejo de errores, autenticación, validación, etc.

```php
require 'ErrorHandler.php'; // Manejador de errores
require 'Auth.php'; // Sistema de autenticación
require 'Router.php'; // Enrutador

$handler = new ErrorHandler();
set_error_handler([$handler, 'handle']);

$auth = new Auth();
if (!$auth->check()) {
    die("Acceso denegado. Debes iniciar sesión.");
}

$router = new Router();
$router->route($_SERVER['REQUEST_URI']);
```

**Explicación:**

* Antes de realizar el enrutamiento, se pueden agregar funcionalidades comunes, como manejo de errores o autenticación.
* En este ejemplo, se incluye un sistema de autenticación y manejo de errores, lo que centraliza estas funciones en el punto de entrada principal (index.php).

### **Resumen:**

> * El Front Controller centraliza todas las solicitudes en un único archivo, que luego decide a qué controlador y acción enviar la solicitud.
> * El enrutamiento es manejado por un componente que mapea las URLs a los controladores correspondientes.
> * Los controladores son responsables de la lógica de negocio y procesan la solicitud.
> * La modularidad permite centralizar funcionalidades comunes, como la autenticación y el manejo de errores, de forma eficiente.

### **Ejemplo completo de front controller**
A traves de este ejemplo manejaremos las rutas de una aplicación web. Usaremos el Front Controller para recibir las solicitudes y redirigirlas a los controladores adecuados según la URL. incluiremos características adicionales como controladores basados en clases, manejo de excepciones, redirección y una estructura más modular. Tambien vamos a agregar manejo de sesiones, autenticación básica y vistas dinámicas.

### **Estructura del Proyecto:**

    /front-controller
    /controllers
        HomeController.php
        ProductController.php
        LoginController.php
    /core
        Router.php
        BaseController.php
    /models
        User.php
    /views
        home.php
        product.php
        login.php
    /config
        config.php
    index.php
    .htaccess

## Petición y respuesta http
## Ciclo de vida de una petición http
## Relaciones entre clases




