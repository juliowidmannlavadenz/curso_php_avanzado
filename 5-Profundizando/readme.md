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
## Concepto de clase abstracta
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


### 4. Declaración: 
Se usa la palabra clave abstract antes de la definición de la clase y de los métodos abstractos.


# Miembros estáticos, patrones de diseño (GOF) introducción a sistemas distribuidos
# Introducción a sistemas distribuidos
# Desarrollo de una API REST


