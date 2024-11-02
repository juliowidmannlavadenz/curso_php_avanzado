# PDO Que es PDO (PHP Data Objects) 
Es una extensión en PHP que proporciona una interfaz uniforme para acceder a bases de datos. Permite trabajar con diferentes sistemas de bases de datos (como MySQL, PostgreSQL, SQLite, entre otros) utilizando el mismo conjunto de funciones, lo que facilita la portabilidad de la aplicación si decides cambiar el sistema de base de datos en el futuro.

## Características de PDO:

### 1. Abstracción de base de datos: 
PDO permite cambiar de un sistema de base de datos a otro sin modificar gran parte del código, ya que la sintaxis de las consultas SQL permanece igual, y solo se cambia el controlador en la conexión.

### Ejemplo: Clase de Abstracción de base de datos  con PDO
Este código define una clase Database que se conecta a distintas bases de datos, permitiendo cambiar solo los datos de conexión:

```php
<?php
class Database {
    private $pdo;

    public function __construct($driver, $host, $dbname, $username, $password) {
        // Crear conexión usando el controlador PDO
        $dsn = "$driver:host=$host;dbname=$dbname";
        $this->pdo = new PDO($dsn, $username, $password);
        $this->pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
    }

    public function query($sql, $params = []) {
        $stmt = $this->pdo->prepare($sql);
        $stmt->execute($params);
        return $stmt->fetchAll(PDO::FETCH_ASSOC);
    }
}

// Ejemplo de uso con MySQL
$db = new Database("mysql", "localhost", "nombre_bd", "usuario", "contraseña");
$resultado = $db->query("SELECT nombre FROM usuarios WHERE id = ?", [1]);
echo $resultado[0]['nombre'];

// Ejemplo de uso con PostgreSQL
$db_pg = new Database("pgsql", "localhost", "nombre_bd", "usuario", "contraseña");
$resultado_pg = $db_pg->query("SELECT nombre FROM usuarios WHERE id = ?", [1]);
echo $resultado_pg[0]['nombre'];
?>
```

### Explicación:

1. **Clase Database:** Abstrae la conexión y el acceso a bases de datos.
2. **Constructor:** Recibe el tipo de base de datos ($driver), host, nombre de la base de datos, usuario y contraseña.
3. **Método** ```query```: Permite ejecutar consultas parametrizadas para evitar inyecciones SQL.
4. Uso con diferentes bases de datos: Simplemente cambiando el ```$driver``` (```mysql```, ```pgsql```, etc.), puedes usar la misma clase para conectar a distintos sistemas de bases de datos sin cambiar el resto del código.

* Este enfoque permite que, si necesitas cambiar de MySQL a PostgreSQL, solo debas cambiar el valor del ```$driver``` al crear una instancia de ```Database```.

### 2. Soporte para múltiples bases de datos:
Esta tabla muestra los controladores de PDO correspondientes y una breve descripción para cada base de datos que PDO soporta de forma nativa en PHP.

| Base de Datos           | Driver PDO           | Descripción                                         |
|-------------------------|----------------------|-----------------------------------------------------|
| MySQL                   | `mysql`              | Soporte para bases de datos MySQL                   |
| PostgreSQL              | `pgsql`              | Soporte para bases de datos PostgreSQL              |
| SQLite                  | `sqlite`             | Soporte para bases de datos SQLite                  |
| Microsoft SQL Server    | `sqlsrv`             | Soporte para SQL Server en sistemas Windows         |
| IBM DB2                 | `ibm`                | Soporte para bases de datos IBM DB2                 |
| Informix                | `informix`           | Soporte para bases de datos Informix                |
| Firebird/InterBase      | `firebird`           | Soporte para bases de datos Firebird/InterBase      |
| Oracle                  | `oci`                | Soporte para bases de datos Oracle                  |
| ODBC                    | `odbc`               | Soporte para conexión a bases de datos mediante ODBC|
| Cubrid                  | `cubrid`             | Soporte para bases de datos Cubrid                  |
| 4D                      | `4d`                 | Soporte para bases de datos 4D                      |


