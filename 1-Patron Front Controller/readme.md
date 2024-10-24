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
### Explicación:
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

### Explicación:

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

### Explicación:

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

### Explicación:

* Antes de realizar el enrutamiento, se pueden agregar funcionalidades comunes, como manejo de errores o autenticación.
* En este ejemplo, se incluye un sistema de autenticación y manejo de errores, lo que centraliza estas funciones en el punto de entrada principal (index.php).

### **Resumen:**

> * El Front Controller centraliza todas las solicitudes en un único archivo, que luego decide a qué controlador y acción enviar la solicitud.
> * El enrutamiento es manejado por un componente que mapea las URLs a los controladores correspondientes.
> * Los controladores son responsables de la lógica de negocio y procesan la solicitud.
> * La modularidad permite centralizar funcionalidades comunes, como la autenticación y el manejo de errores, de forma eficiente.

## **Ejemplo completo de front controller**
En este ejemplo, implementaremos el patron front controller incluiremos las siguientes caracteristicas:

* Controladores basados en clases: para organizar mejor las rutas y acciones.
* Manejo de excepciones: que permite gestionar errores de forma controlada.
* Redirección: para facilitar la navegación entre diferentes secciones.
* Manejo de sesiones y autenticación básica: para gestionar usuarios y mantener sesiones activas.
* Vistas dinámicas: para generar contenido adaptable y flexible.



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
### Explicación:

1. 'app_name' => 'Mi Aplicación':

* Define el nombre de la aplicación, que en este caso es "Mi Aplicación". Este valor podría ser usado en la interfaz de usuario o en mensajes internos de la aplicación.

2. 'base_url' => 'http://localhost/front-controller/':

* Especifica la URL base de la aplicación, que es http://localhost/front-controller/. Esta URL se usaría para generar enlaces internos o redirecciones dentro de la aplicación.
  
3. 'database' => [...]:

* Aquí se configuran los parámetros de conexión a la base de datos:

    * 'host' => 'localhost': El servidor de base de datos está en localhost, es decir, la misma máquina donde se ejecuta el código PHP.
    * 'dbname' => 'mi_base_de_datos': El nombre de la base de datos que se va a usar es "mi_base_de_datos".
    * 'user' => 'root': El nombre de usuario para conectarse a la base de datos es root, que es el usuario por defecto en muchos entornos de desarrollo locales.
    * 'password' => '': La contraseña para el usuario root está vacía, lo cual es común en entornos de desarrollo, pero no es seguro en producción.

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

### Explicación:

1. class Router:

* Declara una clase llamada Router que será usada para gestionar las rutas y enviar la solicitud al controlador adecuado.

2. private $routes = [];:

* Se define una propiedad privada $routes que es un array vacío al principio. Esta propiedad se utiliza para almacenar las rutas y sus controladores asociados. Cada ruta (URL) se asocia con un controlador que maneja la lógica para esa ruta.

3. public function addRoute($path, $controller):

* Este método público addRoute permite agregar rutas al router.
* $path es la ruta (por ejemplo, '/home' o '/about') y $controller es el nombre de la clase controladora que manejará esa ruta.
* La línea $this->routes[$path] = $controller; almacena el controlador en el array $routes, asociándolo con la ruta específica.

4. public function dispatch($route):
   
* Este método es el responsable de "despachar" una ruta, es decir, decidir qué controlador debe manejar la solicitud basada en la ruta que recibe.
* $route es la ruta que se está solicitando.
* if (array_key_exists($route, $this->routes)): Verifica si la ruta solicitada existe en el array $routes.

    * Si la ruta existe:
      
        * $controllerName = $this->routes[$route];: Obtiene el nombre del controlador asociado a esa ruta.
        * $controller = new $controllerName();: Crea una nueva instancia del controlador.
        * $controller->handle();: Llama al método handle del controlador, que sería el encargado de procesar la solicitud (este método debería estar definido en la clase del controlador).
          
    * Si la ruta no existe:
      
        * http_response_code(404);: Envía un código de estado HTTP 404, indicando que la página no fue encontrada.
        * echo "404 - Página no encontrada";: Muestra un mensaje indicando que la página no se encontró.
     
### Ejemplo de uso:

En nuestra aplicación de ejemplo tenemos las siguientes rutas:

* ```/home``` -> Controlador ```HomeController```
* ```/product``` -> Controlador ```ProductController```
* ```/login``` -> Controlador ```LoginController```

Usariamos la clase Router de la siguiente manera:

Archivo **```index.php```**
```php
$router = new Router();

$router->addRoute('home', 'HomeController');
$router->addRoute('product', 'ProductController');
$router->addRoute('login', 'LoginController');

$route = isset($_GET['route']) ? $_GET['route'] : 'home';

$router->dispatch($route);
```
Si la ruta solicitada es /home, el router creará una instancia de HomeController y llamará a su método handle. Si la ruta no existe, como /contact, el código devolverá un error 404.

### Vista en el navegador:

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/salida_controllers2.png?raw=true" alt="Imagen" style />
</p>
<br>


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

### Explicación:

1. class BaseController:
   
* Define una clase llamada BaseController, que actuará como una clase base para otros controladores. Otros controladores heredarían de esta clase y tendrían acceso a sus métodos.
  
2. Método render($view, $data = []):
   
Este método está diseñado para mostrar una vista, es decir, para cargar un archivo de vista que presente una interfaz al usuario.

* $view: Es el nombre del archivo de vista que deseas cargar. Se espera que esté ubicado en el directorio views, que probablemente contenga los archivos HTML o PHP para mostrar al usuario.
  
* $data = []: Este es un array opcional que contiene los datos que deseas pasar a la vista. Por defecto, si no se pasan datos, es un array vacío.
  
* extract($data): Esta función convierte los elementos del array $data en variables individuales. Por ejemplo, si $data contiene ['name' => 'John'], se creará una variable $name con el valor 'John'. Esto facilita el uso de los datos dentro de la vista.
  
* include __DIR__ . '/../views/' . $view . '.php';: Carga e incluye el archivo de vista. Utiliza __DIR__ para obtener el directorio actual donde está el script (en este caso, el controlador). Luego, navega al directorio views donde se espera encontrar la vista (por ejemplo, si $view = 'home', incluirá el archivo views/home.php).

