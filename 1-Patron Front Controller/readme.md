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
En este ejemplo, gestionaremos las rutas de una aplicación web utilizando el patrón Front Controller.
Detallamos las principales características que se implementarán:

    * Recepción y redirección de solicitudes: El Front Controller actuará como punto de entrada único, recibiendo todas las solicitudes y redirigiéndolas a los controladores correspondientes según la URL.
    * Controladores basados en clases: Utilizaremos controladores orientados a objetos, lo que permite una estructura más modular y organizada, facilitando la mantenibilidad del código.
    * Manejo de excepciones: Implementaremos un manejo centralizado de excepciones, lo que garantiza una captura y gestión uniforme de errores en toda la aplicación.
    * Redirección de rutas: Se añadirá lógica de redirección para gestionar flujos de navegación, enviando a los usuarios a las rutas correctas según el contexto o el resultado de operaciones específicas.
    * Manejo de sesiones y autenticación básica: Integraremos un sistema básico de autenticación y gestión de sesiones para proteger las rutas sensibles y gestionar las sesiones de usuario de forma centralizada.
    * Vistas dinámicas: Los controladores generarán vistas dinámicas basadas en los datos procesados, personalizando la experiencia del usuario según las interacciones con la aplicación.

> Esta arquitectura proporcionará una mayor flexibilidad, modularidad y escalabilidad, mejorando tanto la organización interna del código como la experiencia del usuario.

### **1. Estructura del Proyecto:**

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

### **2. Archivo ```.htaccess``` (Opcional, para redirigir todas las solicitudes a index.php)**

```php
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.php?route=$1 [L,QSA]
```

Esto asegura que todas las solicitudes pasen a través de ```index.php```, utilizando route como parámetro para las rutas.

### **3. Archivo ```config/config.php```**

Este archivo contiene la configuración básica de la aplicación.

```php
<?php
return [
    'app_name' => 'Mi Aplicación',
    'base_url' => 'http://localhost/front-controller/',
    'database' => [
        'host' => 'localhost',
        'dbname' => 'mi_base_de_datos',
        'user' => 'root',
        'password' => ''
    ]
];
```

### **4. Archivo ```core/Router.php```**

Este es el núcleo del enrutamiento. Se encarga de recibir las rutas y encontrar el controlador adecuado.

```php
<?php

class Router
{
    private $routes = [];

    public function addRoute($path, $controller)
    {
        $this->routes[$path] = $controller;
    }

    public function dispatch($route)
    {
        if (array_key_exists($route, $this->routes)) {
            $controllerName = $this->routes[$route];
            $controller = new $controllerName();
            $controller->handle();
        } else {
            http_response_code(404);
            echo "404 - Página no encontrada";
        }
    }
}
```

### **5. Archivo ```core/BaseController.php```**

Este es el controlador base del que heredan los demás controladores. Aquí puedes definir métodos comunes como la carga de vistas y manejo de sesiones.

```php
<?php

class BaseController
{
    protected function render($view, $data = [])
    {
        extract($data);
        include __DIR__ . '/../views/' . $view . '.php';
    }

    protected function redirect($url)
    {
        header('Location: ' . $url);
        exit;
    }
}
```

### **6. Controlador ```controllers/HomeController.php```**

Este controlador maneja las solicitudes de la página de inicio.

```php
<?php

class HomeController extends BaseController
{
    public function handle()
    {
        $this->render('home', ['title' => 'Página de Inicio']);
    }
}
```
### **7. Controlador ```controllers/ProductController.php```**

Este controlador muestra una lista de productos.

```php
<?php

class ProductController extends BaseController
{
    public function handle()
    {
        $products = ['Producto 1', 'Producto 2', 'Producto 3'];
        $this->render('product', ['title' => 'Lista de Productos', 'products' => $products]);
    }
}
```

### **8. Controlador ```controllers/LoginController.php```**

Este controlador maneja la autenticación de usuarios.

