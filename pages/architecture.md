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
<web-app xmlns="http://java.sun.com/xml/ns/javaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
  version="3.0">

  <servlet>
    <servlet-name>trackbox</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
  </servlet>

  <servlet-mapping>
    <servlet-name>trackbox</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>

  <!--
    Deshabilita en el contenedor de Servlet el manejo de archivo de
    bienvenida. Necesario para la compatibilidad con Servlet 3.0 y Tomcat
    7.0
  -->
  <welcome-file-list>
    <welcome-file></welcome-file>
  </welcome-file-list>

</web-app>
    ]]></script>
  </div>
</div>

### Servlet 3.0

<div class="row">
  <div class="col-md-12">
    <h4><i class="icon-code"></i> MyWebApplicationInitializer.java</h4>
    <script type="syntaxhighlighter" class="brush: java;"><![CDATA[
package com.makingdevs.practica1;

import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.ServletRegistration;

import org.springframework.web.WebApplicationInitializer;
import org.springframework.web.context.support.XmlWebApplicationContext;
import org.springframework.web.servlet.DispatcherServlet;

public class MyWebApplicationInitializer implements WebApplicationInitializer {

  @Override
  public void onStartup(ServletContext servletContext) throws ServletException {
    XmlWebApplicationContext appContext = new XmlWebApplicationContext();
    appContext.setConfigLocation("/WEB-INF/spring/dispatcher-config.xml");

    ServletRegistration.Dynamic registration = servletContext.addServlet("dispatcher", new DispatcherServlet(appContext));
    registration.setLoadOnStartup(1);
    registration.addMapping("/");
  }

}
    ]]></script>
  </div>
</div>

* `DispatcherServlet` es un simple Servlet que hereda de HttpServlet y por lo tanto hay que definirla en el descriptor web.xml
* Cada `DispatcherServlet` tiene su propio `ApplicationContext`, a este se le denomina `WebApplicationContext`
* Tras la inicialización del `DispatcherServlet`, por convención el framework busca por un archivo con la notación: `{nombre_del_servlet}-servlet.xml`, que se debe de ubicar dentro del directorio WEB-INF de la aplicación
* Este archivo contiene todos los Beans de configuración del MVC
* El `DispatcherServlet` cuenta con las siguientes propiedades:
    * `contextClass` - Clase que implementa el **WebAppCtx**, usada por el Servlet. Default: XmlWebApplicationContext
    * `contextConfigLocation` - Indica donde se puede encontrar la configuración del Servle
    * `namespace` - Namespace del **WebAppCtx**. Default: {nombre-del-servlet}-servlet

<div class="bs-callout bs-callout-info">
<h4><i class="icon-coffee"></i> Información de utilidad</h4>
  <p>
    Te recomendamos profundizar en <a href="http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/servlet/DispatcherServlet.html">la documentación del Dispatcher Servlet de Spring</a>, así conocerás de forma más detallada cuál es el alcance de la configuración que puedes hacer.
  </p>
</div>

<div class="alert alert-success">
  <strong><i class="icon-thumbs-up"></i> Hey!</strong> Recuerda la convención dle nombre del archivo de configuración de Spring con respecto al nombre del Servlet.
</div>

## Elementos esenciales de SpringMVC

### WebApplicationContext

* Es una extensión del `ApplicationContext` de Spring con características adicionales para aplicaciones Web
* Se diferencia por que es capaz de resolver temas y conoce el Servlet con el que esta asociado
* El **WebAppCtx** va a contener la configuración de los elementos para que SpringMVC funcione
* El **WebAppCtx** va a poder usar los beans(Repositories, Services, etc.) que se declararon en el contexto de la aplicación

![alt dispatcher_servlet](http://docs.spring.io/spring/docs/4.0.1.RELEASE/spring-framework-reference/html/images/mvc-contexts.gif "dispatcher_servlet")

Los componentes de un `WebApplicationContext` son:

* Controllers
* Handler Mappings
* View Resolvers
* Locale Resolvers
* Theme Resolver
* Multipart file resolver
* Handler Exception Resolver

## Ciclo de vida del request

![alt lifecycle_request](/img/lifecycle_request.png "lifecycle_request")

<div class="bs-callout bs-callout-info">
<h4><i class="icon-coffee"></i> Información de utilidad</h4>
  <p>
    Te recomendamos que también veas <a href="http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/context/support/AnnotationConfigWebApplicationContext.html">la documentación del AnnotationConfigWebApplicationContext</a>, podrás conocer otra forma de configurar una aplicación basada en anotaciones de Spring. Además de la interfaz <a href="http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/WebApplicationInitializer.html">WebApplicationInitializer</a> que tiene información de utilidad.
  </p>
</div>