### 3. Prepared Statements (Consultas preparadas):
Son una técnica para ejecutar consultas SQL de forma segura y eficiente, especialmente cuando se trabaja con datos de usuarios en aplicaciones web o de escritorio. Esta técnica se utiliza para prevenir ataques de inyección SQL (ya que permite separar los datos de la consulta) y mejorar el rendimiento en bases de datos.

### Funcionamiento de los Prepared Statements
En una consulta preparada, el proceso se divide en dos fases:

1. **Preparación:**
* La consulta se envía a la base de datos con marcadores de posición (placeholders) en lugar de los datos específicos.

```sql```

```php
SELECT * FROM usuarios WHERE nombre = ? AND edad = ?
```

* Aquí, los signos de interrogación (```?```) son los marcadores de posición para los valores que se proporcionarán después.

2. **Ejecución:**
* Se proporcionan los valores específicos de los marcadores de posición, y la base de datos ejecuta la consulta.
* En este proceso, los valores se vinculan a los placeholders, de modo que la base de datos trata estos valores solo como datos y no como parte de la lógica de la consulta.

### Ejemplo de uso de Prepared Statements

```php
$stmt = $pdo->prepare("SELECT * FROM usuarios WHERE id = :id");
$stmt->execute(['id' => $id]);
$result = $stmt->fetchAll();
```

### Explicación:

1. **Preparar la consulta:** ```$pdo->prepare("SELECT * FROM usuarios WHERE id = :id");``` crea una consulta con un marcador de posición ```:id```.
2. **Ejecutar la consulta:** ```$stmt->execute(['id' => $id]);``` reemplaza ```:id``` por el valor de $id y ejecuta la consulta.
3. **Obtener resultados:** ```$stmt->fetchAll()```; recupera todos los registros que coinciden y los almacena en ```$result```.

### 4. Transacciones:
Es un conjunto de operaciones que se ejecutan como una unidad indivisible. Esto significa que todas las operaciones dentro de la transacción deben completarse con éxito para que los cambios se confirmen en la base de datos. Si alguna de las operaciones falla, la transacción se cancela y la base de datos vuelve a su estado original, garantizando que no haya datos incompletos o inconsistentes.

PDO soporta transacciones, permitiendo ejecutar una serie de consultas de forma segura, revirtiendo los cambios en caso de fallo.

### Propiedades de las Transacciones: ACID

Las transacciones deben cumplir con cuatro propiedades conocidas como ACID:

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/acid.png?raw=true" style />
</p>
<br>


1. **Atomicidad (Atomicity):** Una transacción es una operación "todo o nada". Si alguna parte de la transacción falla, toda la transacción se revierte (rollback), y no se guardan cambios parciales en la base de datos.

2. **Consistencia (Consistency):** Una transacción lleva la base de datos de un estado válido a otro estado válido, manteniendo reglas y restricciones definidas, como claves primarias, claves foráneas, etc.

3. **Aislamiento (Isolation):** Las transacciones se ejecutan de forma aislada entre sí, de modo que los resultados intermedios no son visibles para otras transacciones hasta que la transacción actual se complete. Esto evita conflictos y asegura que las transacciones no se afecten entre ellas.

4. **Durabilidad (Durability):** Una vez que una transacción se completa y confirma, sus cambios persisten en la base de datos, incluso si ocurre una falla del sistema.

### Ejemplo de transacción

```php
$pdo->beginTransaction();
$pdo->exec("INSERT INTO usuarios (nombre) VALUES ('Juan')");
$pdo->commit();
```

### Explicación

* En este código, ```beginTransaction()``` inicia una transacción, ```exec()``` ejecuta una consulta SQL, y ```commit()``` confirma la transacción, haciendo permanentes los cambios en la base de datos.

# Manejo de errores con PDO

# Conexión con PDO

Una conexión con PDO  es una forma de interactuar con bases de datos en PHP utilizando una interfaz orientada a objetos. PDO proporciona una manera consistente y segura de acceder a varias bases de datos, como MySQL, PostgreSQL, SQLite, entre otras.

## Elementos de una conexión
Para establecer una conexión, PDO requiere tres elementos:

### 1. DSN (Data Source Name): 
* Define el tipo de base de datos y otros detalles de conexión.

### 2. Usuario: 
* Nombre de usuario para acceder a la base de datos.

### 3. Contraseña: 
* Clave de acceso al sistema de base de datos.

## Ejemplo básico de una conexión

