# Configuración esencial de SpringMVC

------

Hay dos formas de configurar SpringMVC : con Java Config y con el namespace XML.

Ambos enfoques proveen configuración por default que sobreescribe la del `DispatcherServlet`. El objetivo es reponer la mayoría de la aplicaciones de tener de crear la misma configuración y además de proveer constructores de alto nivel para que SpringMVC sirva de un punto de comienzo simple, y requiera poco o nulo conocimiento de tal configuración.

No hay ventajas entre configuraciones, sólo gustos y preferencias.

## Uso del namespace MVC de SpringMVC

<div class="row">
  <div class="col-md-12">
    <h4><i class="icon-code"></i> trackbox-servlet.java</h4>
    <script type="syntaxhighlighter" class="brush: java; highlight:[7];"><![CDATA[
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mvc="http://www.springframework.org/schema/mvc"
  xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd
    http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

  <mvc:annotation-driven />

  <mvc:view-controller path="/" view-name="home" />

  <mvc:resources location="/libs/" mapping="/static/**" />

  <bean id="jspViewResolver"
    class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="viewClass"
      value="org.springframework.web.servlet.view.JstlView" />
    <property name="prefix" value="/WEB-INF/jsp/" />
    <property name="suffix" value=".jsp" />
  </bean>

</beans>
    ]]></script>
  </div>
</div>

Lo anterior registra varios beans, un `RequestMappingHandlerMapping`, un `RequestMappingHandlerAdapter` y un `ExceptionHandlerExceptionResolver` con el soporte de procesar los requests con métodos de controladores anotados con `@RequestMapping`, `@ExceptionHandler`, y otros más.

## Uso del MVC Java Config

<div class="row">
  <div class="col-md-6">
    <h4><i class="icon-code"></i> WebConfig.java</h4>
    <script type="syntaxhighlighter" class="brush: java; highlight:[14,15];"><![CDATA[
package com.makingdevs.practica1;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.ViewResolver;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter;
import org.springframework.web.servlet.view.InternalResourceViewResolver;
import org.springframework.web.servlet.view.JstlView;

@Configuration
@EnableWebMvc
public class WebConfig extends WebMvcConfigurerAdapter {

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/static/**").addResourceLocations("/libs/");
    }
    
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("home");
    }
    
    @Bean
    public ViewResolver viewResolver(){
      InternalResourceViewResolver resolver = new InternalResourceViewResolver();
      resolver.setViewClass(JstlView.class);
      resolver.setPrefix("/WEB-INF/jsp/");
      resolver.setSuffix(".jsp");
      return resolver;
    }

}
    ]]></script>
  </div>
  <div class="col-md-6">
    <h4><i class="icon-code"></i> web.xml</h4>
    <script type="syntaxhighlighter" class="brush: xml;"><![CDATA[
<web-app xmlns="http://java.sun.com/xml/ns/javaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
  version="3.0">

  <servlet>
    <servlet-name>trackbox</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
    <init-param>
      <param-name>contextClass</param-name>
      <param-value>org.springframework.web.context.support.AnnotationConfigWebApplicationContext</param-value>
    </init-param>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>com.makingdevs.practica1.WebConfig</param-value>
    </init-param>

  </servlet>

  <servlet-mapping>
    <servlet-name>trackbox</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>

  <!-- Deshabilita en el contenedor de Servlet el manejo de archivo de bienvenida. 
    Necesario para la compatibilidad con Servlet 3.0 y Tomcat 7.0 -->
  <welcome-file-list>
    <welcome-file></welcome-file>
  </welcome-file-list>

</web-app>
    ]]></script>
  </div>
</div>

<div class="bs-callout bs-callout-info">
<h4><i class="icon-coffee"></i> Información de utilidad</h4>
  <p>
    Absolutamente, toda la configuración que puedes hacer con XML la puedes realizar con anotaciones, y viceversa. Sin embargo, la diferencia radicara en las interfaces que deberás implementar si optas por usar Java Config, pero no te preocupes, no son díficiles y están bien documentadas.
  </p>
</div>

## [`ContextLoaderListener`](http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/context/ContextLoaderListener.html)

Uno de los conceptos expuestos por Spring en el modelo de aplicación ligero es la arquitectura por capas. Retomando la arquitectura _clásica_ por capas, la capa web es una más de todas; sirve como punto de entrada dentro de una aplicación para delegar a objetos de servicio, cualquier otro objeto de negocio, DAO's, etc; tales objetos existen en una capa distinta dentro de otro _contexto_, **¿cómo usamos y añadimos beans del contenedor de Spring dentro de nuestra aplicación?**

<div class="row">
  <div class="col-md-12">
    <h4><i class="icon-code"></i> WebConfig.java</h4>
    <script type="syntaxhighlighter" class="brush: xml;"><![CDATA[
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>

  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:/**AppCtx.xml</param-value>
  </context-param>
  ]]></script>
  </div>
</div>

Si no indicas el `contextConfigLocation`, entonces el listener buscará por un archivo ubicado en el directorio de la aplicación `WEB-INF/applicationContext.xml` que debe definir la carga de los beans de la aplicación. Una vez que los archivos son cargados al contexto, Spring crea el `WebApplicationContext` basado en las definiciones de los beans y los almacena en el `ServletContext` de la aplicación. 

Un aspecto importante a considerar es que, no se crean varios application context, en lugar de ello, todo se va al mismo contenedor que se esta definiendo por DispatcherServlet, y los beans del contenedor pueden ser usados entre ellos por que están dentro del mismo contexto.

<div class="bs-callout bs-callout-info">
<h4><i class="icon-coffee"></i> Información de utilidad</h4>
  <p>
    Si no estás usando SpringMVC y deseas usar algún otro framework o Servlet puro, este listener es el indicado, y la forma en la cual obtienes el application context es: <code>WebApplicationContext ctx = WebApplicationContextUtils.getWebApplicationContext(servletContext);</code>
  </p>
</div>