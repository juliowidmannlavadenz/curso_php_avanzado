# MVC
## Definición MVC (modelo, vista, controlador)

As un patrón de arquitectura de software que organiza el código en tres componentes principales (Modelo, Vista y Controlador) con el propósito de separar las responsabilidades y facilitar el mantenimiento y escalabilidad de aplicaciones complejas. Este patrón es especialmente popular en el desarrollo web y de aplicaciones de software, ya que permite manejar de manera eficiente la lógica de negocios, la interfaz de usuario y la comunicación entre ambos.

El modelo MVC divide una aplicación en tres componentes independientes:

* **Modelo:** Representa los datos y la lógica de negocio.
* **Vista:** Se encarga de la presentación de los datos y la interacción con el usuario.
* **Controlador:** Maneja la lógica de entrada del usuario, coordina la actualización de los datos en el modelo y la presentación de esos datos en la vista.
* 
> * Esta separación mejora la organización del código y permite el desarrollo paralelo, ya que los desarrolladores pueden trabajar en cada componente de manera independiente.

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






# Sintaxis alternativa a las estructuras repetitivas

