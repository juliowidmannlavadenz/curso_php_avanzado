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
Es importante para identificar problemas en las consultas a la base de datos y evitar que estos se traduzcan en fallos de la aplicación o filtren información sensible.

## Modos de manejo de errores
PDO tiene tres modos de manejo de errores que se pueden configurar:

### 1. PDO::ERRMODE_SILENT: 
El modo predeterminado. Solo se configura un código de error interno, pero no se genera ninguna notificación visible.

```php
$pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_SILENT);
```

### 2. PDO::ERRMODE_WARNING: 
Emite una advertencia de PHP, útil para depuración rápida.

```php
$pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_WARNING);
```

### 3. PDO::ERRMODE_EXCEPTION: 
Lanza una excepción PDOException y permite capturar errores en bloques try-catch. Este es el modo más recomendado para manejar errores en producción, ya que facilita la gestión de errores de forma más controlada.

```php
$pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
```

### Consejos de seguridad:
> * No muestres mensajes de error detallados en un entorno de producción. En su lugar, registra los errores en un archivo de log para revisarlos posteriormente.
> * Sanitiza la salida del mensaje de error para evitar que información sensible quede expuesta.


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

### Vista en el navegador

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/vista.png?raw=true" style />
</p>
<br>

### 8. Vista para agregar un libro
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
 
### Vista en el navegador

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/agregar_libro.png?raw=true" style />
</p>
<br>

### 9. Vista para editar un libro
**Archivo:**```views/edit.php```

```php
<?php
require_once '../controllers/BookController.php';

$bookController = new BookController();
$book = $bookController->read()->fetch(PDO::FETCH_ASSOC);

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $id = $_POST['id'];
    $title = $_POST['title'];
    $author = $_POST['author'];
    $price = $_POST['price'];

    if ($bookController->update($id, $title, $author, $price)) {
        header('Location: index.php');
    } else {
        echo "Error al actualizar el libro.";
    }
}
?>

<!DOCTYPE html>
<html>
<head>
    <title>Editar Libro</title>
</head>
<body>
    <h1>Editar Libro</h1>
    <form method="POST">
        <input type="hidden" name="id" value="<?php echo $book['id']; ?>">
        <label>Título:</label>
        <input type="text" name="title" value="<?php echo $book['title']; ?>" required>
        <br>
        <label>Autor:</label>
        <input type="text" name="author" value="<?php echo $book['author']; ?>" required>
        <br>
        <label>Precio:</label>
        <input type="number" name="price" value="<?php echo $book['price']; ?>" required>
        <br>
        <input type="submit" value="Actualizar">
    </form>
</body>
</html>
```

### Explicación:
* **Código PHP:** Incluye el controlador y carga los datos del libro a editar.
* **Método GET:** Obtiene el ID del libro de la URL y busca sus datos.
* **Método POST:** Captura los datos del formulario y llama al método update del controlador.
* **Estructura HTML:**
    * Título de la página.
    * Formulario para editar los detalles del libro (título, autor y precio).
    * Botón para actualizar y enlace para volver a la lista de libros.
 
### Vista en el navegador

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/editar_libro.png?raw=true" style />
</p>
<br>

### 10. Vista para borrar un libro
**Archivo:**```views/delete.php```

```php
<?php
require '../config/database.php';
try {

    $db = (new Database())->getConnection();

    if (isset($_GET['id'])) {
        $bookId = intval($_GET['id']); 

        $db->beginTransaction();

        $query = "DELETE FROM books WHERE id = :id";
        $stmt = $db->prepare($query);
        $stmt->bindParam(':id', $bookId, PDO::PARAM_INT);

        if ($stmt->execute()) {
            $db->commit();
            echo "El libro ha sido eliminado correctamente.";
            echo '<br><a href="index.php">Regresar al listado de libros</a>'; 
        } else {
            $db->rollBack();
            echo "Error al eliminar el libro.";
        }
    } else {
        echo "No se ha proporcionado un ID de libro.";
    }
} catch (PDOException $e) {
 
    if (isset($db)) {
        $db->rollBack(); 
    }
    echo "Error: " . $e->getMessage();
}
```

### Explicación:
* **Código PHP:** Se incluye el archivo de configuración para establecer la conexión a la base de datos.
* **Conexión a la base de datos:** Se obtiene una conexión usando la clase ```Database```.
* Método GET: Se verifica si se proporciona un ID de libro en la URL y se convierte a entero.
* Transacción: Se inicia una transacción y se prepara una consulta SQL para eliminar el libro.
* Ejecución y manejo de la consulta: Se ejecuta la consulta; si tiene éxito, se confirma la transacción y se muestra un mensaje de éxito con un enlace para regresar. Si falla, se revierte la transacción y se muestra un mensaje de error.
* Validación de ID: Si no hay ID, se informa al usuario. Se capturan excepciones y se revierte la transacción si es necesario.

