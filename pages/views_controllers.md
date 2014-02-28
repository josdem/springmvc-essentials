# Vistas y controllers

------

## Declaración de controllers

* Proveen el acceso al comportamiento de la aplicación que definimos a través de interfaces de servicios
* Interpretan  las entradas del usuario y la transforman en un modelo que es representado para el usuario por la vista
* Spring implementa los controllers de una manera muy abstracta, la cual habilita una forma muy variada de declararlos
* Desde la versión 2.5 el modelo de programación está basado en anotaciones
* No necesitan extender clases base o implementar interfaces
* No tienen dependencias directas a la API de Servlet

### Conceptos de `@Controller`

* Indica que una clase en particular sirve del rol de controller
* Actúa como estereotipo para la clase anotada, se pudo haber usado `@Component`
* El dispatcher busca métodos y clases anotados con `@RequestMapping`
* Gracias a las firmas de los métodos variados en un controller se pueden formular muchos tipos de los mismos
* Debemos de habilitar la autodetección de dichas clases para agregarlas al AppCtx

### Uso de `@RequestMapping`

* Mapea un URL a una clase específica o a un método manejador en particular
* Típicamente la anotación a nivel de clase mapea a un controlador para formularios, complementándolas con anotaciones a nivel de método indicando un método específico HTTP(GET/POST) o parámetros específicos HTTP 
* Está anotación no es requerida a nivel de clase 
    * Los paths pasan de ser relativos a absolutos
* Soporta Ant paths

<div class="row">
  <div class="col-md-6">
    <h4><i class="icon-code"></i> ProjectController.java</h4>
    <script type="syntaxhighlighter" class="brush: java;"><![CDATA[
package com.makingdevs.practica2;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class ProjectController {

  @RequestMapping("/project")
  public String allProjects(Model model) {
    model.addAttribute("message", "Welcome to projects manager!");
    return "project/list";
  }
}
// You must add @ComponentScan(basePackages = { "com.makingdevs.practica2" })
// or <context:component-scan base-package="com.makingdevs.practica2"/>
// depending on your configuration
  ]]></script>
  </div>
  <div class="col-md-6">
    <h4><i class="icon-code"></i> project/list.jsp</h4>
    <script type="syntaxhighlighter" class="brush: html;"><![CDATA[
<%@ page language="java" contentType="text/html; charset=UTF-8"
  pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html lang="en">
  <head>
    
  </head>
  <body>

    <div class="container">
      <h1>${message}</h1>
    </div> <!-- /container -->
    
  </body>
</html>
    ]]></script>
  </div>
</div>

## Declaración de View Resolvers

<blockquote>
  <p>Los métodos de los controllers siempre regresarán de form explícita o implícita un <code>ModelAndView</code></p>
</blockquote>