```php
<?php
// controllers/LoginController.php

class LoginController extends BaseController {
    public function index() {
        $error = null;

        // Manejo del formulario de inicio de sesión
        if ($_SERVER['REQUEST_METHOD'] === 'POST') {
            $username = $_POST['username'];
            $password = $_POST['password'];

            // Autenticación
            if (User::authenticate($username, $password)) {
                $_SESSION['user'] = $username; // Guardar el usuario en la sesión
                header('Location: index.php?route=home'); // Redirigir a la página de inicio
                exit;
            } else {
                $error = 'Credenciales inválidas. Intenta de nuevo.';
            }
        }

        // Cargar la vista de inicio de sesión
        $this->render('login', ['error' => $error]);
    }
}
```

### **9. Modelo ```models/User.php```**

Este modelo se encarga de la autenticación de usuarios. Puede estar conectado a una base de datos.
* crearemos un modelo de usuario que contenga algunas credenciales de prueba.

```php
<?php
// models/User.php

class User {
    private static $users = [
        [
            'username' => 'admin',
            'password' => 'password123', // Contraseña en texto plano (solo para fines de ejemplo)
        ],
        [
            'username' => 'user',
            'password' => 'mypassword', // Otra cuenta de usuario
        ],
    ];

    public static function authenticate($username, $password) {
        foreach (self::$users as $user) {
            if ($user['username'] === $username && $user['password'] === $password) {
                return true; // Credenciales válidas
            }
        }
        return false; // Credenciales inválidas
    }
}
```

### **10. Archivo ```views/home.php```**

La vista de la página de inicio.

```php
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title><?php echo $title; ?></title>
</head>
<body>
    <h1><?php echo $title; ?></h1>
    <p>Bienvenido a la página de inicio.</p>
    <nav>
        <ul>
            <li><a href="index.php?route=home">Inicio</a></li>
            <li><a href="index.php?route=product">Productos</a></li>
            <li><a href="index.php?route=login">Iniciar Sesión</a></li>
        </ul>
    </nav>
</body>
</html>
```

### **11. Archivo ```views/product.php```**

La vista de la página de productos.

```php
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title><?php echo $title; ?></title>
</head>
<body>
    <h1><?php echo $title; ?></h1>
    <ul>
        <?php foreach ($products as $product): ?>
            <li><?php echo $product; ?></li>
        <?php endforeach; ?>
    </ul>
    <nav>
        <ul>
            <li><a href="index.php?route=home">Inicio</a></li>
            <li><a href="index.php?route=product">Productos</a></li>
            <li><a href="index.php?route=login">Iniciar Sesión</a></li>
        </ul>
    </nav>
</body>
</html>
```

### **12. Archivo ```views/login.php```**

La vista de la página de inicio de sesión.

```php
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Iniciar Sesión</title>
</head>
<body>
    <h1>Iniciar Sesión</h1>
    <?php if (isset($error)): ?>
        <p style="color: red;"><?php echo $error; ?></p>
    <?php endif; ?>
    <form method="POST">
        <label for="username">Usuario:</label>
        <input type="text" name="username" required><br>
        <label for="password">Contraseña:</label>
        <input type="password" name="password" required><br>
        <button type="submit">Iniciar Sesión</button>
    </form>
    <nav>
        <ul>
            <li><a href="index.php?route=home">Inicio</a></li>
            <li><a href="index.php?route=product">Productos</a></li>
            <li><a href="index.php?route=login">Iniciar Sesión</a></li>
        </ul>
    </nav>
</body>
</html>
```

### **13. Archivo ```index.php```**

El punto de entrada de la aplicación. Aquí se inicia la sesión, se carga la configuración y se despachan las rutas.

```php
<?php
session_start();

// Cargar configuración
$config = include __DIR__ . '/config/config.php';

// Autocargar clases
spl_autoload_register(function ($className) {
    $directories = ['controllers', 'core', 'models'];
    foreach ($directories as $dir) {
        $file = __DIR__ . '/' . $dir . '/' . $className . '.php';
        if (file_exists($file)) {
            include $file;
        }
    }
});

// Crear enrutador
$router = new Router();

// Definir rutas
$router->addRoute('home', 'HomeController');
$router->addRoute('product', 'ProductController');
$router->addRoute('login', 'LoginController');

// Obtener la ruta de la solicitud
$route = isset($_GET['route']) ? $_GET['route'] : 'home';

// Despachar la ruta
$router->dispatch($route);
```

## Petición y respuesta http
## Ciclo de vida de una petición http
## Relaciones entre clases