### Ejemplo de uso del método ```render:```

```php
$this->render('home', ['name' => 'John']);
```

* Esto buscaría e incluiría el archivo ```views/home.php``` y dentro de esa vista se podría usar la variable ```$name```, que tendrá el valor ```'John'```.

3. Método redirect($url):
Este método se utiliza para redirigir a una URL específica, es decir, enviar al usuario a otra página.

* $url: Es la URL a la que se redirige al usuario.
* header('Location: ' . $url);: Envía una cabecera HTTP de redirección con la URL especificada. Esto indica al navegador que debe cargar una nueva página.
* exit;: Detiene la ejecución del script inmediatamente después de la redirección, asegurándose de que no se ejecute ningún código adicional tras la redirección.

### Ejemplo de uso del método ```redirect```

```php
$this->redirect('/home');
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

### Explicación:

1. Extensión de BaseController:

* La clase HomeController hereda de BaseController. Esto significa que puede utilizar todos los métodos definidos en BaseController, como el método render(), que se emplea para cargar vistas.

2. Método handle():

* Este método, llamado handle(), es responsable de gestionar la lógica para mostrar la vista de la página de inicio.

2.1. Renderización de la vista:

* Se llama al método render() de BaseController para cargar la vista correspondiente. En este caso, la vista es 'home'.
* Se pasa un array que contiene datos para la vista:
  
    * 'title' => 'Página de Inicio': Este par clave-valor establece el título de la página como "Página de Inicio". La clave 'title' se usará en la vista para mostrar este         texto, probablemente en una etiqueta <title> o como un encabezado en la página.

3. Flujo general.
   
Cuando se llama al método handle() de HomeController:

1. Se ejecuta el código dentro de handle(), que invoca el método render().
2. El método render() busca un archivo de vista llamado home.php dentro de la carpeta views/ y lo carga.
3. En la vista home.php, se puede utilizar la variable $title para mostrar el título de la página.


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
### Explicación:

* Aquí definimos una clase llamada ProductController que extiende de BaseController. El propósito de esta clase es manejar la lógica para mostrar una lista de productos y renderizar una vista que muestra esa lista. 

### **8. Controlador ```controllers/LoginController.php```**

Este controlador maneja la autenticación de usuarios.

```php
<?php

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

### Explicación:

1. Clase LoginController que extiende BaseController:

* La clase LoginController hereda de BaseController, lo que le da acceso a métodos como render(), que sirve para cargar vistas (páginas HTML).

2. Método index():

* El método index() es responsable de manejar la solicitud para el formulario de inicio de sesión.
* Se inicializa la variable $error como null, que almacenará cualquier mensaje de error que deba mostrarse al usuario si el inicio de sesión falla.

3. Manejo del formulario de inicio de sesión.

* Se verifica si el método de la solicitud es POST. Esto indica que el formulario de inicio de sesión ha sido enviado.
* Si es una solicitud POST, el código toma los valores ingresados en los campos username y password usando $_POST['username'] y $_POST['password'].

4. Autenticación.

* Se llama al método User::authenticate($username, $password) para comprobar si el usuario ha ingresado credenciales válidas.
  
    * Si las credenciales son correctas:
      
        * Se almacena el nombre de usuario en la sesión ($_SESSION['user'] = $username), lo que permite al sistema recordar que el usuario está autenticado.
        * El navegador redirige al usuario a la página de inicio (home) con header('Location: index.php?route=home');.
        * Se usa exit; para detener la ejecución del script después de la redirección.
          
    * Si las credenciales son incorrectas:
      
        * Se establece un mensaje de error: Credenciales inválidas. Intenta de nuevo. que se mostrará en la página de inicio de sesión.

5. Renderización de la vista de inicio de sesión.

* Finalmente, se carga la vista de inicio de sesión utilizando el método render() heredado de BaseController.
* La vista login.php recibe un array con la variable $error para mostrar el mensaje de error si lo hay.


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

### Explicación:

1. Definición de la clase User.

* Esta es la clase que se encarga de gestionar la autenticación de usuarios en la aplicación.

2. Propiedad estática $users.

* La clase tiene una propiedad estática llamada $users, que es un array que contiene la lista de usuarios de la aplicación.
* Cada usuario está representado por un array asociativo con un username y una password.
* Las contraseñas están guardadas en texto plano, lo cual es inseguro en un entorno real. En una aplicación real, las contraseñas deben estar hasheadas (por ejemplo, utilizando password_hash()).

3. Método estático authenticate().

* Este método es responsable de verificar si las credenciales proporcionadas son válidas.
* Es un método estático, lo que significa que puede ser llamado sin instanciar la clase User (por ejemplo, User::authenticate()).


### **10. Archivo ```views/home.php```**

La vista de la página de inicio. Creamos una plantilla en HTML con partes dinámicas para renderizar contenido, en este caso el título de la página. 

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

### Explicación:

* Los enlaces usan la estructura ```index.php?route=...``` para navegar entre las diferentes rutas de la aplicación.

    * Inicio: Redirige a la ruta home.
    * Productos: Redirige a la ruta product.
    * Iniciar Sesión: Redirige a la ruta login.

Estas rutas son procesadas por el controlador (index.php) y despachadas para mostrar la vista correspondiente.

### Vista en el navegador:

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/inicio2.png?raw=true" alt="Imagen" style />
</p>
<br>

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

### Explicación:

* (<?php foreach...): Aquí utilizamos un bucle foreach para recorrer la lista de productos almacenada en la variable $products, que es pasada desde el controlador. Por cada elemento de $products, se genera un <li> (elemento de lista) que contiene el nombre del producto.
  
    * $products: Es un array de productos que contiene los nombres (por ejemplo, ['Producto 1', 'Producto 2', 'Producto 3']).
    * <li><?php echo $product; ?></li>: Cada nombre de producto se inserta dentro de un <li>, que es el formato estándar para listas en HTML.


### Vista en el navegador:

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/lista_productos.png?raw=true" alt="Imagen" style />
</p>
<br>

