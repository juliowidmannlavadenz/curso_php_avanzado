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



# Clases abstractas
Es una clase que no se puede instanciar directamente; es decir, no se pueden crear objetos de ella. Sirve como una plantilla para que otras clases la extiendan. Las clases abstractas se utilizan para definir métodos que deben ser implementados por las subclases, asegurando una estructura consistente en todas las clases derivadas.

## Características principales
### 1. No se puede instanciar: 
No se puede crear un objeto de una clase abstracta directamente.

### Ejemplo:
```php
abstract class Animal {
    abstract public function hacerSonido();
}
```
### Explicación:

1. La clase ```Animal``` es abstracta, lo que significa que no se puede instanciar directamente (no puedes hacer ```$animal = new Animal()```).
2. Contiene un método abstracto ```hacerSonido()```, que es solo una declaración sin implementación. Las clases que extiendan ```Animal``` deben implementar el método ```hacerSonido()``` para definir su comportamiento específico.

### Extendiendo la clase ```Animal```

```php
abstract class Animal {
    abstract public function hacerSonido();
}

class Perro extends Animal {
    public function hacerSonido() {
        echo "Guau guau";
    }
}

class Gato extends Animal {
    public function hacerSonido() {
        echo "Miau miau";
    }
}

// Crear instancias de las clases derivadas y llamar al método hacerSonido
$perro = new Perro();
$perro->hacerSonido(); // Salida: Guau guau

$gato = new Gato();
$gato->hacerSonido(); // Salida: Miau miau
```

### Explicación:

1. La clase ```Animal``` es una clase abstracta que declara un método abstracto ```hacerSonido()```. Esto significa que cualquier clase que herede de ```Animal``` está obligada a implementar el método ```hacerSonido()```.
2. La clase ```Perro``` extiende ```Animal``` e implementa el método ```hacerSonido()``` con la salida específica "Guau guau".
3. La clase ```Gato``` también extiende ```Animal``` e implementa su propia versión del método ```hacerSonido()``` con la salida "Miau miau".
4. Cuando se crean instancias de ```Perro``` y ```Gato``` y se llama al método ```hacerSonido()```, cada una muestra su comportamiento específico. Esto es un ejemplo de **polimorfismo**, donde las clases derivadas tienen diferentes implementaciones del mismo método declarado en la clase base abstracta.



### 2. Métodos abstractos: 
Puede contener métodos abstractos, que son métodos sin cuerpo (implementación). Las clases que heredan de la clase abstracta deben proporcionar la implementación de estos métodos.

### Ejemplo:

```php
abstract class Vehiculo {
    abstract public function arrancar();
}

class Coche extends Vehiculo {
    public function arrancar() {
        echo "El coche está arrancando";
    }
}

class Moto extends Vehiculo {
    public function arrancar() {
        echo "La moto está arrancando";
    }
}
```

### Explicación:
1. **Clase abstracta** ```Vehiculo```: Es una clase que no puede ser instanciada directamente. Contiene un método abstracto ```arrancar()```, que solo se declara y no tiene implementación. Las clases que hereden de ```Vehiculo``` deben implementar este método.

2. **Clase** ```Coche```: Extiende la clase abstracta ```Vehiculo``` e implementa el método ```arrancar()```, definiendo su propia funcionalidad que imprime "El coche está arrancando".

3. **Clase** ```Moto```: También extiende ```Vehiculo``` e implementa su propia versión del método ```arrancar()```, que imprime "La moto está arrancando".

4. **Polimorfismo**: Las clases ```Coche``` y ```Moto``` implementan el método ```arrancar()``` de formas diferentes, lo que permite que cada clase tenga su propia versión del método abstracto.

### 3. Métodos concretos: 
También puede contener métodos con implementación, lo que permite definir comportamientos comunes que pueden ser heredados por las subclases.

### Ejemplo:

```php
abstract class DispositivoElectronico {
    // Método abstracto (debe ser implementado por las clases hijas)
    abstract public function encender();

    // Método concreto (tiene implementación y puede ser heredado)
    public function informacion() {
        echo "Este es un dispositivo electrónico.";
    }
}

class Televisor extends DispositivoElectronico {
    public function encender() {
        echo "El televisor se está encendiendo.";
    }
}
```

### Explicación:
1. **Clase abstracta** ```DispositivoElectronico```: No se puede instanciar directamente. Contiene un método abstracto ```encender()``` que debe ser implementado por las clases que la extiendan y un método concreto ```informacion()``` con una implementación que puede ser heredada por las clases hijas.

2. **Método concreto** ```informacion()```: Este método tiene una implementación completa en la clase abstracta y puede ser llamado por las instancias de las clases derivadas sin necesidad de ser redefinido.

3. **Clase** ```Televisor```: Extiende ```DispositivoElectronico``` e implementa el método abstracto ```encender()```, proporcionando la funcionalidad específica para encender el televisor.

### Uso del método concreto:

```php
$tv = new Televisor();
$tv->encender(); // Salida: El televisor se está encendiendo.
$tv->informacion(); // Salida: Este es un dispositivo electrónico.
```
* Aquí, ```encender()``` es implementado en la clase ```Televisor```, mientras que ```informacion()``` es heredado de ```DispositivoElectronico``` y se usa sin modificación.

### 4. Declaración: 
Se usa la palabra clave abstract antes de la definición de la clase y de los métodos abstractos.

### Ejemplo:

```php
abstract class Transporte {
    // Declaración de un método abstracto
    abstract public function mover();

    // Método concreto con implementación
    public function tipoTransporte() {
        echo "Este es un medio de transporte.";
    }
}
```

