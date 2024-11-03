# Estándares de php: psr y spl
## ¿Que es PSR?
Los estándares PSR (PHP Standard Recommendations) son guías creadas por el PHP-FIG (PHP Framework Interoperability Group) para ayudar a estandarizar las prácticas de codificación y facilitar la interoperabilidad entre diferentes proyectos y bibliotecas en PHP. 

## Principales estándares PSR:

| PSR    | Título                                      | Descripción                                                      | Ejemplo                                                  |
|--------|----------------------------------------------|------------------------------------------------------------------|----------------------------------------------------------|
| PSR-1  | Estándares Básicos de Codificación          | Define las bases para una codificación coherente y legible.      | Clases deben usar *StudlyCaps*: `class MiClase {}`      |
| PSR-2  | Guía de Estilo de Codificación              | Extiende PSR-1 con una guía detallada de estilo de codificación. | Indentación de 4 espacios: `if ($x) { echo $x; }`       |
| PSR-3  | Interfaz de Registro de Log                 | Especifica una interfaz estándar para el registro de logs.       | Uso de `LoggerInterface`: `$logger->info('Mensaje');`   |
| PSR-4  | Autoloading de Clases                       | Define un estándar de autoloading mediante namespaces.           | `namespace App\Controllers; class HomeController {}`   |
| PSR-7  | Interfaces de Solicitud y Respuesta HTTP    | Establece interfaces para manejar solicitudes y respuestas HTTP. | `$request->getMethod(); $response->withStatus(200);`    |
| PSR-12 | Extensión de la Guía de Estilo PSR-2        | Actualiza y mejora PSR-2 con nuevas prácticas de codificación.   | `declare(strict_types=1); use function ...;`            |

## ¿Que es SPL?
SPL (Standard PHP Library) es una colección de interfaces y clases que se incluyen en PHP de forma predeterminada. Está diseñada para proporcionar un conjunto de herramientas estándar para la manipulación de estructuras de datos, iteración, y manejo de excepciones, entre otras funcionalidades. 

## Principales componentes de SPL:

| Componente          | Descripción                                                | Ejemplo de Uso                                         |
|---------------------|------------------------------------------------------------|---------------------------------------------------------|
| Iteradores          | Permiten recorrer estructuras de datos de forma flexible. | `foreach (new DirectoryIterator('.') as $file) { ... }`|
| Estructuras de Datos| Clases para manejar pilas, colas, y listas enlazadas.     | `$stack = new SplStack(); $stack->push('Elemento');`    |
| Autoloading         | Función para cargar clases automáticamente.               | `spl_autoload_register(function ($class) { ... });`     |
| Excepciones SPL     | Excepciones estándar para errores comunes en PHP.         | `throw new InvalidArgumentException('Mensaje de error');` |
| Interfaces SPL      | Interfaces para implementar funcionalidades comunes.      | `class MiClase implements Countable { ... }`           |


# Gestión de los abstractos (clases abstractas e interfaces)
# Miembros estáticos, patrones de diseño (GOF) introducción a sistemas distribuidos
# Introducción a sistemas distribuidos
# Desarrollo de una API REST