```php
<?php
$dsn = 'mysql:host=localhost;dbname=testdb';
$username = 'admin_user';
$password = '1[MtFTvccbIEvcIu';

try {
    $pdo = new PDO($dsn, $username, $password);
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
    echo "Conexión exitosa<br>Bienvenido";
} catch (PDOException $e) {
    echo "Error en la conexión:<br>" . $e->getMessage();
}
?>
```

### Explicación:
* Establecemos una conexión a una base de datos MySQL usando PDO. Utilizamos un bloque ```try-catch``` para manejar posibles errores de conexión, configurando un modo de error que lanza excepciones, lo que permite un manejo más seguro y efectivo de errores en la conexión.

### Ejecutando el servidor integrado 
* Usamos el siguiente comando para iniciar el servidor:

```php
php -S localhost:8000
```

### Vista en el navegador

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/conexion.png?raw=true" style />
</p>
<br>

## Ejemplos de conexiones PDO

### 1. MySQL
```php
$dsn = 'mysql:host=localhost;dbname=nombreBD;charset=utf8mb4';
$username = 'usuario';
$password = 'contraseña';
```
### 2. PostgreSQL
```php
$dsn = 'pgsql:host=localhost;port=5432;dbname=nombreBD;user=usuario;password=contraseña';
```

### 3. SQLite
```php
$dsn = 'sqlite:/ruta/al/archivo.db';
```
* Para una base de datos SQLite, la cadena de conexión apunta directamente al archivo de la base de datos.

### 4. SQL Server
```php
$dsn = 'sqlsrv:Server=localhost;Database=nombreBD';
$username = 'usuario';
$password = 'contraseña';
```

### 5. Oracle
```php
$dsn = 'oci:dbname=//localhost:1521/nombreBD';
$username = 'usuario';
$password = 'contraseña';
```

### 6. IBM Db2
```php
$dsn = 'ibm:DRIVER={IBM DB2 ODBC DRIVER};DATABASE=nombreBD;HOSTNAME=localhost;PORT=50000;PROTOCOL=TCPIP;';
$username = 'usuario';
$password = 'contraseña';
```

### 7. Firebird
```php
$dsn = 'firebird:dbname=/ruta/al/archivo.fdb';
$username = 'usuario';
$password = 'contraseña';
```

# Consultas con PDO
## Definición
Es una operación que permite ejecutar sentencias SQL en la base de datos y obtener resultados. 

## Ventajas de usar consultas PDO
### 1. Compatibilidad con múltiples bases de datos: 
PDO soporta múltiples sistemas de gestión de bases de datos como MySQL, PostgreSQL, SQLite, SQL Server, etc. Esto permite cambiar de base de datos sin necesidad de modificar significativamente el código.

### 2. Protección contra inyecciones SQL: 
Al usar consultas preparadas, PDO ayuda a proteger contra ataques de inyección SQL, ya que los parámetros de las consultas son tratados de forma segura.

### 3. Mejor manejo de errores: 
PDO ofrece un control más robusto sobre el manejo de errores a través de excepciones (PDOException). Esto permite capturar errores y manejar las fallas de forma controlada.

### 4. Soporte de consultas preparadas: 
Las consultas preparadas en PDO son más seguras y eficientes, ya que separan la lógica de la consulta de los datos que se envían. Esto también ayuda a mejorar el rendimiento, ya que las consultas preparadas pueden ser reutilizadas.

### 5. Flexibilidad en el manejo de resultados: 
PDO permite obtener resultados en distintos formatos, como objetos, arrays asociativos, etc., proporcionando flexibilidad al momento de procesar los datos.

### 6. Mayor consistencia: 
Usar una única clase para todas las conexiones y consultas de la base de datos aporta consistencia al proyecto, reduciendo el esfuerzo de aprendizaje y el código redundante.

### 7. Manejo de transacciones: 
PDO tiene soporte nativo para transacciones, lo que permite realizar operaciones complejas que pueden ser confirmadas o revertidas de forma sencilla con ```beginTransaction()```, ```commit()``` y ```rollBack()```.

## Tipos de consultas

### 1. Consultas de selección (```SELECT```)
Permiten obtener datos de la base de datos.

**Ejemplo: Consultas básicas con** ```SELECT```