### Explicación:
1. **Declaración de la clase abstracta** ```Transporte```: La palabra clave ```abstract``` se utiliza para definir una clase abstracta. Esto significa que ```Transporte``` no puede ser instanciada directamente. Es una clase base que debe ser extendida por otras clases.

2. **Método abstracto** ```mover()```: Se declara sin cuerpo (sin implementación). Las clases que hereden de ```Transporte``` están obligadas a implementar este método, definiendo su propia lógica de cómo "moverse".

3. **Método concreto** ```tipoTransporte()```: Se define con una implementación en la clase abstracta. Las clases hijas pueden usar este método tal como está o sobrescribirlo si es necesario.

## Ejemplo completo de clases abstractas
Ejemplo de un sistema CRUD (Crear, Leer, Actualizar, Borrar) que utiliza clases abstractas para gestionar tickets de soporte.

### 1. Estructura de archivos
```php
support-tickets/
│
├── index.php              # Archivo principal para manejar las solicitudes
├── Ticket.php             # Clase abstracta Ticket
├── TicketRepository.php    # Clase para manejar la lógica CRUD
├── Database.php           # Clase para manejar la conexión a la base de datos
└── config.php             # Configuración de la base de datos
```

### 2. Creación de la BBDD y tabla relacionada
```php
CREATE DATABASE IF NOT EXISTS support_tickets;

USE support_tickets;

CREATE TABLE IF NOT EXISTS tickets (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    description TEXT NOT NULL,
    status ENUM('open', 'closed') NOT NULL DEFAULT 'open'
);
```
### 3. Configuración de la base de datos
**Archivo:** ```config.php```

```php
<?php
// config.php
define('DB_HOST', 'localhost');
define('DB_NAME', 'support_tickets');
define('DB_USER', 'root');
define('DB_PASS', '');
?>
```
* Aquí se definen las constantes para la configuración de la base de datos.

### 4. Gestionar la conexión a la base de datos
**Archivo:** ```Database.php```

```php
<?php
class Database {
    private $host = DB_HOST;
    private $dbname = DB_NAME;
    private $username = DB_USER;
    private $password = DB_PASS;
    public $conn;

    public function __construct() {
        $this->conn = null;
        try {
            $this->conn = new PDO("mysql:host={$this->host};dbname={$this->dbname}", $this->username, $this->password);
            $this->conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
        } catch (PDOException $e) {
            echo "Connection error: " . $e->getMessage();
        }
    }
}
?>
```
* Se define la clase ```Database``` que maneja la conexión a la base de datos utilizando PDO. Si la conexión falla, se captura la excepción y se muestra un mensaje de error.

### 5. Definir la clase abstracta Ticket
**Archivo:** ```Ticket.php```

```php
<?php
abstract class Ticket {
    protected $id;
    protected $title;
    protected $description;
    protected $status;

    abstract public function create();
    abstract public function read();
    abstract public function update();
    abstract public function delete();

    // Getters y setters
    public function getId() {
        return $this->id;
    }

    public function setId($id) {
        $this->id = $id;
    }

    public function getTitle() {
        return $this->title;
    }

    public function setTitle($title) {
        $this->title = $title;
    }

    public function getDescription() {
        return $this->description;
    }

    public function setDescription($description) {
        $this->description = $description;
    }

    public function getStatus() {
        return $this->status;
    }

    public function setStatus($status) {
        $this->status = $status;
    }
}
?>
```
* La clase abstracta ```Ticket``` define la estructura básica de un ticket. Incluye propiedades para el título, la descripción y el estado, así como métodos abstractos para las operaciones CRUD.

### 6. Implementar la lógica CRUD para los tickets
**Archivo:** ```TicketRepository.php```

```php
<?php
require_once 'Database.php';
require_once 'Ticket.php';
require_once 'config.php'; // Asegúrate de incluir el archivo de configuración

class TicketRepository extends Ticket {
    private $db;

    public function __construct() {
        $this->db = (new Database())->conn;
    }

    public function create() {
        $query = "INSERT INTO tickets (title, description, status) VALUES (:title, :description, :status)";
        $stmt = $this->db->prepare($query);
        $stmt->bindParam(':title', $this->title);
        $stmt->bindParam(':description', $this->description);
        $stmt->bindParam(':status', $this->status);
        return $stmt->execute();
    }

    public function read() {
        $query = "SELECT * FROM tickets";
        $stmt = $this->db->prepare($query);
        $stmt->execute();
        return $stmt->fetchAll(PDO::FETCH_ASSOC);
    }

    public function update() {
        $query = "UPDATE tickets SET title = :title, description = :description, status = :status WHERE id = :id";
        $stmt = $this->db->prepare($query);
        $stmt->bindParam(':title', $this->title);
        $stmt->bindParam(':description', $this->description);
        $stmt->bindParam(':status', $this->status);
        $stmt->bindParam(':id', $this->id);
        return $stmt->execute();
    }

    public function delete() {
        $query = "DELETE FROM tickets WHERE id = :id";
        $stmt = $this->db->prepare($query);
        $stmt->bindParam(':id', $this->id);
        return $stmt->execute();
    }
}
?>
```

* Esta clase extiende la clase abstracta ```Ticket``` y proporciona la implementación concreta de los métodos CRUD. Utiliza consultas SQL para interactuar con la base de datos y manipular los tickets.

### 7. Punto de entrada para manejar las solicitudes
**Archivo:** ```index.php```

