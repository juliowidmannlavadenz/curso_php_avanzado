# MVC
## Definición MVC (modelo, vista, controlador)

As un patrón de arquitectura de software que organiza el código en tres componentes principales (Modelo, Vista y Controlador) con el propósito de separar las responsabilidades y facilitar el mantenimiento y escalabilidad de aplicaciones complejas. Este patrón es especialmente popular en el desarrollo web y de aplicaciones de software, ya que permite manejar de manera eficiente la lógica de negocios, la interfaz de usuario y la comunicación entre ambos.

El modelo MVC divide una aplicación en tres componentes independientes:

* **Modelo:** Representa los datos y la lógica de negocio.
* **Vista:** Se encarga de la presentación de los datos y la interacción con el usuario.
* **Controlador:** Maneja la lógica de entrada del usuario, coordina la actualización de los datos en el modelo y la presentación de esos datos en la vista.
  
> Esta separación mejora la organización del código y permite el desarrollo paralelo, ya que los desarrolladores pueden trabajar en cada componente de manera independiente.

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/mvc_grafico2.png?raw=true" alt="Imagen width="400" style />
</p>
<br>

## Partes del modelo MVC
## Modelo:

El modelo es el componente que maneja toda la lógica de negocios, las reglas de la aplicación y la estructura de los datos. Su responsabilidad es:

* Gestionar y manipular los datos de la aplicación, ya sea desde una base de datos, archivos, o cualquier otro origen.
* Aplicar las reglas de negocio y la lógica específica de la aplicación.
* Notificar cambios en los datos a la vista para que ésta se actualice.

### Ejemplo:
En una aplicación de inventario de productos, el modelo se encargaría de gestionar el inventario, incluyendo funciones para agregar, actualizar o eliminar productos y calcular el valor del inventario.

```Laravel 11```

```php
<?php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Producto extends Model
{
    protected $fillable = ['nombre', 'cantidad', 'precio'];

    public function agregarProducto($data) { /* lógica para agregar */ }
    public function actualizarProducto($id, $data) { /* lógica para actualizar */ }
    public function eliminarProducto($id) { /* lógica para eliminar */ }
    public function calcularValorInventario() { return $this->sum(DB::raw('cantidad * precio')); }
}
```

* Este modelo tiene funciones básicas para gestionar productos y calcular el valor del inventario.

## Vista:

La vista es la capa de presentación de la aplicación, responsable de mostrar los datos y recibir la interacción del usuario. Su función principal es:

* Mostrar la información de manera comprensible y amigable.
* Presentar formularios o interfaces de entrada para que el usuario pueda interactuar con la aplicación.
* Recibir los datos que el modelo envía y reflejar cualquier cambio que se realice en ellos.

### Ejemplo:
En una tienda en línea, la vista podría ser una página de productos donde el usuario puede ver la lista de artículos disponibles y seleccionar uno para obtener más información.

```Laravel 11```

```php
@foreach($productos as $producto)
    <div>{{ $producto->nombre }} - <a href="{{ route('productos.show', $producto->id) }}">Ver más</a></div>
@endforeach
```

* Esta vista recorre una lista de productos y muestra el nombre de cada uno con un enlace para ver más detalles.

## Controlador:

El controlador actúa como intermediario entre la vista y el modelo, gestionando la lógica de entrada del usuario. Sus principales responsabilidades son:

* Interpretar las acciones del usuario (como clics o datos de formularios).
* Actualizar el modelo en respuesta a las entradas del usuario.
* Solicitar a la vista que se actualice para reflejar los cambios en el modelo.

### Ejemplo:
En una aplicación bancaria, si el usuario desea transferir dinero, el controlador recibe esta acción, procesa la solicitud, consulta o actualiza el modelo (como el saldo de las cuentas involucradas) y finalmente actualiza la vista con el saldo actual.

```Laravel 11```