```php
<?php
$dsn = 'mysql:host=localhost;dbname=ejemplo';
$usuario = 'root';
$contrasena = '';

try {
    $conexion = new PDO($dsn, $usuario, $contrasena);
    $conexion->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

    // Consulta simple sin parámetros
    $stmt = $conexion->query('SELECT * FROM usuarios');
    $resultado = $stmt->fetchAll(PDO::FETCH_ASSOC);

    foreach ($resultado as $fila) {
        echo 'Nombre: ' . $fila['nombre'] . '<br>';
    }
} catch (PDOException $e) {
    echo 'Error: ' . $e->getMessage();
}
?>
```

* Este script conecta a una base de datos MySQL, ejecuta una consulta para obtener todos los registros de la tabla ```usuarios``` y muestra los nombres de los usuarios en la página. Si hay un error en la conexión o la consulta, lo muestra en pantalla.

**Ejemplo: Consulta preparada con parámetros** 

```php
<?php
$id = 1;
$stmt = $conexion->prepare('SELECT * FROM usuarios WHERE id = :id');
$stmt->bindParam(':id', $id, PDO::PARAM_INT);
$stmt->execute();
$resultado = $stmt->fetch(PDO::FETCH_ASSOC);
print_r($resultado);
?>
```

* Este script selecciona un registro de la tabla usuarios cuyo id es igual a 1, lo ejecuta de forma segura usando sentencias preparadas y muestra el resultado en pantalla.

### 2. Consultas de inserción (```INSERT```)
Estas consultas se utilizan para agregar datos nuevos a la base de datos.

**Ejemplo:** ```INSERT``` **con parámetros**

```php
<?php
$nombre = 'Juan';
$email = 'juan@example.com';

$stmt = $conexion->prepare('INSERT INTO usuarios (nombre, email) VALUES (:nombre, :email)');
$stmt->bindParam(':nombre', $nombre);
$stmt->bindParam(':email', $email);
$stmt->execute();

echo 'Usuario insertado con éxito';
?>
```
* insertamos un nuevo registro en la tabla ```usuarios``` con el nombre y el email especificados, usando una consulta preparada para evitar inyecciones SQL y asegurar una inserción segura.

### 3. Consultas de actualización (```UPDATE```)
Se utilizan para modificar datos existentes.

**Ejemplo:** ```UPDATE``` **con parámetros**

```php
<?php
$id = 1;
$nuevoNombre = 'Juan Actualizado';

$stmt = $conexion->prepare('UPDATE usuarios SET nombre = :nombre WHERE id = :id');
$stmt->bindParam(':nombre', $nuevoNombre);
$stmt->bindParam(':id', $id, PDO::PARAM_INT);
$stmt->execute();

echo 'Usuario actualizado con éxito';
?>
```

* Actualizamos el campo ```nombre``` de un registro en la tabla ```usuarios```, donde ```id``` es igual a ```1```, usando una consulta preparada para mayor seguridad y eficiencia.

### 4. Consultas de eliminación (```DELETE```)
Se utilizan para modificar datos existentes.

**Ejemplo:** ```DELETE``` **con parámetros**

```php
<?php
$id = 1;

$stmt = $conexion->prepare('DELETE FROM usuarios WHERE id = :id');
$stmt->bindParam(':id', $id, PDO::PARAM_INT);
$stmt->execute();

echo 'Usuario eliminado con éxito';
?>
```

*  Eliminamos de forma segura un registro de la tabla ```usuarios``` cuyo ```id``` es ```1```, utilizando una consulta preparada para proteger contra inyecciones SQL.

### 5. Consultas con transacciones
Se usan para realizar varias operaciones como una sola transacción. Si una de las operaciones falla, todas las demás también se deshacen.

**Ejemplo: Uso de transacciones** 

```php
<?php
try {
    $conexion->beginTransaction();

    $stmt1 = $conexion->prepare('INSERT INTO usuarios (nombre, email) VALUES (:nombre, :email)');
    $stmt1->execute([':nombre' => 'Pedro', ':email' => 'pedro@example.com']);

    $stmt2 = $conexion->prepare('UPDATE usuarios SET nombre = :nombre WHERE id = :id');
    $stmt2->execute([':nombre' => 'Pedro Actualizado', ':id' => 2]);

    $conexion->commit();
    echo 'Transacción completada con éxito';
} catch (PDOException $e) {
    $conexion->rollBack();
    echo 'Error en la transacción: ' . $e->getMessage();
}
?>
```
* Este script realiza una transacción en la base de datos que inserta un nuevo usuario y actualiza otro. Si ambas operaciones son exitosas, se confirma la transacción; si hay un error, se revierte. Esto garantiza la consistencia de los datos en la base de datos.