```php
<?php

require_once 'TicketRepository.php';

$ticketRepo = new TicketRepository();

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    if (isset($_POST['create'])) {
        $ticketRepo->setTitle($_POST['title']);
        $ticketRepo->setDescription($_POST['description']);
        $ticketRepo->setStatus('open');
        $ticketRepo->create();
    } elseif (isset($_POST['update'])) {
        $ticketRepo->setId($_POST['id']);
        $ticketRepo->setTitle($_POST['title']);
        $ticketRepo->setDescription($_POST['description']);
        $ticketRepo->setStatus($_POST['status']);
        $ticketRepo->update();
    } elseif (isset($_POST['delete'])) {
        $ticketRepo->setId($_POST['id']);
        $ticketRepo->delete();
    }
}

$tickets = $ticketRepo->read();
?>

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Support Tickets</title>
</head>
<body>
    <h1>Tickets de Soporte</h1>
    <form method="POST">
        <input type="text" name="title" placeholder="Título" required>
        <textarea name="description" placeholder="Descripción" required></textarea>
        <button type="submit" name="create">Crear Ticket</button>
    </form>
    
    <h2>Lista de Tickets</h2>
    <ul>
        <?php foreach ($tickets as $ticket): ?>
            <li>
                <strong><?php echo htmlspecialchars($ticket['title']); ?></strong> - <?php echo htmlspecialchars($ticket['status']); ?>
                <form method="POST">
                    <input type="hidden" name="id" value="<?php echo $ticket['id']; ?>">
                    <input type="text" name="title" value="<?php echo htmlspecialchars($ticket['title']); ?>" required>
                    <textarea name="description" required><?php echo htmlspecialchars($ticket['description']); ?></textarea>
                    <select name="status">
                        <option value="open" <?php echo $ticket['status'] == 'open' ? 'selected' : ''; ?>>Abierto</option>
                        <option value="closed" <?php echo $ticket['status'] == 'closed' ? 'selected' : ''; ?>>Cerrado</option>
                    </select>
                    <button type="submit" name="update">Actualizar</button>
                    <button type="submit" name="delete">Eliminar</button>
                </form>
            </li>
        <?php endforeach; ?>
    </ul>
</body>
</html>
```

* Este archivo es la interfaz de usuario que permite a los usuarios crear, actualizar y eliminar tickets. Utiliza formularios para capturar datos de entrada y muestra una lista de tickets existentes. La lógica de manejo de solicitudes se basa en el método HTTP utilizado.

### Vista en el navegador:

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/tickets.png?raw=true" alt="Imagen width="400" style />
</p>
<br>

### Conclusión:
Este ejemplo proporciona una base sólida para un sistema de gestión de tickets de soporte, mostrando el uso de clases abstractas en PHP, así como la implementación de las operaciones básicas de un CRUD. 

# Interfaces
## Definición
Una interfaz es un contrato que define un conjunto de métodos que una clase debe implementar, pero no proporciona la implementación de esos métodos. Las interfaces se utilizan para establecer un estándar que las clases pueden seguir, lo que permite la creación de código más estructurado y flexible.

## Características
### 1. Definición de métodos: 
Una interfaz solo puede declarar métodos, no puede contener implementaciones. Cualquier clase que implemente la interfaz debe proporcionar la implementación de estos métodos.

### Ejemplo:
```php
interface Ejemplo {
    public function metodo1();
    public function metodo2();
}
```
* Este fragmento muestra cómo se define una interfaz ```Ejemplo``` con dos métodos que cualquier clase que la implemente debe definir.

### 2. Múltiples interfaces: 
Una clase puede implementar múltiples interfaces, lo que permite que una clase tenga más de un tipo de comportamiento.

### Ejemplo:

```php
interface Interfaz1 { public function metodoA(); }
interface Interfaz2 { public function metodoB(); }
class MiClase implements Interfaz1, Interfaz2 { public function metodoA() {} public function metodoB() {} }
```
* La clase ```MiClase``` implementa tanto ```Interfaz1``` como ```Interfaz2``` y proporciona la implementación de ```metodoA()``` y ```metodoB()```.

### 3. No se pueden instanciar: 
Las interfaces no se pueden instanciar directamente. Solo se pueden usar como tipos de referencia para las clases que las implementan.

```php
interface MiInterfaz { public function metodo(); }
$instancia = new MiInterfaz(); // Error: no se puede instanciar una interfaz
```
* Este código muestra que intentar crear una instancia de ```MiInterfaz``` provoca un error, ya que las interfaces no se pueden instanciar directamente.

### 4. Visibilidad de métodos: 
Todos los métodos declarados en una interfaz son públicos por defecto y no se puede especificar una visibilidad diferente.

```php
interface Ejemplo { public function metodoA(); }
class MiClase implements Ejemplo { public function metodoA() { echo "Implementación de metodoA"; } }
```
* En este ejemplo, el método ```metodoA()``` declarado en la interfaz ```Ejemplo``` es automáticamente público, y la clase ```MiClase``` lo implementa con la misma visibilidad.

## Ejemplo completo de interface: Creación de CRUD 
Vamos a crear un sistema de gestión de estudiantes que permita registrar, listar, obtener detalles de los estudiantes y borrar estudiantes. La interfaz garantizará que todas las clases que implementan la funcionalidad relacionada con los estudiantes sigan un mismo esquema.

### 1. Estructura de archivos

```php
colegio/
├── index.php
├── StudentInterface.php
├── Student.php
├── StudentRepository.php
├── db.php
└── database.sql
```

### 2. Creacion de la BBDD y tabla asociada
```php
CREATE DATABASE colegio_db;

USE colegio_db;

CREATE TABLE students (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    edad INT NOT NULL,
    grado VARCHAR(50) NOT NULL
);
```
### 3. Conexión a la base de datos
**Archivo:** ```db.php```