```php
<?php
namespace App\Http\Controllers;

use App\Models\Cuenta;

class TransferenciaController extends Controller
{
    public function transferir($cuentaOrigenId, $cuentaDestinoId, $monto) {
        $cuentaOrigen = Cuenta::find($cuentaOrigenId);
        $cuentaDestino = Cuenta::find($cuentaDestinoId);
        
        $cuentaOrigen->saldo -= $monto;
        $cuentaDestino->saldo += $monto;

        $cuentaOrigen->save();
        $cuentaDestino->save();

        return view('saldo.actual', ['cuenta' => $cuentaOrigen]);
    }
}
```

* Este controlador procesa la transferencia, actualiza los saldos y devuelve la vista con el saldo actualizado de la cuenta de origen.

## Ejemplo completo de MVC (modelo, vista, controlador) en Laravel 11
Este proyecto consiste en una aplicación sencilla en Laravel donde puedes ver una lista de automóviles con sus fotos y votar por cualquiera de ellos. Implementaremos el patrón de arquitectura MVC (```Modelo-Vista-Controlador```), lo que facilita la separación de lógica y presentación en el desarrollo de aplicaciones web.

### 1. Estructura de archivos del proyecto
El proyecto tendra una estructura como esta.

```php
VotacionAutos/
├── app/
│   ├── Http/
│   │   └── Controllers/
│   │       └── AutoController.php  // Controlador del auto
│   └── Models/
│       └── Auto.php                // Modelo del auto
├── database/
│   └── migrations/
│       └── 2024_xx_xx_create_autos_table.php  // Migración para la tabla autos
├── public/
│   └── storage/
│       └── autos/                  // Carpeta para las fotos de los autos
├── resources/
│   └── views/
│       └── autos/
│           └── index.blade.php     // Vista para mostrar autos y votar
└── routes/
    └── web.php                     // Definición de rutas
```

### 2. Configuración del proyecto

1. Creamos un nuevo proyecto en Laravel llamado ```VotacionAutos```.

```php
laravel new VotacionAutos
```

* Esto crea la estructura básica del proyecto en Laravel en la carpeta ```VotacionAutos```.

2. Configurar el archivo ```.env``` para conectar el proyecto a la base de datos. Ajustamos los datos de conexión, usaremos una base de datos MySql.

```php
DB_DATABASE=votacion_autos
DB_USERNAME=tu_usuario
DB_PASSWORD=tu_contraseña
```

* El archivo .env es el archivo de configuración donde definimos las credenciales de la base de datos.

3. Ejecutamos la migración inicial para que Laravel cree la tabla ```migrations```.

```php
php artisan migrate
```

### 3. Crear el Modelo y la Migración
Creamos el modelo ```Auto```, que representa cada automóvil. A cada automóvil le añadiremos una imagen y un contador de votos.

```php
php artisan make:model Auto -m
```

Este comando generará dos archivos:

* Un archivo de modelo en ```app/Models/Auto.php```.
* Un archivo de migración en ```database/migrations```.

**Archivo de Migración:**

Abrimos el archivo de migración recién creado en ```database/migrations```, y definimos la estructura de la tabla autos:

**Archivo:** ```database/migrations/2024_10_29_001716_create_autos_table.php```

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('autos', function (Blueprint $table) {
            $table->id();
            $table->string('nombre');
            $table->string('imagen'); // Ruta de la imagen
            $table->integer('votos')->default(0); // Contador de votos
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('autos');
    }
};
```

* Este código crea una tabla ```autos``` con columnas para el nombre, la imagen (como una cadena que almacena la ruta) y los votos (inicialmente 0). ```timestamps()``` añade campos ```created_at``` y ```updated_at``` automáticamente.

**Ejecutar la migración:**
Ejecutamos la migración para crear la tabla ```autos``` en la base de datos.

```php
php artisan migrate
```

### 4. Crear el Controlador

Generamos un controlador llamado ```AutoController``` para gestionar las funciones de la aplicación:

```php
php artisan make:controller AutoController
```
* Dentro del controlador, añadimos dos funciones: una para mostrar todos los autos y otra para incrementar el contador de votos cuando un usuario vota.

**Archivo:**  ```Http/Controllers/AutoController.php```

```php
<?php

