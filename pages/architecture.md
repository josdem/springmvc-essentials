# Arquitectura de las aplicaciones Spring MVC

------

Spring Web MVC es un framework diseñado alrededor de un `DispatcherServlet` que atiende las solicitudes de los _requests_ a los manejadores, con _handler mappings_ configurables, resolución de vistas, localización, y resolución de temas, así como el soporte de upload de archivos. Desde Spring 2.5 y reforzado en 3.0, el manejador por default está basado en anotaciones como `@Controller` y `@RequestMapping`, ofreciendo un ampli rango de firmas de métodos. Adicionalmente, desde Spring 3, el uso de anotaciones como `@PathVariable` permiten crear sitios RESTful.

<blockquote>
  <p>SpringMVC esta basado en el principio: Open For extension, Close for modification.</p>
</blockquote>

En SpringMVC se puede usar cualquier objeto como un _command_ o un _form-backing object_; no se necesitan implementar interfaces específicas del framework o heredar de algo. El binding(atado) de datos es altamente flexible, Spring hace las conversiones y tratamientos de los tipos que conforman la estructura del objeto.

La resolución de vistas de Spring es extremadamente flexible. Un `Controller` es tipicamente responsable de preparar un modelo en un `Map` y seleccionando un nombre de vista, aunque se podría escribir directamente al flujo del response para completar el request. La resolución de vistas es muy configurable y flexible, ya sea a través de beans de Spring o los encabezados del request. Se puede integrar directamente con tecnologías como JSP, Velocity, Freemarker, o generar directamente XML y JSON, o bien, generar hojas de cálculo.

### Características de SpringMVC

* Clara separación de roles
* Configuración poderosa y directa del MVC y las clases de la aplicación como JavaBeans
* Adaptabilidad, sin ser invasivo, y flexible
* Código de negocio reusable, evitando la duplicación
* Binding personalizable en conjunto con validaciones
* Manejadores de mapeos y resolución de vistas personalizables
* Transferencia de modelos flexibles hacia las vistas
* Internacionalización y resolución de temas personalizable, con soporte àra JSP's con o sin las librerías de tags de Spring
* Una librería de tags simple y poderosa que provee de características como _data binding_, temas, y generación de formularios mas fáciles de describir
* Beans cuyo ciclo de vida está en el alcance del request actual HTTP o del objeto `Session`

### Recomendaciones para entrar con SpringMVC

* La capa de servicio debe ser transaccional
* Cuando una operación de un Dao de Hibernate o JDBC falle la excepción debe ser trasladada
* Los objetos de la capa de servicio no deben llamar a la capa Web
* Un servicio de negocio que falla con una concurrencia relacionada puede ser reintentado

## DispatcherServlet

![alt dispatcher_servlet](/img/dispatcher_servlet.png "dispatcher_servlet")


<div class="row">
  <div class="col-md-12">
    <h4><i class="icon-code"></i> web.xml</h4>
    <script type="syntaxhighlighter" class="brush: xml;"><![CDATA[

    ]]></script>
  </div>
</div>

* `DispatcherServlet` es un simple Servlet que hereda de HttpServlet y por lo tanto hay que definirla en el descriptor web.xml
* Cada DispatcherServlet tiene su propio ApplicationContext, a este se le denomina WebApplicationContext
* El WebAppCtx va a contener la configuración de los elementos para que SpringMVC funcione
* El WebAppCtx va a poder usar los beans(Repositories, Services, etc.) que se declararon en el contexto de la aplicación
* Tras la inicialización del DS, por convención el framework busca por un archivo con la notación: `{nombre_del_servlet}-servlet.xml`
* Que se debe de ubicar dentro del directorio WEB-INF de la aplicación
* Este archivo contiene todos los Beans de configuración del MVC
* Adicionalmente, podemos cambiar la ubicación de este archivo

## Elementos esenciales de SpringMVC


WebApplicationContext **

Es una extensión del AppCtx de Spring con características adicionales para aplicaciones Web
Se diferencia por que es capaz de resolver temas y conoce el Servlet con el que esta asociado

    Controllers
    Handler Mappings
    View Resolvers
    Locale Resolvers
    Theme Resolver
    Multipart file resolver
    Handler Exception Resolver

## Ciclo de vida del request

![alt lifecycle_request](/img/lifecycle_request.png "lifecycle_request")