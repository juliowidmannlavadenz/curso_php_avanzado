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
# Miembros estáticos, patrones de diseño (GOF)
# Introducción a sistemas distribuidos
# Desarrollo de una API REST


