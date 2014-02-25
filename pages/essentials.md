# Elementos esenciales del MVC

------

Como todas las buenas cosas que usamos hoy en día, el patrón de diseño MVC tuvo su origen en los 70's con [Smalltalk](http://www.pharo-project.org/home), su creador [Trygve Reenskaug](http://en.wikipedia.org/wiki/Trygve_Reenskaug) lo concebía en aquel entonces como:

<blockquote>
  <p>
    MVC fue concebido como una solución general al problema de los usuarios que controlan un conjunto de datos grande y complejo. La parte más difícil fue dar con buenos nombres para los diferentes componentes de la arquitectura. Model-View-Editor fue el primer set. Después de largas discusiones, en particular con Adele Goldberg, terminamos con los términos del Modelo-Vista-Controlador.
  </p>
  <footer>Trygve Reenskaug</footer>
</blockquote>

Hoy en día, hay varias implementaciones de tal patrón, y variaciones como Model-View-Presenter, siendo este último uno de los más usados en la actualidad. Sin embargo, describiremos los términos en los conceptos que nos serán de utilidad para este entrenamiento.

Según [Robert Eckstein](http://www.oreilly.com/pub/au/155) describe los tres elementos del MVC como sigue:

* **Model** - Representa los datos y las reglas que gobiernan el acceso y actualizaciones a dichos datos. En el software empresarial, un modelo a menudo sirve como una aprocimación de software a un proceso de la vida real.
* **View** - La vista renderiza el contenido de un modelo. Específica exactamente como los datos del modelo deben ser presentados. Si los datos del modelo cambian, entonces la vista debe actualizar la presentación en cuanto sea necesario. Esto puede ser mantenido con un modelo _push_, en el cual las vistas se registran con el modelo para notificación de cambios, o un modelo _pull_, en el cual la vista es responsable de llamar el modelo cuando necesite entregar los datos actuales.
* **Controller** - El controller traduce las interacciones del usuario con la vista dentro de las acciones que el modelo realiza. Las interacciones del usuario pueden ser clicks de botón o selecciones de menú. las cuales causarían(hablando de una aplicación web) solicitudes HTTP _GET_ y _POST_. Dependiendo del contexto, un controller quizá seleccione una nueva vista, para ser presentada al usuario.

## Front Controller

El mecanismo de manejo de solicitudes en la capa de presentación debe controlar y coordinar el procesamiento de cada usuario a lo largo de múltiples request. Dichos mecanismos de control pueden ser manejados en cualquier forma: centralizada o descentralizada.

El sistema requiere un punto de acceso centralizado para el manejo de solicitudes en la capa de presentación para soportar la integración de los servicios de sistema, entrega de contenido, administración de vistas, y la navegación. Cuando el usuario accede a la vista directamente sin atravesar un mecanismo centralizado entonces dos problemas ocurren:

* Cada vista proveerá de sus propios servicios, resultando a menudo en código duplicado.
* La navegación de las vistas se le delega a las vistas. Resultando una mezcla de contenidos de vista y navegación.

Adicionalmente, el control distribuido es más difícil de mantener, debido a que los cambios deberán ser aplicados en varios puntos a la vez.

**Solución**

El uso de un controller como el punto inicial de contacto para el manejo de una solicitud. El controller administra el manejo del request, incluyendo la invocación de servicios de seguridad como la autenticación y la autorización, delegando el procesamiento de negocio, administrando la elección de una vista apropiada, manejando errores, y administrando la selección de las estrategias de generación de contenido.

El controller provee de un punto centralizado que controla y administra el manejo de solicitudes Web. Centralizando los puntos de decisión y control, el controller además ayuda a reducir la cantidad de código, la llamada a _scriptlets_ embebidos en los JSP. Con esto último se promueve la reutilización de código a lo largo de la aplicación. Es preferible esta aproximación a embeber lo mismo en múltiples vistas.

Típicamente, un controller se coordina con un componente despachador(_dispatcher_). Dicho _dispatcher_ es responsable por la administración de vistas  y navegación, es decir, escoge la siguiente vista para el usuario y dirige el control de recursos. Los _dispatchers_ quizá están encapsulados dentro del controller directamente o puede ser extraído en un componente separado.

Mientras este patrón sugiere un manejo centralizado del manejo de todos los _requests_, no límita el número de manejadores en el sistema, tal como un Singleton. Una aplicación quizá use múltiples controllers en un sistema, cada uno mapeando a un conjunto distinto de servicios.

![alt front_controller](/img/FCMainClass.gif "front_controller")

Hay varias estrategias para implementar un Front Controller:

* Servlet Front Strategy
* JSP Front Strategy
* Command and Controller Strategy
* Physical Resource Mapping Strategy
* Logical Resource Mapping Strategy
* Multiplexed Resource Mapping Strategy
* **Dispatcher in Controller Strategy**
* Base Front Strategy
* Filter Controller Strategy

<blockquote>
  <p>El patróon Front Controller define un sólo componente que es responsable del procesamiento de las solicitudes a la aplicación. Centraliza las funciones como la selección de vistas, la seguridad y el manejo de plantillas, además de aplicarlo consistentemente a lo largo de todas las vistas o vistas. Consecuentemente, cuando el comportamiento de dichas funciones necesita cambiar, solamente una pequeña parte de la aplicación necesita ser cambiada, que podrías ser el controller o las clases de ayuda.</p>
</blockquote>