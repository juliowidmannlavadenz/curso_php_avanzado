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


# Sintaxis alternativa a las estructuras repetitivas