### Vista en el navegador

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/borrar_libro.png?raw=true" style />
</p>
<br>

### 11. Punto de entrada de la aplicación
**Archivo:**```public/index.php```

```php
<?php
header('Location: ../views/index.php');
exit;
```
### Explicación:
* **Redirección:** Este archivo redirige automáticamente a la vista principal donde se muestra la lista de libros. Proporciona un punto de entrada simple para la aplicación.

### Vista en el navegador

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/vista.png?raw=true" style />
</p>
<br>

### Conclusión y resumen:
Este ejemplo de gestión de una librería en PHP utiliza PDO para manejar la base de datos con un enfoque de programación orientada a objetos, proporcionando una capa de abstracción. Se implementan consultas preparadas para prevenir la inyección de SQL, y se utilizan transacciones para asegurar que las operaciones se realicen de manera consistente.


# Patrones de la capa de datos: activerecord y repository
## ¿Qué es active record?
Es uno de los patrones de diseño más utilizados para trabajar con bases de datos y modelos de datos. Es popular en muchos frameworks y lenguajes de programación como Ruby on Rails, Laravel y Django, aunque también se puede implementar en otros entornos. 

El patrón Active Record combina los datos y el comportamiento relacionado en una sola clase. Cada objeto de la clase representa una fila en una tabla de base de datos, y los métodos de la clase permiten realizar operaciones CRUD (crear, leer, actualizar, eliminar) directamente sobre la base de datos.

## Características principales
### 1. Simplicidad y cohesión: 
La clase actúa como un modelo de datos y contiene toda la lógica necesaria para manejar los datos de una base de datos.

### Ejemplo: 

```php
$usuario = new Usuario();
$usuario->nombre = 'Juan';
$usuario->guardar(); 
```
* Creamos una instancia de la clase ```Usuario```, se asigna un valor a un atributo y se llama al método ```guardar()``` para insertar o actualizar el registro en la base de datos. Este enfoque muestra la simplicidad y cohesión que ofrece el patrón Active Record.

### 2. Operaciones CRUD: 
Las clases que implementan este patrón pueden leer, insertar, actualizar y eliminar registros sin depender de otras capas de abstracción.

### Ejemplo: 

**Crear** ```create```
```php
$usuario = new Usuario();
$usuario->nombre = 'Ana';
$usuario->guardar(); 
```

**Leer** ```read```
```php
$usuario = Usuario::obtenerPorId(1); 
echo $usuario->nombre;
```

**Actualizar** ```update```
```php
$usuario = Usuario::obtenerPorId(1);
$usuario->nombre = 'Ana María';
$usuario->guardar(); // Actualiza el registro existente
```

**Eliminar** ```delete```
```php
$usuario = Usuario::obtenerPorId(1);
$usuario = Usuario::obtenerPorId(1);
$usuario->eliminar(); 
```
* Operaciones CRUD usando el patrón Active Record.

### 3. Representación de tabla: 
La representación de tabla es una estrategia de mapeo objeto-relacional (ORM) en la que se mapea una clase de la aplicación a una tabla de la base de datos. Los atributos de la clase se corresponden con las columnas de la tabla, y cada objeto de la clase representa una fila de la tabla.

### Relación entre clases y tablas
* **Clase = Tabla:** Cada clase del modelo representa una tabla en la base de datos.
* **Instancia de clase = Fila:** Cada objeto de la clase representa una fila de la tabla.
* **Propiedades = Columnas:** Cada propiedad o atributo de la clase corresponde a una columna de la tabla.

### Ejemplo: 
Imagina que tienes una tabla de base de datos llamada usuarios con las siguientes columnas: id, nombre y email. La representación de tabla en un modelo PHP usando Active Record podría verse así:

```php
class Usuario {
    public $id;
    public $nombre;
    public $email;

    // Métodos para operaciones CRUD van aquí...
}
```

* La clase ```Usuario``` representa la tabla ```usuarios```, y las propiedades ```id```, ```nombre``` y ```email``` corresponden a las columnas de la tabla.

## Ejemplo utilizando active record
En este ejemplo, crearemos un sistema simple de venta de autos usando el patrón Active Record.