```php
<?php
function getConnection() {
    try {
        $dsn = 'mysql:host=localhost;dbname=colegio_db';
        $username = 'root';
        $password = '';
        $options = [
            PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION,
            PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC
        ];
        return new PDO($dsn, $username, $password, $options);
    } catch (PDOException $e) {
        die('Error de conexión: ' . $e->getMessage());
    }
}
```
### Explicación:
* Este archivo establece una conexión con la base de datos usando PDO.

### 4. Definición de la interfaz del estudiante
**Archivo:** ```StudentInterface.php```

```php
<?php
interface StudentInterface {
    public function agregarEstudiante($nombre, $edad, $grado);
    public function obtenerEstudiantes();
    public function obtenerEstudiantePorId($id);
}
```
### Explicación:
* Esta es la interfaz StudentInterface que define el contrato para manejar los estudiantes. Cualquier clase que implemente esta interfaz debe definir estos tres métodos.

### 5. Creación de la clase estudiante ```Student```
**Archivo:** ```Student.php```

```php
<?php
class Student {
    private $id;
    private $nombre;
    private $edad;
    private $grado;

    public function __construct($id, $nombre, $edad, $grado) {
        $this->id = $id;
        $this->nombre = $nombre;
        $this->edad = $edad;
        $this->grado = $grado;
    }

    public function getNombre() {
        return $this->nombre;
    }

    public function getEdad() {
        return $this->edad;
    }

    public function getGrado() {
        return $this->grado;
    }
}
```
### Explicación:
* La clase ```Student``` modela a un estudiante con sus atributos y proporciona métodos para acceder a ellos.

### 6. Creación del repositorio que implementa la interfaz 
**Archivo:** ```StudentRepository.php```

```php
<?php
require_once 'StudentInterface.php';
require_once 'db.php';

class StudentRepository implements StudentInterface {
    private $conn;

    public function __construct() {
        $this->conn = getConnection();
    }

    public function agregarEstudiante($nombre, $edad, $grado) {
        $sql = "INSERT INTO students (nombre, edad, grado) VALUES (:nombre, :edad, :grado)";
        $stmt = $this->conn->prepare($sql);
        $stmt->execute(['nombre' => $nombre, 'edad' => $edad, 'grado' => $grado]);
    }

    public function obtenerEstudiantes() {
        $sql = "SELECT * FROM students";
        $stmt = $this->conn->query($sql);
        return $stmt->fetchAll();
    }

    public function obtenerEstudiantePorId($id) {
        $sql = "SELECT * FROM students WHERE id = :id";
        $stmt = $this->conn->prepare($sql);
        $stmt->execute(['id' => $id]);
        return $stmt->fetch();
    }

    public function eliminarEstudiante($id) {
        $sql = "DELETE FROM students WHERE id = :id";
        $stmt = $this->conn->prepare($sql);
        $stmt->execute(['id' => $id]);
    }    

}
```

### Explicación:
* La clase StudentRepository implementa la interfaz StudentInterface y proporciona la lógica para interactuar con la base de datos.

### 7. Punto de entrada del proyecto
**Archivo:** ```index.php```

```php
<?php
require_once 'StudentRepository.php';

$repo = new StudentRepository();

if ($_SERVER['REQUEST_METHOD'] === 'POST' && isset($_POST['nombre'], $_POST['edad'], $_POST['grado'])) {
    $nombre = $_POST['nombre'];
    $edad = (int)$_POST['edad'];
    $grado = $_POST['grado'];
    
    $repo->agregarEstudiante($nombre, $edad, $grado);
    echo "Estudiante agregado exitosamente.<br>";
}

if (isset($_GET['eliminar_id'])) {
    $id = (int)$_GET['eliminar_id'];
    $repo->eliminarEstudiante($id);
    echo "Estudiante con ID $id eliminado exitosamente.<br>";
}

$estudiantes = $repo->obtenerEstudiantes();
?>

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Gestión de Estudiantes</title>
</head>
<body>
    <h1>Gestión de Estudiantes</h1>

    <h2>Agregar un nuevo estudiante</h2>
    <form method="post" action="">
        <label for="nombre">Nombre:</label>
        <input type="text" id="nombre" name="nombre" required><br>
        <label for="edad">Edad:</label>
        <input type="number" id="edad" name="edad" required><br>
        <label for="grado">Grado:</label>
        <input type="text" id="grado" name="grado" required><br>
        <button type="submit">Agregar Estudiante</button>
    </form>

    <h2>Lista de Estudiantes</h2>
    <?php if (!empty($estudiantes)): ?>
        <ul>
            <?php foreach ($estudiantes as $estudiante): ?>
                <li>
                    <?php echo "Nombre: " . htmlspecialchars($estudiante['nombre']) . " - Edad: " . htmlspecialchars($estudiante['edad']) . " - Grado: " . htmlspecialchars($estudiante['grado']); ?>
                    <a href="?eliminar_id=<?php echo $estudiante['id']; ?>" onclick="return confirm('¿Estás seguro de que quieres eliminar este estudiante?');">[Eliminar]</a>
                </li>
            <?php endforeach; ?>
        </ul>
    <?php else: ?>
        <p>No hay estudiantes registrados.</p>
    <?php endif; ?>

    <h2>Detalles del Estudiante por ID</h2>
    <form method="get" action="">
        <label for="id">ID del Estudiante:</label>
        <input type="number" id="id" name="id" required>
        <button type="submit">Buscar</button>
    </form>

    <?php
    if (isset($_GET['id'])) {
        $id = (int)$_GET['id'];
        $detalles = $repo->obtenerEstudiantePorId($id);
        
        if ($detalles) {
            echo "<h3>Detalles del estudiante con ID $id:</h3>";
            echo "Nombre: " . htmlspecialchars($detalles['nombre']) . "<br>";
            echo "Edad: " . htmlspecialchars($detalles['edad']) . "<br>";
            echo "Grado: " . htmlspecialchars($detalles['grado']) . "<br>";
        } else {
            echo "<p>No se encontró el estudiante con ID $id.</p>";
        }
    }
    ?>
</body>
</html>
```
### Explicación:
* ```index.php``` es el punto de entrada del proyecto. Usa el repositorio para agregar un estudiante, listar todos los estudiantes y obtener detalles de un estudiante por su ID.