### **12. Archivo ```views/login.php```**

Generamos una plantilla para una página de inicio de sesión, en ella manejamos la lógica del formulario, incluyendo la visualización de mensajes de error si las credenciales no son válidas. 

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

### Vista en el navegador:

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/login_vista.png?raw=true" alt="Imagen" style />
</p>
<br>

### **13. Archivo ```index.php```**

El punto de entrada de la aplicación. Aquí se inicia la sesión, se carga la configuración y se despachan las rutas.

```php
<?php
session_start();

$config = include __DIR__ . '/config/config.php';

spl_autoload_register(function ($className) {
    $directories = ['controllers', 'core', 'models'];
    foreach ($directories as $dir) {
        $file = __DIR__ . '/' . $dir . '/' . $className . '.php';
        if (file_exists($file)) {
            include $file;
        }
    }
});

$router = new Router();

$router->addRoute('home', 'HomeController');
$router->addRoute('product', 'ProductController');
$router->addRoute('login', 'LoginController');

$route = isset($_GET['route']) ? $_GET['route'] : 'home';

$router->dispatch($route);
```

### Explicación:

1. Inicio de sesión con session_start():

* session_start(): Este comando inicia o reanuda una sesión en PHP. Permite que el servidor almacene y acceda a variables de sesión, que son útiles para guardar información del usuario mientras navega por el sitio, como si el usuario ha iniciado sesión.

2. Cargar configuración.

* include: Incluye el archivo de configuración (config.php) que está en la carpeta config.
* $config: Aquí se guarda el contenido del archivo de configuración, que puede contener datos importantes como la configuración de la base de datos, rutas, y otros parámetros.

3. Autocargar clases.

* spl_autoload_register(): Registra una función de autocarga que se ejecuta automáticamente cada vez que se intenta usar una clase que aún no ha sido incluida.

* Autocarga de clases:

    * La función anónima que recibe el nombre de la clase ($className) busca archivos que coincidan con ese nombre en las carpetas controllers, core y models.
    * foreach ($directories as $dir): Recorre estas tres carpetas para encontrar el archivo de la clase que necesita incluirse.
    * $file: Construye la ruta del archivo donde podría estar definida la clase ($className).
    * file_exists(): Si el archivo existe, lo incluye automáticamente usando include.

Esto permite que la aplicación cargue automáticamente las clases cuando se necesiten sin tener que incluir manualmente cada archivo.

4. Instanciar el enrutador.

* Crea una nueva instancia de la clase Router, que será utilizada para manejar las rutas de la aplicación.

5. Agregar rutas al enrutador:

* addRoute(): Método del enrutador que asocia una ruta con un controlador específico.
* Cada ruta (como 'home', 'product', y 'login') se asigna a un controlador respectivo (HomeController, ProductController, LoginController).

    * 'home' => 'HomeController': Cuando el usuario accede a la ruta home, el enrutador llamará a la clase HomeController.
    * 'product' => 'ProductController': Si accede a product, llamará a ProductController.

6. Determinar la ruta solicitada.

* $_GET['route']: Captura el valor del parámetro route de la URL. Por ejemplo, si la URL es index.php?route=product, entonces $_GET['route'] será 'product'.
* Ternario: Si no se especifica ninguna ruta en la URL (por ejemplo, si solo se accede a index.php), se asigna por defecto 'home' a la variable $route.

    * Si existe un valor en $_GET['route'], se usa ese valor, si no, el valor por defecto será 'home'.

7. Desplegar la ruta correspondiente.

* dispatch($route): El enrutador toma la ruta que se ha solicitado y busca el controlador asociado.
  
    * Si la ruta existe, el enrutador crea una instancia del controlador correspondiente y llama al método handle() o el método que maneje la solicitud.
    * Si la ruta no existe, el enrutador probablemente devolverá un error 404, que indica que la página no fue encontrada.

### Resumen del flujo:

1. **Iniciar sesión:** Se inicia la sesión con session_start(), lo que permite utilizar variables de sesión, como las relacionadas con la autenticación.
2. **Cargar configuración:** Se carga el archivo de configuración de la aplicación, lo que puede incluir detalles como la conexión a la base de datos o ajustes generales.
3. **Autocargar clases:** Se configura la autocarga de clases para que, cuando se necesite una clase, la aplicación busque automáticamente en los directorios controllers, core, y models.
4. **Instanciar el enrutador:** Se crea un nuevo objeto de la clase Router, que manejará las rutas.
5. **Definir rutas:** Se definen las rutas que la aplicación puede manejar, asociándolas con controladores.
6. **Determinar la ruta:** Se verifica qué ruta ha sido solicitada por el usuario a través de la URL.
7. **Desplegar la ruta:** Se ejecuta la ruta correspondiente, llamando al controlador adecuado.

### **Explicacion del ejemplo:**

1. Autocarga de Clases: Usamos spl_autoload_register() para cargar automáticamente las clases desde diferentes directorios (controllers, core, models).
2. Router: El enrutador es responsable de encontrar el controlador adecuado según la ruta proporcionada.
3. Controladores Base: Los controladores heredan de BaseController, lo que permite tener métodos comunes como render() para cargar vistas y redirect() para redirigir a otra ruta.
4. Autenticación: El LoginController maneja la autenticación de usuarios, y el modelo User se encarga de validar las credenciales.
5. Manejo de Sesiones: Usamos sesiones para almacenar el estado de autenticación del usuario.

### **Resumen:**

> En este ejemplo completo y modular del Patrón Front Controller incluimos los siguientes aspectos:
> * Enrutamiento: Permite que las solicitudes sean redirigidas a los controladores adecuados en función de la URL, lo que centraliza el manejo de las rutas y evita tener múltiples puntos de entrada en la aplicación.
> * Controladores basados en clases: Cada controlador es una clase que agrupa la lógica relacionada con una funcionalidad específica, facilitando el mantenimiento y la escalabilidad del código.
> * Manejo de sesiones: Las sesiones permiten mantener el estado de los usuarios entre las solicitudes, necesario para funcionalidades como el inicio de sesión o la personalización de contenido.
> * Autenticación básica: Se incluye una verificación de acceso que asegura que solo los usuarios autenticados puedan acceder a ciertas partes de la aplicación, añadiendo una capa básica de seguridad.

