# Mejorando la aplicación

------

Existen elementos dentro del framework que nos ayudan a implementar funcionalidades deseadas, o bien, mejorar la forma en como se comportan los componentes al momento de ejecutarse, por ejemplo, el request o las vistas.

## Interceptores

El manejador de mapeos de Spring incluye un manejador de interceptores, los cuales son útiles para aplicar funcionalidades específicas a ciertos requests.

Los interceptores en el _handler mapping_ deben implementar `HandlerInterceptor`, dicha interface define 3 métodos:

* `boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)`
* `void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView)`
* `void  afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)`

<div class="row">
  <div class="col-md-12">
    <h4><i class="icon-code"></i> TimeOpeningInterceptor.java</h4>
    <script type="syntaxhighlighter" class="brush: java;"><![CDATA[
  package com.makingdevs.practica12;

import java.util.Calendar;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

public class TimeOpeningInterceptor implements HandlerInterceptor {

  private Log log = LogFactory.getLog(TimeOpeningInterceptor.class);
  private int openingTime = 0;
  private int closingTime = 50;

  @Override
  public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
    log.debug("preHandle()");
    log.debug(request);
    log.debug(response);
    log.debug(handler);
    Calendar cal = Calendar.getInstance();
    int hour = cal.get(Calendar.SECOND);
    if (openingTime <= hour && hour < closingTime) {
      return true;
    } else {
      response.sendRedirect("http://makingdevs.com");
      return false;
    }
  }

  @Override
  public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
      ModelAndView modelAndView) throws Exception {
    log.debug("postHandle()");
    log.debug(request);
    log.debug(response);
    log.debug(handler);
    log.debug(modelAndView);
  }

  @Override
  public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)
      throws Exception {
    log.debug("afterCompletion()");
    log.debug(request);
    log.debug(response);
    log.debug(handler);
    log.debug(ex);
  }

}
    ]]></script>
  </div> 
</div>


### Configuración con Java Config

<div class="row">
  <div class="col-md-12">
    <h4><i class="icon-code"></i> WebConfig.java</h4>
    <script type="syntaxhighlighter" class="brush: java;"><![CDATA[
@Configuration
@EnableWebMvc
@ComponentScan(basePackages = { "com.makingdevs.practica7", "com.makingdevs.practica4", "com.makingdevs.practica5",
    "com.makingdevs.practica6", "com.makingdevs.practica9", "com.makingdevs.practica10", "com.makingdevs.practica11" })
public class WebConfig extends WebMvcConfigurerAdapter {
  
  @Override
  public void addInterceptors(InterceptorRegistry registry) {
    registry.addInterceptor(new TimeOpeningInterceptor());
  }

  // Mor configuration

}
    ]]></script>
  </div> 
</div>

### Configuración con el namespace `mvc`

<div class="row">
  <div class="col-md-12">
    <h4><i class="icon-code"></i> trackbox-servlet.xml</h4>
    <script type="syntaxhighlighter" class="brush: xml;"><![CDATA[
<mvc:interceptors>
  <bean class="com.makingdevs.practica12.TimeOpeningInterceptor" />
</mvc:interceptors>
    ]]></script>
  </div> 
</div>

## Internacionalización: LocaleResolver y LocaleChangeInterceptor

La mayoría de los componentes de Spring soportan internacionalización, tal como lo hace también el MVC. El `DispatcherServlet` habilita automáticamente la resolución de mensajes usando la ubicación del cliente. Esto es hecho con los objetos `LocaleResolvers`, y hay varios tipos:

* `AcceptHeaderLocaleResolver`
* `CookieLocaleResolver`
* `SessionLocaleResolver`

<div class="row">
  <div class="col-md-12">
    <h4><i class="icon-code"></i> WebConfig.java</h4>
    <script type="syntaxhighlighter" class="brush: java;"><![CDATA[
@Configuration
@EnableWebMvc
@ComponentScan(basePackages = { "com.makingdevs.practica7", "com.makingdevs.practica4", "com.makingdevs.practica5",
    "com.makingdevs.practica6", "com.makingdevs.practica9", "com.makingdevs.practica10", "com.makingdevs.practica11" })
public class WebConfig extends WebMvcConfigurerAdapter {
  
  @Bean
  public MessageSource messageSource(){
    // Hey ma! Look...
    // http://docs.spring.io/spring/docs/4.0.0.RELEASE/javadoc-api/org/springframework/context/support/ReloadableResourceBundleMessageSource.html
    ReloadableResourceBundleMessageSource ms = new ReloadableResourceBundleMessageSource();
    ms.setBasenames("classpath:/i18n/messages");
    //ms.setDefaultEncoding("UTF-8");
    return ms;
  }
  
  @Bean
  public LocaleResolver localeResolver(){
    SessionLocaleResolver localeResolver = new SessionLocaleResolver();
    localeResolver.setDefaultLocale(new Locale("es"));
    return localeResolver;
  }
  
  @Override
  public void addInterceptors(InterceptorRegistry registry) {
    LocaleChangeInterceptor localeInterceptor = new LocaleChangeInterceptor();
    localeInterceptor.setParamName("lang");
    registry.addInterceptor(new TimeOpeningInterceptor());
    registry.addInterceptor(localeInterceptor).addPathPatterns("/");
  }

  // More beans definition
}
    ]]></script>
  </div> 