### 6. Consultas usando ```execute()``` con arrays
Una forma rápida de pasar los parámetros a una consulta.

**Ejemplo: Uso de** ```execute()``` **con arrays**

```php
<?php
$stmt = $conexion->prepare('SELECT * FROM usuarios WHERE nombre = ? AND email = ?');
$stmt->execute(['Juan', 'juan@example.com']);
$resultado = $stmt->fetchAll(PDO::FETCH_ASSOC);
print_r($resultado);
?>
```
* Buscamos en la tabla ```usuarios``` registros donde el nombre sea ```'Juan'``` y el email sea ```'juan@example.com'```, y luego imprimimos los resultados encontrados en un formato legible.

### 7. Consultas de Solo Lectura (```SELECT``` ```COUNT```, ```SUM```, ```AVG```)
Se usan para obtener estadísticas de la base de datos.

**Ejemplo:** ```SELECT COUNT``` 

```php
<?php
$stmt = $conexion->query('SELECT COUNT(*) AS total FROM usuarios');
$totalUsuarios = $stmt->fetch(PDO::FETCH_ASSOC);
echo 'Total de usuarios: ' . $totalUsuarios['total'];
?>
```

* Este script cuenta y muestra el número total de usuarios en la tabla usuarios, imprimiendo el resultado en un formato legible.

### Resumen:
* ```SELECT```: Para obtener datos.
* ```INSERT```: Para insertar datos.
* ```UPDATE```: Para actualizar datos.
* ```DELETE```: Para eliminar datos.
* Transacciones: Para ejecutar múltiples consultas de manera segura.
* ```execute()``` con arrays: Para pasar parámetros de forma simplificada.

## Ejemplo completo: Crud utilizando PDO 
Construiremos un sistema de gestión de una librería (CRUD)utilizando PDO. Este sistema permitirá realizar operaciones de creación, lectura, actualización y eliminación de libros. Incluiremos las siguientes caracteristicas.
* Abstracción: Crearemos una clase base para manejar la conexión a la base de datos y las operaciones CRUD.
* Consultas preparadas: Utilizaremos consultas preparadas para evitar la inyección de SQL.
* Transacciones: Implementaremos transacciones para asegurar que las operaciones críticas se completen correctamente.
* Conexión avanzada: Usaremos un patrón de diseño para manejar la conexión a la base de datos.
* Consultas avanzadas: Realizaremos consultas avanzadas para filtrar y ordenar los libros.

### 1. Estructura de archivos y directorios

```php
libreria/
│
├── config/
│   └── database.php          # Configuración de la base de datos
│
├── models/
│   └── Book.php              # Modelo de libro
│
├── controllers/
│   └── BookController.php     # Controlador de libro
│
├── views/
│   ├── index.php             # Vista principal (lista de libros)
│   ├── create.php            # Vista para crear un libro
│   ├── edit.php              # Vista para editar un libro
│   └── DELETE.php            # Vista para eliminar un libro
│
├── public/
│   └── index.php             # Punto de entrada de la aplicación
│
└── .htaccess                 # Configuración del servidor (opcional)
```

### 2. Creacion de la base de datos y tabla
Código SQL para crear la tabla ```books``` en la base de datos ```libreria```:

```sql```

```php
CREATE DATABASE IF NOT EXISTS libreria;
USE libreria;

CREATE TABLE IF NOT EXISTS books (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    author VARCHAR(255) NOT NULL,
    price DECIMAL(10, 2) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 3. Creacion del archivo ```.htaccess```
Creamos el archivo ```.htaccess``` para redirigir todas las solicitudes al archivo ```public/index.php```:

```php
RewriteEngine On

