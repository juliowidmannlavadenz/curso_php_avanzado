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

> El modelo de madurez de Richardson permite construir una API REST estructurada y que cumpla con las mejores prácticas del diseño RESTful, lo cual mejora la experiencia de los desarrolladores y usuarios de la API.

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
El análisis de requerimientos es una etapa crucial en el desarrollo de software que tiene como objetivo comprender en detalle qué necesita un sistema para satisfacer las expectativas y necesidades de sus usuarios o stakeholders (partes interesadas). En este proceso se recopilan, documentan y evalúan los requisitos que definen qué debe hacer el sistema y cómo debe comportarse.

## Etapas del Análisis de Requerimientos
### 1. Identificación de Stakeholders:
Es fundamental saber quiénes son los usuarios, administradores y otras personas que tendrán alguna relación con el sistema. Cada stakeholder puede tener diferentes expectativas y necesidades que el sistema debe satisfacer.

### 2. Recolección de Requerimientos: 
Se trata de recopilar toda la información sobre las funcionalidades y el alcance del sistema. Para esto se pueden utilizar diversas técnicas, como:

* **Entrevistas:** Reuniones con los usuarios o stakeholders para entender sus expectativas.
* **Encuestas y Cuestionarios:** Útil para recolectar datos de un grupo grande de usuarios.
* **Análisis de Documentación Existente:** Revisión de sistemas actuales, documentos o cualquier información relevante.
* **Observación:** Observar cómo trabajan los usuarios con sistemas actuales.
* **Prototipos:** Crear prototipos puede ayudar a los usuarios a visualizar el sistema y dar feedback.

### 3. Clasificación de Requerimientos:  
Una vez recopilados, se clasifican en diferentes tipos:

* **Funcionales:** Especifican lo que el sistema debe hacer. Ejemplo: "El sistema debe permitir registrar y actualizar datos de clientes."
* **No Funcionales:** Detallan las características de calidad y restricciones del sistema. Ejemplo: "El sistema debe responder en menos de 2 segundos."
* **Requerimientos de Negocio:** Están relacionados con los objetivos y el valor que se espera que el sistema aporte a la organización.
* **Requerimientos Técnicos:** Involucran especificaciones sobre tecnologías, plataformas y arquitecturas que se deben usar.

### 4. Análisis y Priorización de Requerimientos: 
No todos los requerimientos tienen la misma importancia ni urgencia. Es importante priorizar los requisitos para asegurar que los aspectos más críticos se desarrollen primero. Esto también ayuda a planificar el desarrollo de manera efectiva.

### 5. Documentación de Requerimientos: 
Los requerimientos deben ser documentados de manera clara y detallada, en un formato que facilite su comprensión y revisión. Documentos comunes incluyen:

* **Especificación de Requerimientos de Software (SRS):** Incluye todos los requerimientos detallados.
* **Historias de Usuario:** Descripciones de requerimientos desde la perspectiva del usuario final (más común en metodologías ágiles).

### 6. Validación de Requerimientos: 
Los requerimientos deben ser revisados y aprobados por los stakeholders para asegurar que se alineen con sus expectativas y necesidades. La validación garantiza que los requisitos son correctos, necesarios y factibles de implementar.

### 7. Gestión de Cambios en los Requerimientos: 
A lo largo del proyecto, es común que los requerimientos cambien. Tener un proceso de control de cambios ayuda a evaluar el impacto de los nuevos requerimientos y permite gestionar adecuadamente los recursos y plazos.

## Importancia del Análisis de Requerimientos
Un buen análisis de requerimientos permite:

* Reducir el riesgo de errores y omisiones en el desarrollo.
* Mejorar la comunicación entre desarrolladores y usuarios finales.
* Evitar sobrecostos y retrasos causados por la falta de claridad o cambios imprevistos.
* Aumentar la satisfacción del cliente al garantizar que el producto final cumpla con sus expectativas.

## Herramientas de Apoyo
Algunas herramientas que facilitan el análisis de requerimientos incluyen:

* **Casos de Uso y Diagramas UML:** Ayudan a modelar el comportamiento del sistema y su interacción con los usuarios.
* **Herramientas de Gestión de Requerimientos:** Como Jira, Confluence, o Trello para seguimiento y gestión de tareas.
* **Prototipado:** Herramientas como Figma, Balsamiq o Adobe XD ayudan a visualizar la interfaz del sistema.

> El análisis de requerimientos es un paso decisivo en el ciclo de vida del desarrollo de software, ya que establece la base sobre la cual se construirá el sistema.

# Diseño de software