namespace App\Http\Controllers;

use App\Models\Auto; // Importar el modelo Auto
use Illuminate\Http\Request;

class AutoController extends Controller
{
    public function index()
    {
        // Recupera todos los autos desde la base de datos
        $autos = Auto::all();
        return view('autos.index', compact('autos'));
    }

    public function votar($id)
    {
        // Encuentra el auto por su ID e incrementa el contador de votos
        $auto = Auto::findOrFail($id);
        $auto->increment('votos');
        return redirect()->back()->with('success', '¡Voto registrado!');
    }
}
```

* La función ```index``` recupera todos los autos y pasa los datos a la vista ```index.blade.php```.  
* La función ```votar``` busca el auto por su ID y usa el método ```increment``` para sumar 1 al contador de votos, luego redirige al usuario con un mensaje de éxito.

### 5. Crear las vistas

Crea la vista en ```index.blade.php``` para mostrar la lista de autos con las fotos y un botón de votación para cada uno.

**Archivo:** ```resources/views/autos/index.blade.php```

```php
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Votación de Autos</title>
</head>
<body>
    <h1>Vota por tu Auto Favorito</h1>

    @if(session('success'))
        <p>{{ session('success') }}</p>
    @endif

    <div style="display: flex; flex-wrap: wrap;">
        @foreach($autos as $auto)
            <div style="margin: 10px; text-align: center;">
                <h3>{{ $auto->nombre }}</h3>
                <img src="{{ asset('storage/' . $auto->imagen) }}" alt="{{ $auto->nombre }}" style="width: 200px;">
                <p>Votos: {{ $auto->votos }}</p>
                <form action="{{ route('autos.votar', $auto->id) }}" method="POST">
                    @csrf
                    <button type="submit">Votar</button>
                </form>
            </div>
        @endforeach
    </div>
</body>
</html>
```

* En esta vista, iteramos sobre cada ```auto``` y mostramos su nombre, imagen, contador de votos y un formulario con un botón para votar. La acción del formulario envía una solicitud ```POST``` para votar en el auto correspondiente.

### 6. Crear las rutas
**Archivo:** ```routes/web.php```

```php
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\AutoController;

Route::get('/', function () {
    return view('welcome');
});

Route::get('/autos', [AutoController::class, 'index'])->name('autos.index');
Route::post('/autos/votar/{id}', [AutoController::class, 'votar'])->name('autos.votar');
```

*  Estas rutas enlazan las URLs ```/autos``` y ```/autos/votar/{id}``` con las funciones ```index``` y ```votar``` de ```AutoController```. La ruta ```votar``` usa el método ```POST``` para registrar votos de forma segura.

### 7. Añadir fotos de los automóviles

Para almacenar imágenes , subi las fotos a ```storage/app/public/```. Luego, enlazamos el directorio ```storage``` con ```public``` ejecutando:

```php
php artisan storage:link
```

* Con esto, las imágenes almacenadas en ```storage/app/public``` estarán accesibles a través del navegador desde ```public/storage```.

### 8. Insertar Datos de Prueba

Usamos ```Tinker``` para agregar datos de prueba (autos) directamente desde la línea de comandos:

```php
php artisan tinker
```

Estando dentro de Tinker:

```php
App\Models\Auto::create(['nombre' => 'BMW 2024', 'imagen' => 'auto1.jpg']);
App\Models\Auto::create(['nombre' => 'BMW 2024', 'imagen' => 'auto2.jpg']);
App\Models\Auto::create(['nombre' => 'X-4', 'imagen' => 'auto3.jpg']);