### Resumen:
Este ejemplo muestra cómo usar interfaces para definir un contrato que clases deben seguir, asegurando consistencia y claridad en la implementación de funcionalidades. La separación entre la lógica de negocio y la conexión a la base de datos también ayuda a mantener el código organizado y más fácil de mantener.

### Vista en el navegador:

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/interfaces.png?raw=true" alt="Imagen" style />
</p>
<br>

## Diferencias entre las clases abstractas y las interfaces
Este cuadro resume las principales diferencias entre clases abstractas e interfaces, facilitando la elección según las necesidades del diseño en PHP.

| **Aspecto**             | **Clases Abstractas**                                           | **Interfaces**                                              |
|-------------------------|-----------------------------------------------------------------|-------------------------------------------------------------|
| **Definición**          | Plantillas para clases que pueden incluir métodos abstractos y concretos. | Contratos que las clases deben cumplir; solo pueden tener métodos abstractos. |
| **Métodos**             | Pueden tener métodos abstractos (sin cuerpo) y métodos concretos (con cuerpo). | Solo pueden tener métodos sin cuerpo (abstractos) hasta PHP 8. PHP 8 permite métodos con cuerpo. |
| **Propiedades**         | Pueden definir propiedades con visibilidad (p. ej., `protected`, `private`). | No pueden definir propiedades; solo constantes.             |
| **Herencia**            | Una clase solo puede heredar de una clase abstracta.            | Una clase puede implementar múltiples interfaces.           |
| **Uso de `abstract`**   | Los métodos abstractos deben declararse con la palabra clave `abstract`. | No se usa `abstract` para los métodos en las interfaces.    |
| **Constructor**         | Pueden tener un constructor.                                    | No pueden tener un constructor.                             |
| **Compatibilidad**      | Usadas cuando se necesita compartir código común entre clases relacionadas. | Usadas para garantizar que las clases sigan un contrato específico sin compartir implementación. |
| **Visibilidad de métodos** | Los métodos pueden tener cualquier visibilidad (`public`, `protected`, `private`). | Los métodos son siempre `public` (implícitamente).          |
| **Extensibilidad**      | Pueden extender de una sola clase abstracta.                   | Las clases pueden implementar múltiples interfaces.         |
| **Constantes**          | Pueden tener constantes.                                        | Pueden tener constantes.                                    |


# Miembros estáticos
Son propiedades y métodos que pertenecen a la clase en lugar de a una instancia específica de la clase. Esto significa que puedes acceder a ellos sin tener que crear un objeto de la clase. 

## Definición de miembros estáticos
### 1. Propiedades Estáticas: 
Se definen usando la palabra clave ```static``` y se accede a ellas a través del operador de resolución de ámbito (```::```).
### 2. Métodos Estáticos: 
También se definen con ```static``` y pueden ser llamados sin crear una instancia de la clase.

## Ejemplo de miembros estáticos
Vamos a crear una clase llamada ```Contador``` que tendrá un miembro estático para contar cuántas instancias de la clase se han creado.

### 1. Estructura de archivos
```php
/miembros_estaticos
│
├── Contador.php
└── index.php
```

### 2. Contador: ```Contador.php```

```php
<?php

class Contador {
    private static $contador = 0;

    // Constructor
    public function __construct() {
        self::$contador++; 
    }

    public static function obtenerContador() {
        return self::$contador;
    }
}
```
### 3. Punto de entrada: ```index.php```

```php
<?php

require_once 'Contador.php';

$nuevaInstancia1 = new Contador();
$nuevaInstancia2 = new Contador();
$nuevaInstancia3 = new Contador();

echo "Número de instancias creadas: " . Contador::obtenerContador(); 
```

### Explicación:
1. **Propiedad Estática:** ```private static $contador = 0```; es una propiedad estática que mantiene el conteo de las instancias.
2. **Constructor:** Cada vez que se crea una nueva instancia de ```Contador```, se incrementa la propiedad estática ```$contador```.
3. **Método Estático:** ```obtenerContador()``` es un método estático que devuelve el número total de instancias creadas. Se accede a él utilizando ```Contador::obtenerContador()```.

### Consideraciones:
* Los miembros estáticos son compartidos por todas las instancias de la clase.
* No se puede acceder a miembros no estáticos desde un contexto estático.
* Útil para datos que son comunes a todas las instancias, como contadores, configuraciones, o métodos de utilidad.

### Vista en el navegador:

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/miembros_estaticos.png?raw=true" alt="Imagen width="400" style />
</p>
<br>

# Patrones de diseño (GOF)
"Gang of Four" (GOF) son un conjunto de soluciones reutilizables a problemas comunes en el desarrollo de software. Estos patrones se dividen en tres categorías: 