</div>

<div class="row">
  <div class="col-md-12">
    <h4><i class="icon-code"></i> trackbox-servlet.xml</h4>
    <script type="syntaxhighlighter" class="brush: xml;"><![CDATA[
<bean id="messageSource"
  class="org.springframework.context.support.ReloadableResourceBundleMessageSource">
  <property name="basename" value="classpath:/i18n/messages" />
</bean>

<bean id="localeResolver"
    class="org.springframework.web.servlet.i18n.SessionLocaleResolver">
    <property name="defaultLocale" value="en"/>
</bean>

<mvc:interceptors>
  <bean class="com.makingdevs.practica12.TimeOpeningInterceptor" />
  <mvc:interceptor>
    <mvc:mapping path="/*" />
    <bean class="org.springframework.web.servlet.i18n.LocaleChangeInterceptor">
      <property name="paramName" value="lang" />
    </bean>
  </mvc:interceptor>
</mvc:interceptors>
    ]]></script>
  </div> 
</div>

<div class="row">
  <div class="col-md-4">
    <h4><i class="icon-code"></i> /i18n/messages_es.properties</h4>
    <script type="syntaxhighlighter" class="brush: plain;"><![CDATA[
home=Inicio
about=Acerca de... 
contact=Contáctanos
welcome=Bienvenido a tu entrenamiento!
    ]]></script>
  </div> 
  <div class="col-md-4">
    <h4><i class="icon-code"></i> /i18n/messages_en.properties</h4>
    <script type="syntaxhighlighter" class="brush: plain;"><![CDATA[
home=Home
about=About us
contact=Contact us
welcome=Welcome to your training!
    ]]></script>
  </div> 
  <div class="col-md-4">
    <h4><i class="icon-code"></i> /i18n/messages_fr.properties</h4>
    <script type="syntaxhighlighter" class="brush: plain;"><![CDATA[
home=Maison
about=à propos de nous 
contact=contactez-nous
welcome=Bienvenue dans votre formation!
    ]]></script>
  </div> 
</div>

Ahora sólo tienes que usar la taglib de Spring para acceder a los mensajes: `<spring:message code="welcome"/>`

### Resolviendo códigos de error a mensajes

<div class="row">
  <div class="col-md-4">
    <h4><i class="icon-code"></i> /i18n/messages_es.properties</h4>
    <script type="syntaxhighlighter" class="brush: plain;"><![CDATA[
home=Inicio
about=Acerca de... 
contact=Contáctanos
welcome=Bienvenido a tu entrenamiento!
name.empty=El nombre es requerido
codename.empty=El código no puede ser vacío
codename.toolong=El nombre código es muy largo
typeMismatch.java.util.Date=El formato de la fecha es incorrecto
    ]]></script>
  </div> 
  <div class="col-md-4">
    <h4><i class="icon-code"></i> /i18n/messages_en.properties</h4>
    <script type="syntaxhighlighter" class="brush: plain;"><![CDATA[
home=Home
about=About us
contact=Contact us
welcome=Welcome to your training!
name.empty=Name required
codename.empty=Code name is required too
codename.toolong=Code Name so long, too long
typeMismatch.java.util.Date=The date is malformed
    ]]></script>
  </div> 
  <div class="col-md-4">
    <h4><i class="icon-code"></i> /i18n/messages_fr.properties</h4>
    <script type="syntaxhighlighter" class="brush: plain;"><![CDATA[
home=Maison
about=à propos de nous 
contact=contactez-nous
welcome=Bienvenue dans votre formation!
name.empty=nom nécessaire
codename.empty=Nom de code est nécessaire aussi
codename.toolong=Nom de code si longtemps, trop longtemps
typeMismatch.java.util.Date=La date est incorrect
    ]]></script>
  </div> 
</div>

<div class="bs-callout bs-callout-info">
<h4><i class="icon-coffee"></i> Información de utilidad</h4>
  <p>
    Te recomendamos checar de nuevo tus formularios, en donde validas con JSR-303 y revisar <a href="http://beanvalidation.org/1.0/spec/#standard-resolver-messages">la documentación de dichos mensajes</a>.
  </p>
</div>

## Decoración: ThemeResolver y ThemeChangeInterceptor