* Spring provee ViewResolvers, que permiten mostrar modelos de datos sin atarnos a una tecnología en particular
* Todos los controllers resuelven a una view de manera implícita o explícita
* Las dos interfaces principales a manejar son:
    * `ViewResolver` - Provee el mapeo entre los nombres de vista
    * `View - Direcciona la preparación del request y maneja el request sobre una tecnología de vista

<div class="row">
  <div class="col-md-6">
    <h4><i class="icon-code"></i> trackbox-servlet.xml</h4>
    <script type="syntaxhighlighter" class="brush: xml;"><![CDATA[
  <bean id="jspViewResolver"
    class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="viewClass"
      value="org.springframework.web.servlet.view.JstlView" />
    <property name="prefix" value="/WEB-INF/jsp/" />
    <property name="suffix" value=".jsp" />
  </bean>
    ]]></script>
  </div>
  <div class="col-md-6">
    <h4><i class="icon-code"></i> WebConfig.java</h4>
    <script type="syntaxhighlighter" class="brush: java;"><![CDATA[
  @Bean
  public ViewResolver viewResolver() {
    InternalResourceViewResolver resolver = new InternalResourceViewResolver();
    resolver.setViewClass(JstlView.class);
    resolver.setPrefix("/WEB-INF/jsp/");
    resolver.setSuffix(".jsp");
    return resolver;
  }

    ]]></script>
  </div>
</div>

Cuando un controller regresa una view de nombre _currentView_, entonces el `ViewResolver` buscará por: __/WEB-INF/jsp/currentView.jsp__

## Implementación de FormController

**JSP & JSTL**

* Cuando usamos JSTL necesitamos una clase especial de vista(JstlView), y ésta es preparada previamente
* Spring provee para las views de una librería que ayuda a facilitar el binding de objetos de un formulario, entre algunas funcionalidades más
* Cada tag provee un conjunto de atributos correspondientes al tipo de elemento para no dejar de lado la funcionalidad
* Solo hay que agregar la taglib al encabezado de la View: `<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>`

**TagLib `spring-form.tld`**

* `checkbox`
* `checkboxes`
* `errors`
* `form`
* `hidden`
* `input`
* `label`
* `option`
* `options`
* `password`
* `radiobutton`
* `radiobuttons`
* `select`
* `textarea`

<div class="row">
  <div class="col-md-12">
    <h4><i class="icon-code"></i> ProjectController.java</h4>
    <script type="syntaxhighlighter" class="brush: java;"><![CDATA[
package com.makingdevs.practica2;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

import com.makingdevs.model.Project;
import com.makingdevs.repositories.ProjectRepository;
import com.makingdevs.services.ProjectService;

@Controller
public class ProjectController {
  
  @Autowired
  ProjectRepository projectRepository;
  
  @Autowired
  ProjectService projectService;

  @RequestMapping("/project")
  public String allProjects(Model model) {
    model.addAttribute("message", "Welcome to projects manager!");
    model.addAttribute("projects",projectRepository.findAll());
    return "project/list";
  }
  
  @RequestMapping(value="/newProject",method=RequestMethod.GET)
  public String createNewProject(Model model) {
    Project project = new Project();
    model.addAttribute(project);
    return "project/new";
  }
  
  @RequestMapping(value="/saveProject",method=RequestMethod.POST)
  public String saveProject(Project project) {
    projectService.createNewProject(project);
    return "redirect:/project";
  }
  
}
    ]]></script>
  </div>
</div>

<div class="row">
  <div class="col-md-6">
    <h4><i class="icon-code"></i> project/list.jsp</h4>
    <script type="syntaxhighlighter" class="brush: html;"><![CDATA[
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<!-- document body -->
<div class="container">
  <h1>${message}</h1>
</div>

<div class="container">
  <ul>
  <c:forEach items="${projects}" var="project" >
    <li>${project.codeName}</li>
  </c:forEach>
  </ul>
  <hr>
  <a href="${pageContext.request.contextPath}/newProject" class="btn btn-primary">
    Create a new project
  </a>
</div>
    ]]></script>
  </div>
  <div class="col-md-6">
    <h4><i class="icon-code"></i> project/new.jsp</h4>
    <script type="syntaxhighlighter" class="brush: html;"><![CDATA[
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<!-- document body -->
<form:form commandName="project" method="post" action="${pageContext.request.contextPath}/saveProject">
  <div class="form-group">
    <label for="name">Name</label>
    <form:input path="name" htmlEscape="true" placeholder="New project" class="form-control"/>
  </div>
  <div class="form-group">
    <label for="codeName">Code Name</label>
    <form:input path="codeName" htmlEscape="true" placeholder="PROJECT-CODE" class="form-control"/>
  </div>
  <div class="form-group">
    <label for="description">Description</label>
    <form:textarea path="description" htmlEscape="true" class="form-control" rows="3"/>
  </div>          
  <button type="submit" class="btn btn-default">Create a new project</button>
</form:form>
    ]]></script>
  </div>
</div>