* **Creacionales.**
* **Estructurales.**
* **De comportamiento.**

## 1. Patrones creacionales
Estos patrones se centran en la creación de objetos, proporcionando mecanismos adecuados para crear instancias de clases.

### 1.1 Singleton
Asegura que una clase tenga una única instancia y proporciona un punto de acceso global a ella.

```php
class Singleton {
    private static $instance;

    private function __construct() {}

    public static function getInstance() {
        if (null === self::$instance) {
            self::$instance = new self();
        }
        return self::$instance;
    }
}
```
### 1.2 Factory Method
Define una interfaz para crear un objeto, pero permite a las subclases decidir qué clase instanciar.

```php
abstract class Product {
    abstract public function operation();
}

class ConcreteProductA extends Product {
    public function operation() {
        return "Resultado de la operación A";
    }
}

class Creator {
    abstract public function factoryMethod(): Product;

    public function someOperation() {
        $product = $this->factoryMethod();
        return $product->operation();
    }
}

class ConcreteCreatorA extends Creator {
    public function factoryMethod(): Product {
        return new ConcreteProductA();
    }
}
```

### 1.3 Abstract Factory
Proporciona una interfaz para crear familias de objetos relacionados sin especificar sus clases concretas.

```php
interface AbstractFactory {
    public function createProductA(): ProductA;
    public function createProductB(): ProductB;
}

class ConcreteFactory1 implements AbstractFactory {
    public function createProductA(): ProductA {
        return new ConcreteProductA1();
    }

    public function createProductB(): ProductB {
        return new ConcreteProductB1();
    }
}
```
## 2. Patrones estructurales
Estos patrones se centran en la composición de clases y objetos, creando relaciones entre ellos.

### 2.1 Adapter
Permite que clases con interfaces incompatibles trabajen juntas.

```php
class Adaptee {
    public function specificRequest() {
        return "Solicitud específica";
    }
}

interface Target {
    public function request();
}

class Adapter implements Target {
    private $adaptee;

    public function __construct(Adaptee $adaptee) {
        $this->adaptee = $adaptee;
    }

    public function request() {
        return $this->adaptee->specificRequest();
    }
}
```
### 2.2 Composite
Permite a los clientes tratar objetos individuales y composiciones de objetos de manera uniforme.

```php
interface Component {
    public function operation();
}

class Leaf implements Component {
    public function operation() {
        return "Hoja";
    }
}

class Composite implements Component {
    private $children = [];

    public function add(Component $component) {
        $this->children[] = $component;
    }

    public function operation() {
        return "Composite: " . implode(", ", array_map(function($child) {
            return $child->operation();
        }, $this->children));
    }
}
```
### 2.3 Decorator
Permite agregar funcionalidad a objetos de manera dinámica.

```php
interface Coffee {
    public function cost();
}

class SimpleCoffee implements Coffee {
    public function cost() {
        return 5;
    }
}

class MilkDecorator implements Coffee {
    private $coffee;

    public function __construct(Coffee $coffee) {
        $this->coffee = $coffee;
    }

    public function cost() {
        return $this->coffee->cost() + 2;
    }
}
```

## 3. Patrones de comportamiento
Estos patrones se centran en la comunicación entre objetos.

### 3.1 Observer
Define una relación de dependencia uno a muchos entre objetos de manera que cuando uno cambia de estado, todos sus dependientes son notificados.

```php
class Subject {
    private $observers = [];

    public function attach(Observer $observer) {
        $this->observers[] = $observer;
    }

    public function notify() {
        foreach ($this->observers as $observer) {
            $observer->update();
        }
    }
}

interface Observer {
    public function update();
}
```

### 3.2 Strategy
Define una familia de algoritmos, encapsula cada uno y los hace intercambiables.

```php
interface Strategy {
    public function execute();
}

class ConcreteStrategyA implements Strategy {
    public function execute() {
        return "Ejecución de la estrategia A";
    }
}

class Context {
    private $strategy;

    public function __construct(Strategy $strategy) {
        $this->strategy = $strategy;
    }

    public function doSomething() {
        return $this->strategy->execute();
    }
}
```

### 3.2 Command
Encapsula una solicitud como un objeto, permitiendo parametrizar clientes con diferentes solicitudes.

```php
interface Command {
    public function execute();
}

class Light {
    public function turnOn() {
        echo "La luz está encendida.";
    }
}

class LightOnCommand implements Command {
    private $light;

    public function __construct(Light $light) {
        $this->light = $light;
    }

    public function execute() {
        $this->light->turnOn();
    }
}
```

### Conclusión:
> Los patrones de diseño son herramientas valiosas que pueden mejorar la calidad y la flexibilidad del código. La elección de un patrón depende del contexto del problema que se está resolviendo. 

# Introducción a sistemas distribuidos
Es un conjunto de ordenadores que se presentan a los usuarios como un solo sistema coherente. Estos ordenadores están interconectados a través de una red y pueden estar ubicados en diferentes lugares geográficos. El objetivo principal de un sistema distribuido es trabajar juntos para resolver problemas o proporcionar servicios de manera eficiente y fiable.

## Características principales

### 1. Descentralización: 
No hay un solo punto de control. Las decisiones y el procesamiento se distribuyen entre varias máquinas.
### 2. Transparencia: 
Los usuarios deben poder interactuar con el sistema sin tener que preocuparse por la ubicación de los recursos o los detalles de la comunicación. La transparencia puede ser de acceso, ubicación, replicación y migración.
### 3. Escalabilidad: 
Un sistema distribuido debe ser capaz de crecer, ya sea añadiendo más máquinas o aumentando los recursos de las existentes.
### 4. Confiabilidad: 
Los sistemas deben ser capaces de continuar funcionando correctamente incluso en caso de fallos de hardware o software.
### 5. Concurrencia: 
Múltiples procesos pueden ejecutarse al mismo tiempo en diferentes nodos, lo que permite una mayor eficiencia y velocidad.

