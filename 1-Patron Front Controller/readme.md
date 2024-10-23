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

## **Ejemplo completo de front controller**
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
**Explicación:**

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
**Explicación:**

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
     
**Ejemplo de uso:**

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

**Vista en el navegador:**

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

**Explicación:**

1. class BaseController:
   
* Define una clase llamada BaseController, que actuará como una clase base para otros controladores. Otros controladores heredarían de esta clase y tendrían acceso a sus métodos.
  
2. Método render($view, $data = []):
   
Este método está diseñado para mostrar una vista, es decir, para cargar un archivo de vista que presente una interfaz al usuario.

* $view: Es el nombre del archivo de vista que deseas cargar. Se espera que esté ubicado en el directorio views, que probablemente contenga los archivos HTML o PHP para mostrar al usuario.
  
* $data = []: Este es un array opcional que contiene los datos que deseas pasar a la vista. Por defecto, si no se pasan datos, es un array vacío.
  
* extract($data): Esta función convierte los elementos del array $data en variables individuales. Por ejemplo, si $data contiene ['name' => 'John'], se creará una variable $name con el valor 'John'. Esto facilita el uso de los datos dentro de la vista.
  
* include __DIR__ . '/../views/' . $view . '.php';: Carga e incluye el archivo de vista. Utiliza __DIR__ para obtener el directorio actual donde está el script (en este caso, el controlador). Luego, navega al directorio views donde se espera encontrar la vista (por ejemplo, si $view = 'home', incluirá el archivo views/home.php).

**Ejemplo de uso del método ```render:```**

```php
$this->render('home', ['name' => 'John']);
```

* Esto buscaría e incluiría el archivo ```views/home.php``` y dentro de esa vista se podría usar la variable ```$name```, que tendrá el valor ```'John'```.

3. Método redirect($url):
Este método se utiliza para redirigir a una URL específica, es decir, enviar al usuario a otra página.

* $url: Es la URL a la que se redirige al usuario.
* header('Location: ' . $url);: Envía una cabecera HTTP de redirección con la URL especificada. Esto indica al navegador que debe cargar una nueva página.
* exit;: Detiene la ejecución del script inmediatamente después de la redirección, asegurándose de que no se ejecute ningún código adicional tras la redirección.

**Ejemplo de uso del método ```redirect```**

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

**Explicación:**

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
**Explicación:**

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

**Explicación:**

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

**Explicación:**

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

**Explicación:**

* Los enlaces usan la estructura ```index.php?route=...``` para navegar entre las diferentes rutas de la aplicación.

    * Inicio: Redirige a la ruta home.
    * Productos: Redirige a la ruta product.
    * Iniciar Sesión: Redirige a la ruta login.

Estas rutas son procesadas por el controlador (index.php) y despachadas para mostrar la vista correspondiente.

**Vista en el navegador:**

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

**Explicación:**

* (<?php foreach...): Aquí utilizamos un bucle foreach para recorrer la lista de productos almacenada en la variable $products, que es pasada desde el controlador. Por cada elemento de $products, se genera un <li> (elemento de lista) que contiene el nombre del producto.
  
    * $products: Es un array de productos que contiene los nombres (por ejemplo, ['Producto 1', 'Producto 2', 'Producto 3']).
    * <li><?php echo $product; ?></li>: Cada nombre de producto se inserta dentro de un <li>, que es el formato estándar para listas en HTML.


**Vista en el navegador:**

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

**Vista en el navegador:**

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

**Explicación:**

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

**Resumen del flujo:**

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

### **Ejemplo de uso:**

Archivo ```get/script.php```
```php
<?php
echo "Hola, " . htmlspecialchars($_GET["nombre"]);
?>
```

Si accedemos a este script con una URL como ```http://localhost:3000/script.php?nombre=Yamir Ramirez```, el resultado será:

**Vista en el navegador:**


<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/get.jpg?raw=true" alt="Imagen" style />
</p>

### Petición POST:
Sirve para enviar información que será procesada, por ejemplo, para crear un nuevo recurso.

### **Ejemplo de uso:**

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

### **Ejemplo de uso:**

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

### **Ejemplo de uso:**

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

**Vista en el navegador:**

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/delete_query_params.png?raw=true" alt="Imagen" style />
</p>
<br>

## Definición de respuesta http:

Es el mensaje que el servidor web envía de vuelta al cliente (normalmente un navegador o una aplicación) en respuesta a una solicitud HTTP. Este mensaje incluye:

### 1. Código de estado HTTP: 

Indica si la solicitud fue exitosa (por ejemplo, 200 OK) o si ocurrió algún error (por ejemplo, 404 Not Found, 500 Internal Server Error).

### Códigos de estado HTTP más comunes:

<div style="text-align: center;">
    
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

</div>


# Ciclo de vida de una petición http
# Relaciones entre clases






