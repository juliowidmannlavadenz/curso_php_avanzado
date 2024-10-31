# PDO
## Que es PDO (PHP Data Objects) 
Ss una extensión en PHP que proporciona una interfaz uniforme para acceder a bases de datos. Permite trabajar con diferentes sistemas de bases de datos (como MySQL, PostgreSQL, SQLite, entre otros) utilizando el mismo conjunto de funciones, lo que facilita la portabilidad de la aplicación si decides cambiar el sistema de base de datos en el futuro.

## Características de PDO:

### 1. Abstracción de base de datos: 
PDO permite cambiar de un sistema de base de datos a otro sin modificar gran parte del código, ya que la sintaxis de las consultas SQL permanece igual, y solo se cambia el controlador en la conexión.

### Ejemplo: Clase de Abstracción de base de datos  con PDO
Este código define una clase Database que se conecta a distintas bases de datos, permitiendo cambiar solo los datos de conexión:


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

