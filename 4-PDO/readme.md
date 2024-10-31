# PDO
## Que es PDO (PHP Data Objects) 
Ss una extensión en PHP que proporciona una interfaz uniforme para acceder a bases de datos. Permite trabajar con diferentes sistemas de bases de datos (como MySQL, PostgreSQL, SQLite, entre otros) utilizando el mismo conjunto de funciones, lo que facilita la portabilidad de la aplicación si decides cambiar el sistema de base de datos en el futuro.

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
Incluye soporte para MySQL, PostgreSQL, SQLite, Oracle, entre otros, mediante diferentes controladores.

### 3. Prepared Statements (Consultas preparadas):
Esto ayuda a proteger contra inyecciones SQL, ya que permite separar los datos de la consulta SQL.

### 4. Transacciones:
PDO soporta transacciones, permitiendo ejecutar una serie de consultas de forma segura, revirtiendo los cambios en caso de fallo.



## Manejo de errores en PDO
## Conexión con PDO
## Consultas con PDO
## Patrones de la capa de datos: activerecord y repository