```

* Aquí creamos 3 autos de ejemplo con sus respectivas rutas de imagen. Podemos añadir tantos autos como queramos en la base de datos.

### 9. Ejecutar la aplicación

Iniciamos el servidor y accedemos a la aplicación en el navegador:

```php
php artisan serve
```

### 10. Vista en el navegador
Visitamos http://127.0.0.1:8000/autos para ver y votar por los autos.


<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/votacion_autos.png?raw=true" alt="Imagen width="400" style />
</p>
<br>

# Sintaxis alternativa a las estructuras repetitivas

En PHP podemos utilizar diversas alternativas a las estructuras repetitivas como foreach o for para recorrer colecciones de datos:

* Utilizar funciones.
* Generadores.
* Template Engines.
* Componentes.
* Clases y Métodos.

### 1. Utilizar funciones 
Podemos usar funciones como ```array_map```, ```array_walk```, o ```array_reduce``` para manipular y recorrer arrays sin usar estructuras repetitivas explícitas.

```php
$productos = ['Producto 1', 'Producto 2', 'Producto 3'];

// Usando array_map para crear un nuevo array con una función
$productosFormateados = array_map(function($producto) {
    return "<li>{$producto}</li>";
}, $productos);

// Imprimir los productos
echo "<ul>" . implode('', $productosFormateados) . "</ul>";
```

* Este código convierte un array de productos en una lista HTML utilizando array_map y implode para facilitar el formateo y la impresión en un solo paso.

### 2. Generadores
Un generador es un tipo especial de función que permite iterar sobre una secuencia de valores de forma eficiente, generando cada valor solo cuando se necesita. Los generadores utilizan la palabra clave ```yield``` en lugar de ```return```, lo que permite que la función conserve su estado entre ejecuciones y produzca un valor a la vez, "pausando" su ejecución hasta que se solicite el siguiente valor.
Permiten iterar sobre una colección sin la necesidad de crear un array completo en memoria.

### ¿Como funcionan los generadores?
A diferencia de una función tradicional que devuelve todos los resultados a la vez en un array o lista, un generador produce valores de uno en uno. Esto es útil cuando necesitamos manejar grandes cantidades de datos o secuencias infinitas sin ocupar demasiada memoria.

> Cuando llamas a un generador, PHP no ejecuta el código de inmediato; en su lugar, devuelve un objeto del tipo ```Generator```, que se puede iterar con un ```foreach``` o métodos de generadores (como ```->current()``` o ```->next()```).

Imaginemos un generador que devuelve números pares hasta un cierto límite:

```php
function obtenerPares($limite) {
    for ($i = 0; $i <= $limite; $i += 2) {
        yield $i;
    }
}

foreach (obtenerPares(10) as $par) {
    echo $par . "\n"; // Imprime 0, 2, 4, 6, 8, 10
}
```
* Aquí, ```obtenerPares(10)``` genera los números pares de forma incremental. Solo se produce cada valor a medida que se necesita en el bucle ```foreach```.


### 3. Template engines
Utilizar un motor de plantillas como Twig (Symfony) o Blade (Laravel) puede simplificar la forma en que se presentan las estructuras repetitivas. Estas herramientas permiten una sintaxis más limpia y legible.

```Ejemplo en Twig:```

```php
<ul>
    {% for producto in productos %}
        <li>{{ producto }}</li>
    {% endfor %}
</ul>
```

* Este código en Twig genera una lista ```<ul>```. Recorre el array ```productos``` e imprime cada elemento dentro de un ```<li>```. El resultado es una lista de productos en HTML.

```Ejemplo en Blade:```

```php
<ul>
    @foreach ($productos as $producto)
        <li>{{ $producto }}</li>
    @endforeach
</ul>
```

* Este código en Blade genera una lista HTML ```<ul>```. Utiliza ```@foreach``` para recorrer el array $productos y muestra cada elemento dentro de un ```<li>```. El resultado es una lista con cada producto en un elemento de lista.


