# Patron front controller
## Definición
El Patrón Front Controller en PHP es un patrón de diseño arquitectónico que centraliza todas las peticiones entrantes a una única ubicación antes de que se distribuyan a los controladores específicos. En lugar de tener múltiples scripts que gestionen distintas partes de la aplicación, este patrón permite manejar todas las solicitudes en un solo punto de entrada **(como index.php)**, mejorando la organización y el control sobre las rutas y las acciones que se ejecutan.

<br>
<p align="center">
<img src="https://github.com/juliowidmannlavadenz/curso_php_avanzado/blob/main/assets/fornt_controller.png?raw=true" alt="Imagen width="600" style />
</p>
<br>

## ¿Cómo funciona?

1. Único punto de entrada: Todas las peticiones HTTP que llegan a la aplicación pasan primero por un archivo común, como index.php. Este archivo actúa como un controlador único o Front Controller.

2. Enrutamiento: El Front Controller determina cuál acción o controlador específico debe manejar la solicitud. Esto generalmente se basa en la URL de la solicitud, que se mapea a controladores o acciones.

3. Controladores: Una vez que el Front Controller decide cuál controlador debe manejar la solicitud, delega la ejecución a ese controlador, que es el encargado de ejecutar la lógica de negocio correspondiente y devolver la respuesta adecuada.

4. Modularidad: El uso de un Front Controller permite centralizar funcionalidades comunes, como la gestión de errores, la autenticación, la autorización o la validación de solicitudes, en un solo lugar, evitando duplicaciones de código.


## Petición y respuesta http
## Ciclo de vida de una petición http
## Relaciones entre clases