### 1. Estructura de archivos

```php
/venta-autos
    /models
        Auto.php
    /controllers
        AutoController.php
    /views
        listar_autos.php
        agregar_auto.php
    config.php
    index.php
```

### 2. Creación de la BBDD y la tabla 

```sql```

```php
CREATE DATABASE IF NOT EXISTS venta_autos;
USE venta_autos;

CREATE TABLE IF NOT EXISTS autos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    marca VARCHAR(100) NOT NULL,
    modelo VARCHAR(100) NOT NULL,
    precio DECIMAL(10, 2) NOT NULL
);
```

### 3. Configuración de la base de datos
**Archivo:** ```config.php```

```php
<?php
// config.php
$host = 'localhost';
$dbname = 'venta_autos';
$username = 'root';
$password = '';

try {
    $pdo = new PDO("mysql:host=$host;dbname=$dbname", $username, $password);
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
} catch (PDOException $e) {
    die("Error al conectar a la base de datos: " . $e->getMessage());
}
```

### Explicación:
* Se establece una conexión a la base de datos usando PDO, que permite manejar la base de datos de forma segura.

### 4. Creación de la clase ```Auto```
**Archivo:** ```models/Auto.php```

```php
<?php
require_once 'config.php';

class Auto {
    private $pdo;
    public $id;
    public $marca;
    public $modelo;
    public $precio;

    public function __construct() {
        global $pdo;
        $this->pdo = $pdo;
    }

    public function save() {
        if (isset($this->id)) {
            $stmt = $this->pdo->prepare("UPDATE autos SET marca = ?, modelo = ?, precio = ? WHERE id = ?");
            $stmt->execute([$this->marca, $this->modelo, $this->precio, $this->id]);
        } else {
            $stmt = $this->pdo->prepare("INSERT INTO autos (marca, modelo, precio) VALUES (?, ?, ?)");
            $stmt->execute([$this->marca, $this->modelo, $this->precio]);
            $this->id = $this->pdo->lastInsertId();
        }
    }

    public static function findAll() {
        global $pdo;
        $stmt = $pdo->query("SELECT * FROM autos");
        return $stmt->fetchAll(PDO::FETCH_CLASS, 'Auto');
    }
}
```
### Explicación:
* La clase ```Auto``` contiene propiedades que representan las columnas de la tabla ```autos```. El método ```save()``` inserta o actualiza un registro en la base de datos, y el método ```findAll()``` devuelve todos los registros de la tabla.

### 5. Creación del controlador
**Archivo:** ```controllers/AutoController.php```

```php
<?php
require_once 'models/Auto.php';

class AutoController {
    public function listarAutos() {
        $autos = Auto::findAll();
        require 'Views/listar_autos.php';
    }

    public function agregarAuto($marca, $modelo, $precio) {
        $auto = new Auto();
        $auto->marca = $marca;
        $auto->modelo = $modelo;
        $auto->precio = $precio;
        $auto->save();
        header('Location: index.php');
    }
}
```

### Explicación:
* ```AutoController``` es responsable de manejar la lógica entre la vista y el modelo. Tiene métodos para listar autos y agregar nuevos.

### 5. Creación de la vista 
**Archivo:** ```views/listar_autos.php```

```php
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Listado de Autos</title>
</head>
<body>
    <h1>Listado de Autos</h1>
    <a href="views/agregar_auto.php">Agregar Auto</a>
    <ul>
        <?php foreach ($autos as $auto): ?>
            <li><?php echo "{$auto->marca} - {$auto->modelo} - $ {$auto->precio}"; ?></li>
        <?php endforeach; ?>
    </ul>
</body>
</html>
```

### Explicación:
* La vista muestra la lista de autos obtenidos del método ```listarAutos()``` del controlador.

### Vista en el navegador

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/listar_autos.png?raw=true" style />
</p>
<br>

### 6. Formulario para agregar un auto nuevo 
**Archivo:** ```views/agregar_auto.php```

```php
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Agregar Auto</title>
</head>
<body>
    <h1>Agregar Auto</h1>
    <form action="../index.php" method="post">
        <label>Marca: <input type="text" name="marca" required></label><br>
        <label>Modelo: <input type="text" name="modelo" required></label><br>
        <label>Precio: <input type="number" name="precio" required></label><br>
        <button type="submit">Guardar</button>
    </form>
</body>
</html>
```

### Explicación:
* Formulario  que envía datos a ```index.php``` para agregar un nuevo auto.

### Vista en el navegador

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/ingresar_auto.png?raw=true" style />
</p>
<br>

