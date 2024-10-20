# Patron front controller
## Definición
El Patrón Front Controller en PHP es un patrón de diseño arquitectónico que centraliza todas las peticiones entrantes a una única ubicación antes de que se distribuyan a los controladores específicos. En lugar de tener múltiples scripts que gestionen distintas partes de la aplicación, este patrón permite manejar todas las solicitudes en un solo punto de entrada **(como index.php)**, mejorando la organización y el control sobre las rutas y las acciones que se ejecutan.

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/fornt_controller.png?raw=true" alt="Imagen width="600" style />
</p>
<br>

## ¿Cómo funciona?

### **1. Único punto de entrada (Front Controller):**
El archivo index.php es el único punto de entrada para todas las peticiones. Aquí recibes todas las solicitudes HTTP y decides qué hacer con ellas.

```php
require 'Router.php'; 

$router = new Router(); 
$router->route($_SERVER['REQUEST_URI']); 
```
**Explicación:**
* Todas las peticiones que llegan a la aplicación pasan por index.php.
* En este archivo, cargamos el archivo Router.php, que se encargará de dirigir las solicitudes a los controladores correspondientes.

### **2. Enrutamiento:**
El enrutador (Router.php) se encarga de analizar la URL y determinar qué controlador y acción se deben ejecutar.

### **3. Controladores:**
El controlador es quien maneja la lógica de negocio. Dependiendo de la acción solicitada, se ejecuta el controlador correspondiente.

### **4. Modularidad:**
El patrón Front Controller permite centralizar la funcionalidad común, como manejo de errores, autenticación, validación, etc.

## Petición y respuesta http
## Ciclo de vida de una petición http
## Relaciones entre clases




