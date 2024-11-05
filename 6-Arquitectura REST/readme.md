# Modelo de madurez de Richardson
El modelo de madurez de Richardson es una forma de evaluar la madurez de una API REST en cuatro niveles, considerando la utilización de características propias de HTTP para alcanzar un diseño más completo y eficiente.

### Nivel 0: El túnel HTTP
En este nivel, la API no aprovecha las ventajas de HTTP; normalmente, solo se utiliza el método POST y una única URL para todas las operaciones. Los datos y el tipo de operación se incluyen en el cuerpo de la solicitud. Esto limita el aprovechamiento de REST y de las ventajas que ofrece HTTP, como el caché o la identificación de recursos.

### Ejemplo:

```php
if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    $data = json_decode(file_get_contents("php://input"), true);
    $action = $data['action'] ?? '';

    if ($action == 'crear') {
        // Código para crear un recurso
    } elseif ($action == 'actualizar') {
        // Código para actualizar un recurso
    } elseif ($action == 'eliminar') {
        // Código para eliminar un recurso
    } else {
        // Código para manejar otras operaciones
    }
}
```

### Nivel 1: Recursos individuales
En este nivel, cada recurso tiene su propia URL, permitiendo el uso de múltiples endpoints (URLs) para cada tipo de recurso. Este es un paso adelante, ya que organiza la API de forma más clara y cada recurso se representa de manera única.

### Ejemplo:

```php
// Definición de URLs específicas para cada recurso
$uri = $_SERVER['REQUEST_URI'];

if ($uri == '/api/productos') {
    if ($_SERVER['REQUEST_METHOD'] == 'GET') {
        // Código para listar productos
    } elseif ($_SERVER['REQUEST_METHOD'] == 'POST') {
        // Código para crear un nuevo producto
    }
} elseif (preg_match('/^\/api\/productos\/(\d+)$/', $uri, $matches)) {
    $id = $matches[1];
    if ($_SERVER['REQUEST_METHOD'] == 'GET') {
        // Código para obtener un producto por ID
    } elseif ($_SERVER['REQUEST_METHOD'] == 'PUT') {
        // Código para actualizar un producto por ID
    } elseif ($_SERVER['REQUEST_METHOD'] == 'DELETE') {
        // Código para eliminar un producto por ID
    }
}
```
### Nivel 2: Verbos HTTP
Este nivel implica el uso de métodos HTTP como GET, POST, PUT y DELETE para representar las operaciones CRUD de manera estándar. Esto hace que la API sea más intuitiva y aprovecha las convenciones de HTTP para cada tipo de operación.

### Ejemplo:

```php
$uri = $_SERVER['REQUEST_URI'];
$id = null;
if (preg_match('/^\/api\/productos\/(\d+)$/', $uri, $matches)) {
    $id = $matches[1];
}

switch ($_SERVER['REQUEST_METHOD']) {
    case 'GET':
        if ($id) {
            // Código para obtener un producto por ID
        } else {
            // Código para listar todos los productos
        }
        break;
    case 'POST':
        // Código para crear un nuevo producto
        break;
    case 'PUT':
        if ($id) {
            // Código para actualizar un producto por ID
        }
        break;
    case 'DELETE':
        if ($id) {
            // Código para eliminar un producto por ID
        }
        break;
}
```

### Nivel 3: HATEOAS (Hypermedia As The Engine Of Application State)
En este nivel, la API incluye enlaces de navegación dentro de las respuestas, guiando al cliente sobre qué acciones puede realizar a continuación. Este enfoque facilita la interacción con la API, ya que permite descubrir las operaciones disponibles sin necesidad de consultar la documentación externa.

### Ejemplo:

```php
header('Content-Type: application/json');

function getProducto($id) {
    $producto = [
        "id" => $id,
        "nombre" => "Producto de ejemplo",
        "precio" => 100.0,
        "_links" => [
            "self" => ["href" => "/api/productos/$id"],
            "editar" => ["href" => "/api/productos/$id", "method" => "PUT"],
            "eliminar" => ["href" => "/api/productos/$id", "method" => "DELETE"],
            "listar" => ["href" => "/api/productos", "method" => "GET"]
        ]
    ];
    return json_encode($producto);
}

if ($_SERVER['REQUEST_METHOD'] == 'GET' && isset($_GET['id'])) {
    echo getProducto($_GET['id']);
}
```
# Buenas prácticas en el diseño de una API REST: 

### 1. Versionamiento: 
Incluye la versión de la API en la URL (/api/v1/productos) para mantener la compatibilidad con versiones anteriores.
### 2. Manejo de errores: 
Utiliza códigos de estado HTTP adecuados, como 404 Not Found, 400 Bad Request, 500 Internal Server Error, etc., y devuelve mensajes de error detallados en formato JSON.
### 3. Documentación: 
Utiliza herramientas como Swagger o Postman para documentar y probar la API.
### 4. Autenticación y autorización: 
Protege la API usando mecanismos como OAuth, JWT o API keys, especialmente si la API va a ser pública o requiere manejo de datos sensibles.
### 5. Paginación, filtros y clasificación: 
Implementa paginación y opciones de filtro en las respuestas que manejan grandes volúmenes de datos.

# Análisis de requerimientos
# Diseño de software