### 7. Punto de entrada para manejar las solicitudes
**Archivo:** ```index.php```

```php
<?php
require_once 'controllers/AutoController.php';

$controller = new AutoController();

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $controller->agregarAuto($_POST['marca'], $_POST['modelo'], $_POST['precio']);
} else {
    $controller->listarAutos();
}
```
* Este archivo decide si mostrar la lista de autos o procesar la solicitud de agregar un nuevo auto, dependiendo del método HTTP utilizado.

### Vista en el navegador

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/listar_autos.png?raw=true" style />
</p>
<br>

### Resumen y conclusión:
Este ejemplo muestra cómo implementar el patrón Active Record en PHP puro, estructurando la aplicación en modelos, controladores y vistas para una mayor organización y separación de responsabilidades. ```Auto.php``` representa el modelo de datos con métodos para interactuar con la base de datos, ```AutoController.php``` actúa como un intermediario entre la vista y el modelo, y ```index.php``` maneja las solicitudes del usuario.

## ¿Qué es repository?
Es un patrón de diseño en software que actúa como un intermediario entre la lógica de negocio de una aplicación y la fuente de datos, que puede ser una base de datos, un servicio web, o cualquier otro tipo de almacenamiento. Este patrón se utiliza principalmente en aplicaciones que necesitan una forma de abstraer el acceso a datos, facilitando la separación de preocupaciones y mejorando la mantenibilidad del código.

## Características principales
### 1. Abstracción de Datos: 
Proporciona una interfaz que abstrae la lógica de acceso a datos, de modo que la lógica de negocio no tenga que preocuparse por cómo se obtienen los datos o dónde se almacenan.

### Ejemplo: 

```php
class UserRepository { public function findById($id) { return "Usuario con ID: $id"; } }
$userRepo = new UserRepository();
echo $userRepo->findById(1); // Salida: Usuario con ID: 1
```

*  Este ejemplo muestra cómo crear una clase ```UserRepository``` que abstrae la lógica para encontrar un usuario por ID, y luego se utiliza esa clase para obtener un usuario específico.

### 2. Encapsulamiento:
Encapsula la lógica relacionada con la obtención y almacenamiento de datos, lo que permite que la lógica de negocio permanezca limpia y enfocada en sus responsabilidades.

### Ejemplo: 

```php
class ProductRepository { private $products = []; public function add($product) { $this->products[] = $product; } }
$productRepo = new ProductRepository();
$productRepo->add('Producto 1');
```
* En este ejemplo, la clase ```ProductRepository``` encapsula un array privado de productos y proporciona un método ```add``` para agregar productos a ese array, manteniendo la implementación interna oculta.

### 3. Interfaz Común: 
Define una interfaz común para realizar operaciones CRUD (Crear, Leer, Actualizar, Borrar) en la fuente de datos, lo que permite cambiar la implementación del acceso a datos sin afectar a la lógica de negocio.

### Ejemplo: 

```php
interface UserRepositoryInterface { public function find($id); }
class UserRepository implements UserRepositoryInterface { public function find($id) { return "Usuario $id"; } }
echo (new UserRepository())->find(1);
```
* Este ejemplo define una interfaz ```UserRepositoryInterface``` que declara el método ```find```, y ```UserRepository``` implementa esa interfaz, garantizando una estructura común para los repositorios.

### 4. Reutilización de Código: 
Facilita la reutilización del código de acceso a datos, ya que el mismo repositorio puede ser utilizado en diferentes partes de la aplicación.

### Ejemplo: 

```php
class BaseRepository { public function findAll() { return "Obteniendo todos los registros"; } }
class UserRepository extends BaseRepository {}
echo (new UserRepository())->findAll(); 
```
* En este ejemplo, ```UserRepository``` hereda de ```BaseRepository```, reutilizando la lógica del método ```findAll``` sin necesidad de volver a implementarlo.

### 5. Facilidad de Pruebas: 
Permite realizar pruebas más fácilmente, ya que se pueden usar simulaciones (mocks) de los repositorios en lugar de depender de una base de datos real.

### Ejemplo: 

```php
class MockUserRepository { public function find($id) { return "Mock Usuario $id"; } }
$userRepo = new MockUserRepository();
echo $userRepo->find(1); // Salida: Mock Usuario 1
```
* Este ejemplo usa un repositorio simulado (MockUserRepository) que facilita la prueba de la lógica de negocio sin depender de una base de datos real, haciendo que las pruebas sean rápidas y predecibles.