# Petición y respuesta http
## Definición de petición:

Una petición HTTP es una solicitud que un cliente (generalmente un navegador web) envía al servidor para obtener datos o realizar alguna acción. Esta petición puede ser de diferentes tipos: ```GET```, ```POST```, ```PUT```, ```DELETE```, entre otros, y puede incluir parámetros en la URL o en el cuerpo de la petición.

### Tipos de peticiones HHTP:

| Tipo de Petición | Propósito                                           | Ejemplo de Uso                                                                 |
|------------------|----------------------------------------------------|--------------------------------------------------------------------------------|
| **GET**          | Obtener datos de un servidor.                      | `GET /usuarios` → Recupera la lista de usuarios.                               |
| **POST**         | Enviar datos al servidor para crear un recurso.    | `POST /usuarios` → Crea un nuevo usuario enviando datos en el cuerpo de la petición. |
| **PUT**          | Actualizar completamente un recurso existente.     | `PUT /usuarios/1` → Actualiza los datos del usuario con ID 1.                  |
| **DELETE**       | Eliminar un recurso del servidor.                  | `DELETE /usuarios/1` → Elimina el usuario con ID 1.                            |

### Petición GET:
Se utiliza para solicitar datos sin modificar nada.

### Ejemplo de uso:

Archivo ```get/script.php```
```php
<?php
echo "Hola, " . htmlspecialchars($_GET["nombre"]);
?>
```

Si accedemos a este script con una URL como ```http://localhost:3000/script.php?nombre=Yamir Ramirez```, el resultado será:

### Vista en el navegador:


<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/get.jpg?raw=true" alt="Imagen" style />
</p>

### Petición POST:
Sirve para enviar información que será procesada, por ejemplo, para crear un nuevo recurso.

### Ejemplo de uso:

Archivo ```post/script.php```
```php
<?php
echo "Hola, " . htmlspecialchars($_POST["nombre"]);
?>
```

Este código procesa un formulario enviado mediante el método POST y muestra el valor del campo nombre. Por ejemplo, si un formulario envía el valor "Julio", el resultado será:

```php
    Hola, Julio
```

### **Simular una petición POST con la extensión Thunder Client**

### **1. Instalar Thunder Client:**

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/thunder_client2.png?raw=true" alt="Imagen" style />
</p>
<br>

### **2. Abrir y crear una petición en Thunder Client:**

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/abrir_thunder_client.png?raw=true" alt="Imagen" style />
</p>
<br>

### **3. Enviar una Petición POST:**

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/peticion_post2.png?raw=true" alt="Imagen" style />
</p>
<br>

### Petición PUT:
Reemplaza completamente un recurso existente con los datos enviados.

### Ejemplo de uso:

Archivo ```put/script.php```
```php
<?php
$data = file_get_contents("php://input");

$parsedData = json_decode($data, true);

if ($parsedData !== null && isset($parsedData["nombre"])) {
    echo "Actualizando a: " . htmlspecialchars($parsedData["nombre"]);
} else {
    echo "No se envió el campo 'nombre' o el JSON no es válido.";
}
?>
```

Este código captura los datos enviados con una petición PUT (por ejemplo, un campo nombre) y los muestra en pantalla, simulando la actualización de un recurso.

### Enviar una Petición PUT:

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/peticion_put.png?raw=true" alt="Imagen" style />
</p>
<br>

### DELETE:
Elimina un recurso del servidor.

### Ejemplo de uso:

Archivo ```delete/script.php```
```php
<?php
if (isset($_GET["id"])) {
    echo "Eliminando recurso con ID: " . htmlspecialchars($_GET["id"]);
} else {
    echo "No se envió el campo 'id'.";
}
?>
```
Este código procesa una petición DELETE y obtiene un parámetro id que se envía como parametro en la URL.

### Enviando datos DELETE en la URL (Query Params):

**URL con parámetros:**

```php
    http://localhost:3000/script.php?id=123
```

### Vista en el navegador:

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/delete_query_params.png?raw=true" alt="Imagen" style />
</p>
<br>

### Definición de respuesta http:

Es el mensaje que el servidor web envía de vuelta al cliente (normalmente un navegador o una aplicación) en respuesta a una solicitud HTTP. Este mensaje incluye:

### 1. Código de estado HTTP: 

Indica si la solicitud fue exitosa (por ejemplo, 200 OK) o si ocurrió algún error (por ejemplo, 404 Not Found, 500 Internal Server Error).

### Códigos de estado HTTP más comunes:

    
| **Código**  |               **Categoría**               |                           **Descripción**                           |
|-------------|-------------------------------------------|---------------------------------------------------------------------|
| 200         |        Éxito (2xx)                        |                    OK - Solicitud exitosa                           |
| 201         |        Éxito (2xx)                        |                    Created - Recurso creado exitosamente            |
| 204         |        Éxito (2xx)                        |                    No Content - Sin contenido en la respuesta        |
| 301         |        Redirección (3xx)                  |                    Moved Permanently - Recurso movido               |
| 302         |        Redirección (3xx)                  |                    Found - Recurso temporalmente movido             |
| 304         |        Redirección (3xx)                  |                    Not Modified - No se ha modificado               |
| 400         |        Error del cliente (4xx)            |                    Bad Request - Solicitud incorrecta               |
| 401         |        Error del cliente (4xx)            |                    Unauthorized - No autorizado                     |
| 403         |        Error del cliente (4xx)            |                    Forbidden - Prohibido                            |
| 404         |        Error del cliente (4xx)            |                    Not Found - Recurso no encontrado                |
| 500         |        Error del servidor (5xx)           |                    Internal Server Error - Error interno            |
| 502         |        Error del servidor (5xx)           |                    Bad Gateway - Pasarela incorrecta                |
| 503         |        Error del servidor (5xx)           |                    Service Unavailable - Servicio no disponible      |
| 504         |        Error del servidor (5xx)           |                    Gateway Timeout - Tiempo de espera agotado       |

