
# Patrones de Diseño

## Patrones Creacionales
| Patrón               | Descripción                                                                                                                  | Popularidad |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------|-------------|
| **Factory Method**    | Proporciona una interfaz para crear objetos en una superclase, permitiendo que las subclases alteren el tipo de objetos.     | 3           |
| **Abstract Factory**  | Permite producir familias de objetos relacionados sin especificar sus clases concretas.                                      | 3           |
| **Builder**           | Permite construir objetos complejos paso a paso, produciendo distintos tipos y representaciones con el mismo código.         | 3           |
| **Prototype**         | Permite copiar objetos existentes sin que el código dependa de sus clases.                                                  | 2           |
| **Singleton**         | Asegura que una clase tenga una única instancia, proporcionando un punto de acceso global.                                   | 2           |

## Patrones Estructurales
| Patrón               | Descripción                                                                                                                  | Popularidad |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------|-------------|
| **Adapter**           | Permite la colaboración entre objetos con interfaces incompatibles.                                                         | 3           |
| **Bridge**            | Divide una clase grande en dos jerarquías separadas (abstracción e implementación) que pueden desarrollarse independientemente. | 1           |
| **Composite**         | Permite componer objetos en estructuras de árbol y trabajar con ellas como si fueran objetos individuales.                   | 2           |
| **Decorator**         | Añade funcionalidades a objetos mediante objetos encapsuladores especiales.                                                 | 2           |
| **Facade**            | Proporciona una interfaz simplificada a una biblioteca, framework u otro grupo complejo de clases.                          | 2           |
| **Flyweight**         | Permite mantener más objetos en RAM compartiendo partes comunes del estado entre varios objetos.                             | 0           |
| **Proxy**             | Proporciona un sustituto o marcador de posición para otro objeto, controlando el acceso al objeto original.                  | 1           |

## Patrones de Comportamiento
| Patrón               | Descripción                                                                                                                  | Popularidad |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------|-------------|
| **Chain of Responsibility** | Permite pasar solicitudes a lo largo de una cadena de manejadores, donde cada uno decide procesarla o pasarla al siguiente.      | 1           |
| **Command**           | Convierte una solicitud en un objeto independiente, facilitando operaciones como la parametrización y retraso de solicitudes. | 3           |
| **Iterator**          | Permite recorrer elementos de una colección sin exponer su representación subyacente.                                       | 3           |
| **Mediator**          | Reduce las dependencias caóticas entre objetos al forzar la colaboración a través de un mediador.                           | 0           |
| **Memento**           | Permite guardar y restaurar el estado previo de un objeto sin revelar los detalles de su implementación.                     | 1           |
| **Observer**          | Define un mecanismo de suscripción para notificar a varios objetos sobre eventos en un objeto observado.                     | 3           |
| **State**             | Permite alterar el comportamiento de un objeto cuando su estado interno cambia.                                             | 2           |
| **Strategy**          | Define una familia de algoritmos, colocándolos en clases separadas para hacerlos intercambiables.                           | 3           |
| **Template Method**   | Define el esqueleto de un algoritmo en la superclase, permitiendo sobrescribir pasos en las subclases.                       | 2           |
| **Visitor**           | Separa algoritmos de los objetos sobre los que operan.                                                                      | 1           |