## Tipos de sistemas distribuidos
### 1. Sistemas de archivos distribuidos: 
Permiten que múltiples usuarios accedan y compartan archivos desde diferentes ubicaciones (ejemplo: NFS, HDFS).
### 2. Sistemas de bases de datos distribuidos: 
Permiten el almacenamiento y la gestión de datos en múltiples ubicaciones (ejemplo: Google Spanner, Amazon DynamoDB).
### 3. Sistemas de computación en la nube: 
Proporcionan recursos informáticos a través de Internet, permitiendo el acceso y la escalabilidad (ejemplo: AWS, Google Cloud).
### 4. Sistemas de mensajería: 
Facilitan la comunicación entre diferentes componentes de un sistema distribuido (ejemplo: Apache Kafka, RabbitMQ).

## Desafíos en sistemas distribuidos
### 1. Fallo de nodos: 
Manejar la recuperación ante fallos es crucial para garantizar la fiabilidad.
### 2. Sincronización: 
Coordinar el acceso a recursos compartidos y garantizar la coherencia de los datos.
### 3. Latencia y ancho de banda: 
La comunicación entre nodos puede ser lenta y limitada por el ancho de banda de la red.
### 4. Seguridad: 
Proteger los datos en tránsito y en reposo, y asegurar que solo los usuarios autorizados tengan acceso a los recursos.

# Desarrollo de una API REST
Una API REST (Representational State Transfer) es un conjunto de convenciones y principios para la creación de servicios web que permiten la comunicación entre diferentes aplicaciones a través de HTTP. 

## Características de una API REST
### 1. Recursos: 
En REST, los recursos son objetos o representaciones de datos que se pueden identificar mediante URL. Cada recurso tiene una URI (Uniform Resource Identifier) única.

### 2. Métodos HTTP: 

REST utiliza los métodos HTTP para realizar operaciones sobre los recursos:
* **GET:** Recuperar información sobre un recurso.
* **POST:** Crear un nuevo recurso.
* **PUT:** Actualizar un recurso existente.
* **DELETE:** Eliminar un recurso.

### 3. Estado: 
REST es stateless (sin estado), lo que significa que cada solicitud del cliente al servidor debe contener toda la información necesaria para que el servidor pueda entender y procesar la solicitud. No se guarda información del cliente entre solicitudes.

### 4. Representaciones: 
Los recursos pueden ser representados en diferentes formatos, como JSON, XML, HTML, etc. El formato más común es JSON debido a su simplicidad y facilidad de uso en aplicaciones web.

### 5. Interoperabilidad: 
Las APIs REST están diseñadas para ser utilizadas por diferentes clientes (navegadores web, aplicaciones móviles, etc.) independientemente de la plataforma.

### 6. Cacheabilidad: 
Las respuestas de las APIs REST pueden ser almacenadas en caché para mejorar el rendimiento y reducir la carga en el servidor.

## Ventajas de usar una API REST:
* **Simplicidad:** Utiliza protocolos y estándares ampliamente utilizados (HTTP).
* **Escalabilidad:** Su naturaleza sin estado facilita la escalabilidad horizontal.
* **Flexibilidad:** Puede ser consumida por diferentes tipos de clientes.
* **Desacoplamiento:** Permite que el frontend y el backend sean desarrollados y escalados de manera independiente.

## Ejemplo completo de gestión de un CRUD con API REST 
Este ejemplo presenta una API REST en PHP para una veterinaria que permite gestionar un CRUD (Crear, Leer, Actualizar y Eliminar) de las mascotas. A través de una interfaz HTML, los usuarios podrán interactuar con la API para agregar nuevas mascotas, visualizar la lista de mascotas existentes, actualizar la información de una mascota y eliminar registros.

### 1. Estructura de directorios y archivos

```php
veterinaria-guau/
│
├── index.php
├── db.php
├── mascotas.php
├── agregar.php
├── editar.php
└── eliminar.php
```

### 2. Creación de la base de datos

```sql```

```php
CREATE DATABASE IF NOT EXISTS veterinaria;

USE veterinaria;

CREATE TABLE IF NOT EXISTS mascotas (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    especie VARCHAR(100) NOT NULL
);
```

### 3. Punto de entrada de la aplicación
**Archivo:** ```index.php```

```php
<?php
include_once 'db.php';
include_once 'mascotas.php';

$mascotas = obtenerMascotas();

?>

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Veterinaria - Lista de Mascotas</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <h1>Lista de Mascotas</h1>
    <a href="agregar.php">Agregar Nueva Mascota</a>
    <table>
        <tr>
            <th>ID</th>
            <th>Nombre</th>
            <th>Especie</th>
            <th>Acciones</th>
        </tr>
        <?php foreach ($mascotas as $mascota): ?>
            <tr>
                <td><?php echo $mascota['id']; ?></td>
                <td><?php echo $mascota['nombre']; ?></td>
                <td><?php echo $mascota['especie']; ?></td>
                <td>
                    <a href="editar.php?id=<?php echo $mascota['id']; ?>">Editar</a>
                    <a href="eliminar.php?id=<?php echo $mascota['id']; ?>">Eliminar</a>
                </td>
            </tr>
        <?php endforeach; ?>
    </table>
</body>
</html>
```

