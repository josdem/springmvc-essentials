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
  <div class="col-md-12">
    <h4><i class="icon-code"></i> trackbox-servlet.java</h4>
    <script type="syntaxhighlighter" class="brush: java; highlight:[14];"><![CDATA[
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
</div>

<div class="bs-callout bs-callout-info">
<h4><i class="icon-coffee"></i> Información de utilidad</h4>
  <p>
    Te recomendamos que también veas <a href="http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/context/support/AnnotationConfigWebApplicationContext.html">la documentación del AnnotationConfigWebApplicationContext</a>, podrás conocer otra forma de configurar una aplicación basada en anotaciones de Spring. Además de la interfaz <a href="http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/WebApplicationInitializer.html">WebApplicationInitializer</a> que tiene información de utilidad.
  </p>
</div>

## `ContextLoaderListener`