### 2. Encabezados: 

Metadatos sobre la respuesta, como el tipo de contenido ```(Content-Type)```, la longitud del contenido ```(Content-Length)```, políticas de caché ```(Cache-Control)```, entre otros.

### Principales encabezados HTTP:

| **Encabezado**          | **Descripción**                                                                 |
|-------------------------|---------------------------------------------------------------------------------|
| **Host**                | Especifica el nombre del servidor (dominio) y, opcionalmente, el número de puerto.|
| **User-Agent**          | Información sobre el cliente (navegador o aplicación) que realiza la solicitud.  |
| **Accept**              | Indica qué tipos de contenido el cliente es capaz de procesar (por ejemplo, `text/html`, `application/json`).|
| **Accept-Language**     | Especifica los idiomas preferidos del cliente.                                   |
| **Accept-Encoding**     | Informa al servidor de los tipos de codificación que el cliente puede manejar (como `gzip`, `deflate`).|
| **Authorization**       | Envía credenciales de autenticación para acceder a un recurso protegido.         |
| **Content-Type**        | Especifica el tipo de contenido del cuerpo de la solicitud o respuesta (por ejemplo, `application/json`).|
| **Content-Length**      | Indica la longitud del cuerpo del mensaje en bytes.                              |
| **Cache-Control**       | Controla cómo se debe almacenar en caché la respuesta, tanto en el cliente como en el servidor.|
| **Cookie**              | Envía cookies del cliente al servidor.                                           |
| **Set-Cookie**          | Instruye al cliente a almacenar cookies.                                         |
| **Referer**             | Especifica la URL de la que proviene la solicitud.                               |
| **Location**            | Se utiliza en redireccionamientos para especificar la nueva ubicación de un recurso.|
| **Connection**          | Controla si la conexión debe mantenerse abierta o cerrarse después de completar la solicitud.|
| **X-Forwarded-For**     | Identifica la dirección IP del cliente cuando hay servidores intermedios o proxies.|

### 3. Cuerpo: 

El contenido real de la respuesta, que puede ser una página HTML, un archivo JSON, un archivo binario o cualquier otro tipo de recurso solicitado.

### Ejemplo de respuesta HTTP con una página HTML simple:

```php
   HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8
Content-Length: 138

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Ejemplo de Respuesta HTTP</title>
</head>
<body>
    <h1>Hola, Mundo!</h1>
    <p>Esta es una respuesta HTTP con contenido HTML.</p>
</body>
</html>
```
### En este ejemplo:

* La línea ```HTTP/1.1 200 OK``` indica que la solicitud fue exitosa.
* Los encabezados ```Content-Type``` y ```Content-Length``` especifican que el contenido es de tipo ```text/html``` y tiene una longitud de 138 bytes.
* El cuerpo de la respuesta es un pequeño documento HTML que muestra un saludo "Hola, Mundo!" en una página web.

### Ejemplo de respuesta HTTP que incluye un archivo JSON:

```php
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 60

{
    "mensaje": "Hola, Mundo!",
    "status": "exitoso",
    "data": {
        "id": 1,
        "nombre": "Ejemplo"
    }
}
```

### En este ejemplo:

* La línea ```HTTP/1.1 200 OK``` indica que la solicitud fue exitosa.
* El encabezado ```Content-Type``` especifica que el contenido es de tipo ```application/json```.
* El cuerpo de la respuesta es un objeto JSON que incluye un mensaje, un estado y un objeto de datos con un ```id``` y un ```nombre```.


### Ejemplo completo de petición y respuesta http

En este ejemplo, implementaremos un sistema de blog que maneje diversas peticiones HTTP (```GET```, ```POST```, ```PUT```, ```DELETE```). Utilizaremos GET para recuperar y mostrar entradas, y POST para permitir a los usuarios crear nuevas entradas enviando datos como título y contenido.

* Para interactuar con el servidor, emplearemos cURL, facilitando el envío de estas peticiones desde el cliente.
* La petición PUT permitirá a los autores actualizar entradas existentes.
* DELETE se encargará de eliminar entradas del blog.

### 1. Estructura de directorios y archivos:

    /blog
    │
    ├── index.php
    ├── api
    │   ├── BlogController.php
    │   ├── db.php
    │   └── routes.php
    └── .htaccess

### 2. Archivo .htaccess para reescritura de URL (opcional):

```php
RewriteEngine On
RewriteRule ^api/(.*)$ api/routes.php?request=$1 [QSA,L]
```
Este archivo lo utilizamos para redirigir las peticiones a ```api/routes.php```, permitiendo manejar las rutas de forma más limpia.

### 3. Archivo ```index.php``` (punto de entrada)

```php
<?php
require_once 'api/routes.php';
```

### 4. Archivo ```api/db.php``` (conexión a la base de datos)

```php
<?php

function getDbConnection() {
    $host = 'localhost';
    $db = 'blog_db';
    $user = 'root';
    $pass = ''; // Cambia esto según tu configuración

    try {
        $pdo = new PDO("mysql:host=$host;dbname=$db", $user, $pass);
        $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
        return $pdo;
    } catch (PDOException $e) {
        echo 'Connection failed: ' . $e->getMessage();
        exit;
    }
}
```

### 5. Archivo ```api/BlogController.php``` (controlador del blog)

```php
<?php
// api/BlogController.php

require_once 'db.php';

class BlogController {

    // Obtener todas las entradas
    public function getPosts() {
        $db = getDbConnection();
        $stmt = $db->query("SELECT * FROM posts");
        $posts = $stmt->fetchAll(PDO::FETCH_ASSOC);
        header('Content-Type: application/json');
        echo json_encode($posts);
    }

    // Crear una nueva entrada
    public function createPost($data) {
        $db = getDbConnection();
        $stmt = $db->prepare("INSERT INTO posts (title, content) VALUES (:title, :content)");
        $stmt->execute(['title' => $data['title'], 'content' => $data['content']]);
        header('Content-Type: application/json');
        echo json_encode(['success' => true, 'id' => $db->lastInsertId()]);
    }

    // Actualizar una entrada
    public function updatePost($id, $data) {
        $db = getDbConnection();
        $stmt = $db->prepare("UPDATE posts SET title = :title, content = :content WHERE id = :id");
        $stmt->execute(['title' => $data['title'], 'content' => $data['content'], 'id' => $id]);
        header('Content-Type: application/json');
        echo json_encode(['success' => true]);
    }

    // Eliminar una entrada
    public function deletePost($id) {
        $db = getDbConnection();
        $stmt = $db->prepare("DELETE FROM posts WHERE id = :id");
        $stmt->execute(['id' => $id]);
        header('Content-Type: application/json');
        echo json_encode(['success' => true]);
    }
}
```