### Explicación:
* Se incluyen ```db.php``` y ```mascotas.php``` para manejar la conexión a la base de datos y las operaciones relacionadas con las mascotas.
* Se obtienen todas las mascotas de la base de datos.
* Se muestra una tabla con las mascotas y opciones para agregar, editar y eliminar.

### 4. Conexión a las base de datos
**Archivo:** ```db.php```

```php
<?php
$host = 'localhost';
$db = 'veterinaria';
$user = 'root';
$pass = '';

function conectarDB() {
    global $host, $db, $user, $pass;
    $dsn = "mysql:host=$host;dbname=$db;charset=utf8mb4";
    return new PDO($dsn, $user, $pass);
}
?>
```

### Explicación:
* Se definen las variables para la conexión a la base de datos.
* La función ```conectarDB()``` devuelve una conexión PDO a la base de datos.

### 5. Crear funciones con las operaciones CRUD 
**Archivo:** ```mascotas.php``

```php
<?php
include_once 'db.php';

function obtenerMascotas() {
    $db = conectarDB();
    $stmt = $db->query('SELECT * FROM mascotas');
    return $stmt->fetchAll(PDO::FETCH_ASSOC);
}

function agregarMascota($nombre, $especie) {
    $db = conectarDB();
    $stmt = $db->prepare("INSERT INTO mascotas (nombre, especie) VALUES (:nombre, :especie)");
    $stmt->execute(['nombre' => $nombre, 'especie' => $especie]);
}

function obtenerMascota($id) {
    $db = conectarDB();
    $stmt = $db->prepare("SELECT * FROM mascotas WHERE id = :id");
    $stmt->execute(['id' => $id]);
    return $stmt->fetch(PDO::FETCH_ASSOC);
}

function editarMascota($id, $nombre, $especie) {
    $db = conectarDB();
    $stmt = $db->prepare("UPDATE mascotas SET nombre = :nombre, especie = :especie WHERE id = :id");
    $stmt->execute(['nombre' => $nombre, 'especie' => $especie, 'id' => $id]);
}

function eliminarMascota($id) {
    $db = conectarDB();
    $stmt = $db->prepare("DELETE FROM mascotas WHERE id = :id");
    $stmt->execute(['id' => $id]);
}
?>
```

### Explicación:
* Se definen funciones para obtener todas las mascotas, agregar una nueva mascota, obtener una mascota específica, editar y eliminar mascotas. Cada función interactúa con la base de datos usando PDO.

### 6. Agregar nuevas mascotas
**Archivo:** ```agregar.php```

```php
<?php
include_once 'mascotas.php';

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $nombre = $_POST['nombre'];
    $especie = $_POST['especie'];
    agregarMascota($nombre, $especie);
    header('Location: index.php');
}
?>

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Agregar Mascota</title>
</head>
<body>
    <h1>Agregar Nueva Mascota</h1>
    <form method="POST">
        <label for="nombre">Nombre:</label>
        <input type="text" name="nombre" required>
        <br>
        <label for="especie">Especie:</label>
        <input type="text" name="especie" required>
        <br>
        <input type="submit" value="Agregar">
    </form>
</body>
</html>
```
### Explicación:
* Si se recibe una solicitud ```POST```, se agregan los datos de la nueva mascota a la base de datos.
* Se muestra un formulario para ingresar el nombre y la especie de la mascota.

### 7. Editar la información de una mascota
**Archivo:** ```editar.php```

```php
<?php
include_once 'mascotas.php';

$mascota = null;

if (isset($_GET['id'])) {
    $mascota = obtenerMascota($_GET['id']);
}

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $nombre = $_POST['nombre'];
    $especie = $_POST['especie'];
    editarMascota($_GET['id'], $nombre, $especie);
    header('Location: index.php');
}
?>

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Editar Mascota</title>
</head>
<body>
    <h1>Editar Mascota</h1>
    <?php if ($mascota): ?>
        <form method="POST">
            <label for="nombre">Nombre:</label>
            <input type="text" name="nombre" value="<?php echo $mascota['nombre']; ?>" required>
            <br>
            <label for="especie">Especie:</label>
            <input type="text" name="especie" value="<?php echo $mascota['especie']; ?>" required>
            <br>
            <input type="submit" value="Actualizar">
        </form>
    <?php else: ?>
        <p>No se encontró la mascota.</p>
    <?php endif; ?>
</body>
</html>
```
### Explicación:
* Se obtiene la mascota a editar a través de su ```ID```.
* Si se recibe una solicitud ```POST```, se actualiza la información de la mascota.
* Se muestra un formulario pre-llenado con los datos de la mascota.

### 8. Eliminar la información de una mascota
**Archivo:** ```eliminar.php```

```php
<?php
include_once 'mascotas.php';

if (isset($_GET['id'])) {
    eliminarMascota($_GET['id']);
    header('Location: index.php');
}
?>
```

### Explicación:
* Si se recibe un ```ID``` a través de la URL, se llama a la función para eliminar la mascota y se redirige al usuario de vuelta a la lista de mascotas.

### Resumen:
Este ejemplo de API REST en PHP para una veterinaria presenta un CRUD completo para gestionar mascotas. La estructura modular del código facilita la extensión y el mantenimiento. Cada archivo tiene una función específica, desde la gestión de la base de datos hasta la presentación de la interfaz HTML.

### Conclusión:
> La implementación de una API REST utilizando PHP y HTML permite crear aplicaciones web interactivas y dinámicas que pueden manejar datos de manera eficiente. La modularidad y organización del código son esenciales para mantener un desarrollo ordenado y escalable.