# Rewrite all requests to the public/index.php file
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ http://libreria.test/public/index.php [L]
```
### Explicación:
* ```RewriteEngine On```: Habilita el módulo de reescritura de URLs.
* ```RewriteCond %{REQUEST_FILENAME} !-f```: Verifica que la solicitud no sea un archivo existente.
* ```RewriteCond %{REQUEST_FILENAME} !-d```: Verifica que la solicitud no sea un directorio existente.
* ```RewriteRule ^(.*)$ /public/index.php [L]```: Redirige todas las solicitudes que no sean archivos o directorios existentes al archivo public/index.php. La bandera [L] indica que es la última regla a ejecutar si coincide.

### 4. Configuración de la Base de Datos 
**Archivo:**```config/database.php```

```php
<?php
class Database {
    private $host = 'localhost';
    private $db_name = 'libreria';
    private $username = 'root';
    private $password = '';
    public $conn;

    public function getConnection() {
        $this->conn = null;
        try {
            $this->conn = new PDO("mysql:host={$this->host};dbname={$this->db_name}", $this->username, $this->password);
            $this->conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
        } catch (PDOException $exception) {
            echo "Error de conexión: " . $exception->getMessage();
        }
        return $this->conn;
    }
}
```

### Explicación:
* **Clase Database:** Esta clase se encarga de gestionar la conexión a la base de datos.
* **Propiedades:**
    * ```$host```, ```$db_name```, ```$username```, ```$password```: Almacenan los detalles necesarios para conectarse a la base de datos.
    * ```$conn```: Almacena la conexión a la base de datos.
* **Método getConnection:**
    * Establece la conexión utilizando PDO.
    * Maneja excepciones para capturar errores de conexión.

### 5. Modelo de libro
**Archivo:**```models/Book.php```

```php
<?php
class Book {
    private $conn;
    private $table_name = 'books';

    public $id;
    public $title;
    public $author;
    public $price;

    public function __construct($db) {
        $this->conn = $db;
    }

    public function create() {
        $query = "INSERT INTO " . $this->table_name . " (title, author, price) VALUES (:title, :author, :price)";
        $stmt = $this->conn->prepare($query);

        $stmt->bindParam(':title', $this->title);
        $stmt->bindParam(':author', $this->author);
        $stmt->bindParam(':price', $this->price);

        if ($stmt->execute()) {
            return true;
        }
        return false;
    }

    public function read() {
        $query = "SELECT * FROM " . $this->table_name . " ORDER BY id DESC";
        $stmt = $this->conn->prepare($query);
        $stmt->execute();
        return $stmt;
    }

    public function update() {
        $query = "UPDATE " . $this->table_name . " SET title = :title, author = :author, price = :price WHERE id = :id";
        $stmt = $this->conn->prepare($query);

        $stmt->bindParam(':id', $this->id);
        $stmt->bindParam(':title', $this->title);
        $stmt->bindParam(':author', $this->author);
        $stmt->bindParam(':price', $this->price);

        if ($stmt->execute()) {
            return true;
        }
        return false;
    }

    public function delete() {
        $query = "DELETE FROM " . $this->table_name . " WHERE id = :id";
        $stmt = $this->conn->prepare($query);
        $stmt->bindParam(':id', $this->id);
        if ($stmt->execute()) {
            return true;
        }
        return false;
    }
}
```

### Explicación:
* **Clase Book:** Modelo que representa un libro en la base de datos.
* **Propiedades:**
    * ```$conn```: Conexión a la base de datos.
    * ```$table_name```: Nombre de la tabla que contiene los libros.
    * ```$id```, ```$title```, ```$author```, ```$price```: Atributos del libro.
* **Métodos:**
    * ```create()```: Inserta un nuevo libro en la base de datos.
    * ```read()```: Lee todos los libros de la base de datos.
    * ```update()```: Actualiza los detalles de un libro específico.
    * ```delete()```: Elimina un libro de la base de datos.

### 6. Controlador de libro
**Archivo:**```controllers/BookController.php```

```php
<?php
require_once '../config/database.php';
require_once '../models/Book.php';

class BookController {
    private $db;
    private $book;

    public function __construct() {
        $database = new Database();
        $this->db = $database->getConnection();
        $this->book = new Book($this->db);
    }