Puedes aplicar temas a SpringMVC para establecer el _look and feel_ de toda la aplicación. Un tema es una colección de recursos estáticos, tipicamente hojas de estilos e imagénes, que afectan el estilo visual de la aplicación.

Para usar temas tenemos que declarar un bean del tipo `org.springframework.ui.context.ThemeSource`. La interfaz `WebApplicationContext` extiende de `ThemeSource` pero delega la responsabilidad a su implementación, por default un `ResourceBundleThemeSource` que implementa cargar los archivos de propiedades del classpath. Para usar una implementación propia de `ThemeSource` se puede registrar un bean con el nombre reservado del _themeSource_. El AppCtx detecta el nombre y lo usa.

Por ejemplo:

```
styleSheet=/themes/amelia/bootstrap.min.css
background=/themes/amelia/img/background.jpg
```

Y de forma análoga como los mensajes en Spring, podemos usar estos con ayuda de la taglib y el tag spring:theme.

<div class="row">
  <div class="col-md-12">
    <h4><i class="icon-code"></i> Using themes</h4>
    <script type="syntaxhighlighter" class="brush: xml;"><![CDATA[
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags"%>
<html>
<head>
  <link rel="stylesheet" href="<spring:theme code=styleSheet/>" type="text/css"/>
</head>
<body style="background=<spring:theme code=background/>">
...
</body>
</html>
    ]]></script>
  </div> 
</div>

En el uso de temas, el `DispatcherServlet` buscará por un bean de nombre _themeResolver_ del tipo `ThemeResolver`, el cual, funciona como el `LocaleResolver`, para detectar el tema a usar en un request en particular. Los que tenemos disponibles son:

* FixedThemeResolver
* SessionThemeResolver
* CookieThemeResolver

Y en conjunto con un `ThemeChangeInterceptor` se atrapa el request para aplicar las acciones de decoración.

<div class="row">
  <div class="col-md-6">
    <h4><i class="icon-code"></i> WebConfig.java</h4>
    <script type="syntaxhighlighter" class="brush: java;"><![CDATA[
  @Bean
  public ThemeResolver themeResolver(){
    SessionThemeResolver themeResolver = new SessionThemeResolver();
    themeResolver.setDefaultThemeName("style.normal");
    return themeResolver;
  }

  @Override
  public void addInterceptors(InterceptorRegistry registry) {
    LocaleChangeInterceptor localeInterceptor = new LocaleChangeInterceptor();
    localeInterceptor.setParamName("lang");
    ThemeChangeInterceptor themeInterceptor = new ThemeChangeInterceptor();
    themeInterceptor.setParamName("theme");
    registry.addInterceptor(new TimeOpeningInterceptor());
    registry.addInterceptor(localeInterceptor).addPathPatterns("/");
    registry.addInterceptor(themeInterceptor).addPathPatterns("/");
  }
    ]]></script>
  </div> 
  <div class="col-md-6">
    <h4><i class="icon-code"></i> trackbox-servlet.xml</h4>
    <script type="syntaxhighlighter" class="brush: xml;"><![CDATA[
<bean id="themeResolver"
  class="org.springframework.web.servlet.theme.SessionThemeResolver">
  <property name="defaultThemeName" value="style.normal" />
</bean>

<mvc:interceptors>
  <bean class="com.makingdevs.practica12.TimeOpeningInterceptor" />
  <mvc:interceptor>
    <mvc:mapping path="/*" />
    <bean
      class="org.springframework.web.servlet.i18n.LocaleChangeInterceptor">
      <property name="paramName" value="lang" />
    </bean>
  </mvc:interceptor>
  <mvc:interceptor>
    <mvc:mapping path="/*" />
    <bean class="org.springframework.web.servlet.theme.ThemeChangeInterceptor">
      <property name="paramName" value="theme" />
    </bean>
  </mvc:interceptor>
</mvc:interceptors>
    ]]></script>
  </div> 
</div>

Define los temas en los archivos de propiedades adecuados:

<div class="row">
  <div class="col-md-4">
    <h4><i class="icon-code"></i> /style/amelia.properties</h4>
    <script type="syntaxhighlighter" class="brush: plain;"><![CDATA[
css=bootstrap/dist/css/amelia.bootstrap.min.css
    ]]></script>
  </div> 
  <div class="col-md-4">
    <h4><i class="icon-code"></i> /style/normal.properties</h4>
    <script type="syntaxhighlighter" class="brush: plain;"><![CDATA[
css=bootstrap/dist/css/bootstrap.min.css
    ]]></script>
  </div> 
  <div class="col-md-4">
    <h4><i class="icon-code"></i> /style/superhero.properties</h4>
    <script type="syntaxhighlighter" class="brush: plain;"><![CDATA[
css=bootstrap/dist/css/superhero.bootstrap.min.css
    ]]></script>
  </div> 
</div>

Y finalmente usalos con ayuda de la taglib de spring: `<spring:theme code='css' />`, por ejemplo:

`<link href="${pageContext.request.contextPath}/static/<spring:theme code='css' />" rel="stylesheet">`

<div class="bs-callout bs-callout-info">
<h4><i class="icon-coffee"></i> Información de utilidad</h4>
  <p>
    Para complementar este tema te recomendamos un decorador de sitios de nombre <a href="http://wiki.sitemesh.org/wiki/display/sitemesh/Home">Sitemesh</a>, el cual te ayuda a concentrar todo el diseño visual de la aplicación en un sólo lugar.
  </p>
</div>

## Manejo de errores en la aplicación

Las implementaciones de `HandlerExceptionResolver` tratan con excepciones inesperadoas que ocurren en la ejecución de los controladores. Es encargado de reensamblar la excepción mapeada en el web.xml. Sin embargo, provee de una forma más sencilla de tratarla. 

Además de la implementación de la interfaz `HandlerExceptionResolver`, al cual solo le importa la implementación del método `ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex);`, contamos con elementos como `SimpleMappingExceptionResolver` o los métodos anotados con `@ExceptionHandler`. Este último funciona sólo sobre las excepciones que pueda arrojar el controlador en el cual esté anotado.

<div class="row">
  <div class="col-md-12">
    <h4><i class="icon-code"></i> SimpleController.java</h4>
    <script type="syntaxhighlighter" class="brush: java;"><![CDATA[
@Controller
public class SimpleController {

  // another methods...

  @ExceptionHandler(IOException.class)
  public ResponseEntity<String> handleIOException(IOException ex) {
    // prepare responseEntity
    return responseEntity;
  }

}    
    ]]></script>
  </div> 
</div>

El valor de `@ExceptionHandler` puede ser un arreglo de tipos de excepciones, que si es lanzada alguna de ellas, entonces este método anotado reaccionará.

Para fines globales de nuestra aplicación implementaremos la interfaz de forma directa.

<div class="row">
  <div class="col-md-6">
    <h4><i class="icon-code"></i> CustomExceptionResolver.java</h4>
    <script type="syntaxhighlighter" class="brush: java;"><![CDATA[
package com.makingdevs.practica13;

import java.util.HashMap;
import java.util.Map;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerExceptionResolver;
import org.springframework.web.servlet.ModelAndView;

@Component
public class CustomExceptionResolver implements HandlerExceptionResolver {

  public ModelAndView resolveException(HttpServletRequest request,
      HttpServletResponse response, Object handler, Exception ex) {
    Map<String,Object> model = new HashMap<String,Object>();
    model.put("ex", ex);
    model.put("message", ex.getMessage());
    return new ModelAndView("handlerException",model);
  }

}
    ]]></script>
  </div> 
  <div class="col-md-6">
    <h4><i class="icon-code"></i> ErrorController.java</h4>
    <script type="syntaxhighlighter" class="brush: java;"><![CDATA[
package com.makingdevs.practica13;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import com.makingdevs.model.Project;
import com.makingdevs.repositories.ProjectRepository;
import com.makingdevs.services.ProjectService;

@Controller
public class ErrorController {
  
  @Autowired
  ProjectRepository projectRepository;
  
  @Autowired
  ProjectService projectService;

  @RequestMapping("/error")
  public void throwError(){
    projectService.createNewProject(new Project());
  }
  
  @RequestMapping("/error/db")
  public void throwDBError(){
    projectRepository.save(new Project());
  }
}
    ]]></script>
  </div> 
</div>

<div class="row">
  <div class="col-md-6">
    <h4><i class="icon-code"></i> handlerException.jsp</h4>
    <script type="syntaxhighlighter" class="brush: html;"><![CDATA[
<div class="container">
  <!-- Main component for a primary marketing message or call to action -->
  <div class="jumbotron">
    <h1>Wops, this feature is new!!!</h1>
    <p>${message}</p>
  </div>
</div> <!-- /container -->

<div class="container">
  <div class="row">
    <div class="col-md-12">
      <div class="alert alert-danger">
        <c:forEach items="${ex.stackTrace}" var="trace">
          ${trace}
        </c:forEach>
      </div>
    </div>
  </div>
</div>
    ]]></script>
  </div> 
</div>

<div class="bs-callout bs-callout-info">
<h4><i class="icon-coffee"></i> Información de utilidad</h4>
  <p>
    Por default, Spring usa <code><a href="http://docs.spring.io/spring/docs/4.0.2.RELEASE/javadoc-api/org/springframework/web/servlet/mvc/support/DefaultHandlerExceptionResolver.html">DefaultHandlerExceptionResolver</a></code>, el cual define códigos de error 4xx y 5xx que permiten el tratamiento de los errores de la aplicación, si te ha fallado la aplicación o haz arrojado alguna excepción entonces ya lo debiste haber visto.
  </p>
</div>