### 6. Archivo ```api/routes.php``` (enrutamiento)

```php
<?php

require_once 'BlogController.php';

$request = $_GET['request'] ?? '';
$controller = new BlogController();

switch ($_SERVER['REQUEST_METHOD']) {
    case 'GET':
        if ($request === 'posts') {
            $controller->getPosts();
        } else {
            http_response_code(404);
            echo json_encode(['error' => 'Not Found']);
        }
        break;

    case 'POST':
        if ($request === 'posts') {
            $data = json_decode(file_get_contents('php://input'), true);
            $controller->createPost($data);
        } else {
            http_response_code(404);
            echo json_encode(['error' => 'Not Found']);
        }
        break;

    case 'PUT':
        $id = explode('/', $request)[1] ?? null;
        if ($id) {
            $data = json_decode(file_get_contents('php://input'), true);
            $controller->updatePost($id, $data);
        } else {
            http_response_code(404);
            echo json_encode(['error' => 'Not Found']);
        }
        break;

    case 'DELETE':
        $id = explode('/', $request)[1] ?? null;
        if ($id) {
            $controller->deletePost($id);
        } else {
            http_response_code(404);
            echo json_encode(['error' => 'Not Found']);
        }
        break;

    default:
        http_response_code(405);
        echo json_encode(['error' => 'Method Not Allowed']);
}
```

### 7. Configuración de la base de datos

Crearemos una base de datos llamada ```blog_db``` y una tabla llamada ```posts``` con la siguiente estructura:

```php
CREATE DATABASE IF NOT EXISTS blog_db;

USE blog_db;

CREATE TABLE posts (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    content TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMPs
);
```

Este script primero crea la base de datos blog_db si no existe, luego selecciona esa base de datos con USE, y finalmente crea la tabla posts.

## Ejemplos de uso
Para utilizar los comandos de curl utilizaremos el intérprete de comandos Bash (Bourne Again Shell). Si no tienes instalado descargalo junto con Git desde el siguiente enlace (Windows): https://git-scm.com/downloads/win

### 1. Crear una entrada (POST)

```bash```

```php
curl -X POST http://localhost/blog/api/posts -H "Content-Type: application/json" -d '{"title": "Mi primera entrada", "content": "Contenido de la entrada."}'
```

### Explicación:

1. curl: Esta es la herramienta utilizada para transferir datos con URLs. Permite realizar diferentes tipos de solicitudes HTTP (GET, POST, PUT, DELETE, etc.).

2. -X POST: Este parámetro especifica el tipo de solicitud HTTP que se está realizando. En este caso, se está enviando una solicitud POST, que generalmente se usa para enviar datos al servidor (por ejemplo, para crear un nuevo recurso).

3. http://localhost/blog/api/posts: Esta es la URL a la que se está enviando la solicitud. En este caso, es un endpoint de la API que se ejecuta en localhost (es decir, en el mismo ordenador donde se está ejecutando el comando) y está destinado a manejar las publicaciones en un blog.

4. -H "Content-Type: application/json": Este parámetro establece un encabezado HTTP que indica al servidor el tipo de contenido que se está enviando. Aquí, se especifica que los datos se están enviando en formato JSON.

5. -d '{"title": "Mi primera entrada", "content": "Contenido de la entrada."}': Este parámetro se utiliza para enviar los datos que se incluirán en la solicitud POST. Los datos se envían en formato JSON y contienen dos campos:

    * "title": El título de la entrada del blog, que en este caso es "Mi primera entrada".
    * "content": El contenido de la entrada del blog, que es "Contenido de la entrada."

### intérprete bash:

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/peticion_post_bash.png?raw=true" alt="Imagen" style />
</p>
<br>

### Registro en la tabla post:

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/post_mysql.png?raw=true" alt="Imagen" style />
</p>
<br>

### 2. Obtener todas las entradas (GET)

```bash```

```php
curl http://localhost/blog/api/posts
```

### Explicación:

Al ejecutar este comando, cURL envía una solicitud HTTP GET (que es el método predeterminado si no se especifica otro) a la URL proporcionada. Listamos todas las entradas del blog.

### intérprete bash:

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/get_lista_entradas.png?raw=true" alt="Imagen" style />
</p>
<br>

### Lista de todas las entradas de la tabla post:

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/get_lista_mysql.png?raw=true" alt="Imagen" style />
</p>
<br>

### 3. Actualizar una entrada (PUT)

```bash```

```php
curl -X PUT http://localhost/blog/api/posts/1 -H "Content-Type: application/json" -d '{"title": "Entrada actualizada", "content": "Contenido actualizado."}'
```

### Explicación:

Al ejecutar este comando, cURL realiza una solicitud PUT para actualizar la publicación con el ID 1 en el servidor, cambiando tanto su título como su contenido a los valores proporcionados en el JSON.

### intérprete bash:

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/put_actualizar.png?raw=true" alt="Imagen" style />
</p>
<br>

### Actualizando la entrada (1) de la tabla post:

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/put_actualizar_mysql.png?raw=true" alt="Imagen" style />
</p>
<br>

### 4. Eliminar una entrada (DELETE)

```bash```

```php
curl -X DELETE http://localhost/blog/api/posts/1
```

### Explicación:

Este comando de cURL envía una solicitud ```DELETE``` para eliminar una publicación del blog con el ID ```3``` en el servidor localizado en ```localhost```.

### intérprete bash:

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/delete1.png?raw=true" alt="Imagen" style />
</p>
<br>