    public function create($title, $author, $price) {
        $this->book->title = $title;
        $this->book->author = $author;
        $this->book->price = $price;

        $this->db->beginTransaction();
        if ($this->book->create()) {
            $this->db->commit();
            return true;
        } else {
            $this->db->rollBack();
            return false;
        }
    }

    public function read() {
        return $this->book->read();
    }

    public function update($id, $title, $author, $price) {
        $this->book->id = $id;
        $this->book->title = $title;
        $this->book->author = $author;
        $this->book->price = $price;

        $this->db->beginTransaction();
        if ($this->book->update()) {
            $this->db->commit();
            return true;
        } else {
            $this->db->rollBack();
            return false;
        }
    }

    public function delete($id) {
        $this->book->id = $id;

        $this->db->beginTransaction();
        if ($this->book->delete()) {
            $this->db->commit();
            return true;
        } else {
            $this->db->rollBack();
            return false;
        }
    }
}
```
### Explicación:
* **Clase BookController:** Controlador que gestiona las operaciones relacionadas con los libros.
* **Propiedades:**
    * ```$db```: Conexión a la base de datos.
    * ```$book```: Instancia del modelo ```Book```.
* **Métodos:**
    * ```create()```: Crea un nuevo libro, utilizando transacciones para asegurar la operación.
    * ```read()```: Devuelve todos los libros.
    * ```update()```: Actualiza los datos de un libro específico, usando transacciones.
    * ```delete()```: Elimina un libro específico, utilizando transacciones.

### 7. Vista principal
**Archivo:**```views/index.php```

```php
  <?php
require_once '../controllers/BookController.php';

$bookController = new BookController();
$books = $bookController->read();
?>

<!DOCTYPE html>
<html>
<head>
    <title>Lista de Libros</title>
</head>
<body>
    <h1>Lista de Libros</h1>
    <a href="create.php">Agregar Libro</a>
    <table border="1">
        <tr>
            <th>ID</th>
            <th>Título</th>
            <th>Autor</th>
            <th>Precio</th>
            <th>Acciones</th>
        </tr>
        <?php while ($row = $books->fetch(PDO::FETCH_ASSOC)): ?>
            <tr>
                <td><?php echo $row['id']; ?></td>
                <td><?php echo $row['title']; ?></td>
                <td><?php echo $row['author']; ?></td>
                <td><?php echo $row['price']; ?></td>
                <td>
                    <a href="edit.php?id=<?php echo $row['id']; ?>">Editar</a>
                    <a href="delete.php?id=<?php echo $row['id']; ?>">Eliminar</a>
                </td>
            </tr>
        <?php endwhile; ?>
    </table>
</body>
</html>
```

### Explicación:
* **Código PHP:** Incluye el controlador y obtiene la lista de libros.
* **Estructura HTML:**
    * Título de la página.
    * Enlace para agregar un nuevo libro.
    * Tabla que muestra todos los libros con ID, título, autor, precio y acciones (editar y eliminar).
* **Bucle while:** Itera a través de los resultados y los muestra en la tabla.

### 8. Vista para crear un libro
**Archivo:**```views/create.php```

```php
<?php
require_once '../controllers/BookController.php';

$bookController = new BookController();

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $title = $_POST['title'];
    $author = $_POST['author'];
    $price = $_POST['price'];

    if ($bookController->create($title, $author, $price)) {
        header('Location: index.php');
    } else {
        echo "Error al agregar el libro.";
    }
}
?>

<!DOCTYPE html>
<html>
<head>
    <title>Agregar Libro</title>
</head>
<body>
    <h1>Agregar Libro</h1>
    <form method="POST">
        <label>Título:</label>
        <input type="text" name="title" required>
        <br>
        <label>Autor:</label>
        <input type="text" name="author" required>
        <br>
        <label>Precio:</label>
        <input type="number" name="price" required>
        <br>
        <input type="submit" value="Agregar">
    </form>
</body>
</html>
```
### Explicación:
* **Código PHP:** Incluye el controlador y gestiona el envío del formulario.
* **Método POST:** Captura los datos del formulario y llama al método create del controlador.
* **Estructura HTML:**
    * Título de la página.
    * Formulario para ingresar los detalles del libro (título, autor y precio).
    * Botón para enviar el formulario.
    * Enlace para volver a la lista de libros.


# Patrones de la capa de datos: activerecord y repository