### Borrando la entrada (3) de la tabla post:

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/delete2.png?raw=true" alt="Imagen" style />
</p>
<br>

### Explicacion del ejemplo:

1. **Estructura:** Hemos creado un sistema que sigue el patrón de diseño Front Controller y permite manejar diferentes tipos de peticiones HTTP a través de un único punto de entrada (index.php).
2. **Controlador:** BlogController.php maneja las operaciones del blog, como obtener, crear, actualizar y eliminar entradas.
3. **Conexión a la base de datos:** La conexión se establece en db.php, y utilizamos PDO para la interacción con la base de datos.
4. **Enrutamiento:** routes.php se encarga de redirigir las peticiones a los métodos correspondientes en el controlador.
5. **Métodos HTTP:** Cada operación (crear, leer, actualizar, eliminar) utiliza el método HTTP correspondiente (POST, GET, PUT, DELETE).

### Resumen:
> Este ejemplo proporciona una base para construir un blog simple y escalable:
> * Comienza con las funciones básicas de CRUD (Crear, Leer, Actualizar, Eliminar) usando los métodos HTTP (GET, POST, PUT, DELETE).
> * Luego, puedes mejorar el sistema con autenticación de usuarios, validaciones del lado del servidor, y paginación para manejar grandes volúmenes de entradas.
> * Otras mejoras incluyen un sistema de comentarios, búsqueda avanzada y soporte para categorías o etiquetas.
> * La estructura es flexible, permitiendo añadir funcionalidades como SEO para facilitar el crecimiento del proyecto.

# Ciclo de vida de una petición http

Es el  proceso que ocurre desde que un usuario hace una solicitud a un servidor web hasta que el servidor responde con los datos solicitados.

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/ciclo_vida_php3.png?raw=true" alt="Imagen" style />
</p>
<br>

### Pasos principles del ciclo:
### 1. Solicitud del cliente (Cliente HTTP)

El ciclo de vida comienza cuando el cliente (generalmente un navegador web o una aplicación) envía una solicitud HTTP a un servidor web. La solicitud contiene información como:

* La URL del recurso solicitado.
* El método HTTP (```GET```, ```POST```, ```PUT```, ```DELETE```, etc.).
* Cabeceras HTTP que contienen metadatos (cookies, tipo de contenido, agente de usuario, etc.).
* Datos en el cuerpo de la solicitud (principalmente en métodos ```POST``` o ```PUT```).

### 2. Recepción de la solicitud por el servidor web

El servidor web (como Apache, Nginx o IIS) recibe la solicitud HTTP. Según la configuración del servidor y la URL solicitada, el servidor determina qué recurso debe procesar la solicitud. Si la solicitud es para un archivo PHP, se delega el manejo de la petición al intérprete de PHP.

### 3. Interprete PHP

Una vez que el servidor web decide que la solicitud corresponde a un script PHP:

* El intérprete de PHP se encarga de procesar el script.
* Durante este paso, PHP puede realizar diferentes acciones, como acceder a bases de datos, leer o escribir archivos, o realizar cálculos.
* También puede trabajar con los datos de la solicitud (por ejemplo, formularios enviados en POST o parámetros de URL en GET).

### 4. Ejecución del código PHP

* El archivo PHP es interpretado línea por línea.
* Las variables de entorno como $_GET, $_POST, $_FILES, $_COOKIE se llenan con la información recibida en la solicitud.
* Cualquier lógica de negocio implementada en el código (validaciones, autenticaciones, consultas a bases de datos, etc.) se ejecuta.
* Se genera una respuesta en forma de HTML (o JSON, XML, etc.) que será devuelta al cliente.

### 5. Respuesta del servidor (Respuesta HTTP)

Una vez que PHP ha procesado la solicitud, devuelve los resultados al servidor web.

* La respuesta incluye un código de estado HTTP (200 para una solicitud exitosa, 404 si no se encontró el recurso, 500 si hubo un error en el servidor, etc.).
* También puede incluir cabeceras HTTP adicionales (tipo de contenido, cookies, longitud del contenido, etc.).
* Finalmente, el cuerpo de la respuesta contiene el contenido generado por PHP (por ejemplo, HTML, un archivo, o datos en formato JSON).

### 6. Envío de la respuesta al cliente

El servidor web toma la respuesta generada por PHP, le añade sus propias cabeceras HTTP si es necesario, y envía la respuesta de vuelta al cliente.

### 7. Interpretación de la respuesta por el cliente

Una vez que el cliente (como el navegador) recibe la respuesta:

* Si es un documento HTML, el navegador lo renderiza para mostrarlo en la pantalla.
* Si es un archivo JSON o XML, la aplicación cliente puede procesar esos datos.
* El ciclo de vida termina aquí, aunque el cliente podría iniciar una nueva petición, lo que iniciaría un nuevo ciclo.

## Ejemplo completo del ciclo de vida HTTP (PHP - Apache)

Crearemos una aplicación de ejemplo que gestione usuarios la cual nos permitira: 

* Ver una lista de usuarios.
* Añadir un nuevo usuario a través de un formulario.
* Eliminar usuarios de la lista.

Involucraremos tambien interacciones con la base de datos y el manejo de formularios, todo manejado dentro del ciclo de vida HTTP en PHP.

### 1. Estructura de directorios y archivos

```php
/mi-sitio/
    index.php
    add_user.php
    delete_user.php
    db.php
    templates/
        header.php
        footer.php
```

### 2. Creación de la base de datos

Crearemos  la base de datos ```ejemplo_php``` y la tabla ```usuarios``` (si no están creadas):

```php
CREATE DATABASE IF NOT EXISTS ejemplo_php;

USE ejemplo_php;

CREATE TABLE IF NOT EXISTS usuarios (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL
);
```

### 3. Archivo ```db.php``` (Conexión a la base de datos)

El archivo ```db.php``` maneja la conexión a la base de datos:

```php
<?php
// db.php

$host = 'localhost';  
$db   = 'ejemplo_php'; // Nombre de la base de datos
$user = 'root';       // Usuario MySQL (ajustar según tu configuración)
$pass = '';           // Contraseña MySQL (ajustar según tu configuración)

try {
    // Conexión a la base de datos usando PDO
    $pdo = new PDO("mysql:host=$host;dbname=$db", $user, $pass);
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
} catch (PDOException $e) {
    die("Error al conectar a la base de datos: " . $e->getMessage());
}
?>
```

### 4. Archivo ```header.php``` (Encabezado de la página)

Definimos un menú de navegación para mejorar la experiencia de usuario:

```php
<!-- templates/header.php -->
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gestión de Usuarios</title>
</head>
<body>
    <header>
        <h1>Gestión de Usuarios</h1>
        <nav>
            <a href="index.php">Inicio</a>
            <a href="add_user.php">Añadir Usuario</a>
        </nav>
    </header>
```

### 5. Archivo ```footer.php``` (Pie de la página)

```php
<!-- templates/footer.php -->
    <footer>
        <p>Todos los derechos reservados &copy; 2024</p>
    </footer>
</body>
</html>
```

### 6. Archivo ```index.php``` (Ver usuarios)

En ```index.php``` se listan todos los usuarios, y cada usuario tiene un botón para eliminarlo:

```php
<?php
// index.php

// Incluir conexión a la base de datos
require 'db.php';

// Incluir el encabezado
include 'templates/header.php';

// Consultar los usuarios de la base de datos
try {
    $stmt = $pdo->query("SELECT id, nombre, email FROM usuarios");
    $usuarios = $stmt->fetchAll(PDO::FETCH_ASSOC);
} catch (Exception $e) {
    die("Error al realizar la consulta: " . $e->getMessage());
}
?>

<main>
    <h2>Lista de Usuarios</h2>
    <ul>
        <?php foreach ($usuarios as $usuario): ?>
            <li>
                <?php echo htmlspecialchars($usuario['nombre']) . " (" . htmlspecialchars($usuario['email']) . ")"; ?>
                <form method="POST" action="delete_user.php" style="display:inline;">
                    <input type="hidden" name="id" value="<?php echo $usuario['id']; ?>">
                    <button type="submit">Eliminar</button>
                </form>
            </li>
        <?php endforeach; ?>
    </ul>
</main>

<?php
// Incluir el pie de página
include 'templates/footer.php';
?>
```

### Explicación del código anterior
### 1. Ver usuarios:

* index.php carga todos los usuarios desde la base de datos y los muestra en una lista.
* Cada usuario tiene un botón "Eliminar", que envía el ID del usuario a delete_user.php a través de un formulario POST.


### 7. Archivo ```add_user.php``` (Añadir un usuario)

Este archivo muestra un formulario para agregar un nuevo usuario, y luego procesa el formulario para insertar el usuario en la base de datos.

```php
<?php
require 'db.php';

include 'templates/header.php';

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    // Recoger los datos del formulario
    $nombre = $_POST['nombre'] ?? '';
    $email = $_POST['email'] ?? '';

    // Validar los datos
    if (!empty($nombre) && !empty($email)) {
        try {
            $stmt = $pdo->prepare("INSERT INTO usuarios (nombre, email) VALUES (?, ?)");
            $stmt->execute([$nombre, $email]);

            echo "<p>Usuario añadido con éxito</p>";
        } catch (Exception $e) {
            die("Error al añadir el usuario: " . $e->getMessage());
        }
    } else {
        echo "<p>Por favor, completa todos los campos.</p>";
    }
}
?>

<main>
    <h2>Añadir Usuario</h2>
    <form method="POST">
        <label for="nombre">Nombre:</label>
        <input type="text" id="nombre" name="nombre" required>
        <br>
        <label for="email">Email:</label>
        <input type="email" id="email" name="email" required>
        <br>
        <button type="submit">Añadir</button>
    </form>
</main>

<?php
include 'templates/footer.php';
?>
```

### Explicación del código
### 1. Formulario para añadir usuarios:

* Cuando el usuario envía el formulario, el script valida si los campos están completos.
* Si todo es correcto, inserta el nuevo usuario en la base de datos usando una sentencia preparada ```($stmt->execute([$nombre, $email]))```.


### 8. Archivo ```delete_user.php``` (Eliminar un usuario)

Este archivo maneja la eliminación de un usuario cuando el formulario de "Eliminar" en ```index.php``` es enviado.

```php
<?php
// delete_user.php

// Incluir conexión a la base de datos
require 'db.php';

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $id = $_POST['id'] ?? '';

    if (!empty($id)) {
        try {
            // Eliminar el usuario por su ID
            $stmt = $pdo->prepare("DELETE FROM usuarios WHERE id = ?");
            $stmt->execute([$id]);

            echo "<p>Usuario eliminado con éxito</p>";
        } catch (Exception $e) {
            die("Error al eliminar el usuario: " . $e->getMessage());
        }
    } else {
        echo "<p>ID inválido.</p>";
    }
}

// Redirigir a la página principal después de eliminar
header('Location: index.php');
exit;
?>
```

### Explicación del código
### 1. Eliminar un usuario:

* Este script recibe el ID del usuario que se va a eliminar a través de un formulario POST.
* Elimina el usuario de la base de datos usando una sentencia preparada ($stmt->execute([$id])).
* Después de eliminar, redirige al usuario de vuelta a index.php usando header('Location: index.php');.

### Ciclo de vida completo:

1. El navegador solicita ```http://tu-sitio.com/index.php```.
* El navegador envía la solicitud a Apache.

2. Apache recibe la solicitud y delega a PHP el manejo del archivo ```index.php```.
* Apache maneja la solicitud con PHP.

3. PHP ejecuta el código en ```index.php```, obtiene datos de la base de datos y genera HTML.
* PHP consulta la base de datos y muestra los usuarios.

4. PHP devuelve el HTML generado al servidor Apache.
* Una vez que PHP ha generado la página, la devuelve a Apache.

5. Apache envía el HTML al navegador.
* Apache transmite la respuesta al cliente.

6. El navegador muestra la página web al usuario.
* El navegador renderiza la lista de usuarios, y el usuario puede interactuar con la aplicación (añadir o eliminar usuarios).

# Relaciones entre clases

