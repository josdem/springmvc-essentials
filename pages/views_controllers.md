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
    * Los paths pasan de ser absolutos a relativos
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

### Tags de Spring MVC

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
import org.springframework.web.servlet.ModelAndView;

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
  
  @RequestMapping(value="/project/new",method=RequestMethod.GET)
  public Project createNewProject() {
    return new Project();
  }
  
  @RequestMapping(value="/saveProject",method=RequestMethod.POST)
  public ModelAndView saveProject(Project project) {
    projectService.createNewProject(project);
    return new ModelAndView("redirect:/project");
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
  <a href="${pageContext.request.contextPath}/project/new" class="btn btn-primary">
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

## URI Templates y MultiActionControllers

Los _URI templates_ pueden ser usados para acceder convenientemente a partes selectas de una URL en un método anotado con `@RequestMapping`.

Un _URI template_ es un String en forma de URI, conteniendo uno o más nombres de variables. Cuando se sustituye los valores en esas variables, el template llega a ser una URI. 

En SpringMVC podemos usar la anotación `@PathVariable` sobre el argumento de un método para atar el valor de la variable del _URI template_.

<div class="row">
  <div class="col-md-6">
    <h4><i class="icon-code"></i> ProjectController.java</h4>
    <script type="syntaxhighlighter" class="brush: java;"><![CDATA[
package com.makingdevs.practica3;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;

import com.makingdevs.model.UserStory;
import com.makingdevs.services.UserStoryService;

@Controller
public class UserStoryController {

  @Autowired
  UserStoryService userStoryService;

  @RequestMapping("/project/{codeName}/userStories")
  public String allProjects(@PathVariable("codeName") String codeName, Model model) {
    List<UserStory> userStories = userStoryService.findUserStoriesByProject(codeName);
    model.addAttribute("project",userStories.get(0).getProject());
    // Hey ma! help me to validate..
    model.addAttribute("userStories",userStories);
    return "userStory/project";
  }

}
// You must add @ComponentScan(basePackages = { "com.makingdevs.practica3" })
// or <context:component-scan base-package="com.makingdevs.practica3"/>
// depending on your configuration
    ]]></script>
  </div>
  <div class="col-md-6">
    <h4><i class="icon-code"></i> ProjectController.java</h4>
    <script type="syntaxhighlighter" class="brush: java;"><![CDATA[
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<!-- html body -->
<div class="container">
  <h1>UserStories of ${project.codeName}</h1>
</div>

<div class="container">
  <ul>
    <c:forEach items="${userStories}" var="us" >
      <li>${us.description}</li>
    </c:forEach>
  </ul>
  <hr>
  <a href="#" class="btn btn-primary">
    Create a new user story
  </a>
</div>
    ]]></script>
  </div>
</div>

Recuerda agregar el link hacia el controller `<a href="${pageContext.request.contextPath}/project/${project.codeName}/userStories">${project.codeName}</a>` en la vista de lista de Projects.

<div class="bs-callout bs-callout-info">
<h4><span class="glyphicon glyphicon-thumbs-up"></span> Es tu turno:</h4>
Implementa el controller para ver los elementos Task que le corresponden a un User Story. Además, la implementación de la creación de nuevas historias de usuario es más trivial, te recomendamos hacerla para ejercitar el uso de formas en los controladores.
</div>

## Métodos manejadores anotados con `@RequestMapping`

Un método manejador anotado con `@RequestMapping` puede tener firmas flexibles. La mayoría de los argumentos puede ser usados en orden arbitrario con la única excepción de `BindingResults`.

Los objetos que pueden ir firmados como argumentos en un método con `@RequestMapping` son:

* `HttpServletRequest` / `HttpServletResponse`
* `HttpSession`
* `org.springframework.web.context.request.WebRequest` / `org.springframework.web.context.request.NativeWebRequest` ( Parámetros Génericos)
* `java.util.Locale`
* `java.io.InputStream` / `java.io.Reader`
* `java.io.OutputStream` / `java.io.Writer`
* `@PathVariable` + variable
* `@RequestParam` + parámetro
* `@RequestHeader` + Asignación
* `java.util.Map` / `org.springframework.ui.Model` / `org.springframework.ui.ModelMap`
* Command u objetos de formulario para atar parámetros(`@InitBinder`)
* `org.springframework.validation.Errors` / `org.springframework.validation.BindingResult`
* `org.springframework.web.bind.support.SessionStatus`(`@SessionAttributes`)* 

Los tipos de retorno que pueden regresar los métodos firmados con `@RequestMapping` son:

* `ModelAndView`
* `Model`
* `Map`
* `View`
* `String`
* `void`
* Cualquier objeto que sea considerado un atributo del modelo a ser expuesto por una vista

El hecho de que no exista la necesidad de especificar una vista de forma explícita es gracias a `@ModelAttribute` y `RequestToViewNameTranslator`

<div class="row">
  <div class="col-md-12">
    <h4><i class="icon-code"></i> ExplorerController.java</h4>
    <script type="syntaxhighlighter" class="brush: java;"><![CDATA[
package com.makingdevs.practica4;

// A lot of imports

@Controller
@RequestMapping("/explorer")
public class ExplorerController {

  private Log log = LogFactory.getLog(ExplorerController.class);

  @RequestMapping("/requestAndResponse")
  public void explorarRequestAndResponse(HttpServletRequest request, HttpServletResponse response) {
    log.debug("\nRequest:\t" + ToStringBuilder.reflectionToString(request));
    log.debug("\nResponse:\t" + ToStringBuilder.reflectionToString(response));
  }

  @RequestMapping("/session")
  public String explorarSession(HttpSession session) {
    log.debug("\nSession:\t" + ToStringBuilder.reflectionToString(session));
    return "helloWorld";
  }

  @RequestMapping("/logout")
  public String logout(HttpSession session) {
    log.debug("\nSession:\t" + ToStringBuilder.reflectionToString(session));
    session.invalidate();
    return "helloWorld";
  }

  @RequestMapping("localeAndStream")
  public Map<String, Object> explorarLocaleAndStream(Locale locale, Reader reader, Writer writer) throws IOException {
    log.debug("\nLocale:\t" + ToStringBuilder.reflectionToString(locale));
    log.debug("\nInputStream:\t" + ToStringBuilder.reflectionToString(reader));
    log.debug("\nOutputStream:\t" + ToStringBuilder.reflectionToString(writer));
    return new HashMap<String, Object>();
  }

  @RequestMapping(value = "/commandErrors", method = RequestMethod.GET)
  public Model explorarCommandErrorsSessionStatus(ModelMap modelMap) {
    Model model = new ExtendedModelMap();
    model.addAttribute(new Task());
    log.debug("\nModel:\t" + ToStringBuilder.reflectionToString(model));
    log.debug("\nModelMap:\t" + ToStringBuilder.reflectionToString(modelMap));
    return model;
  }

  @RequestMapping(value = "/commandErrors", method = RequestMethod.POST)
  public String explorarCommandErrorsSessionStatus(Task task, Errors errors) {
    log.debug("\nObjeto:\t" + ToStringBuilder.reflectionToString(task));
    log.debug("\nErrors:\t" + ToStringBuilder.reflectionToString(errors));
    return "helloWorld";
  }
}
    ]]></script>
  </div>
</div>

## Captura de parámetros - `@RequestParam`

* Lo usamos para ligar parámetros del request a un parámetro del método en el controller
* Son requeridos por default, pero podemos cambiar el comportamiento con el atributo `required=false`
* Estos parámetros pueden ser recibidos a través de _GET_ o de _POST_, con la respectiva formación de envío

### `@CookieValue`

Ata un parámetro del método al valor HTTP de la cookie

<div class="row">
  <div class="col-md-12">
    <h4><i class="icon-code"></i> SearchController.java</h4>
    <script type="syntaxhighlighter" class="brush: java;"><![CDATA[
package com.makingdevs.practica5;

import java.util.HashMap;
import java.util.List;
import java.util.Map;

import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServletResponse;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.CookieValue;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;

import com.makingdevs.model.Task;
import com.makingdevs.model.UserStory;
import com.makingdevs.repositories.TaskRepository;
import com.makingdevs.repositories.UserStoryRepository;

@Controller
@RequestMapping(value = "/search/**")
public class SearchController {

  private Log log = LogFactory.getLog(SearchController.class);

  @Autowired
  UserStoryRepository userStoryRepository;

  @Autowired
  TaskRepository taskRepository;

  @RequestMapping(method = RequestMethod.GET)
  public String searchInProjects(HttpServletResponse response, @CookieValue(value = "queryCounter", defaultValue = "0") Integer queryCounter) {
    queryCounter++;
    log.debug("This is the " + queryCounter + " time!");
    Cookie cookie = new Cookie("queryCounter", queryCounter.toString());
    response.addCookie(cookie);
    return "search";
  }

  @RequestMapping(method = RequestMethod.POST)
  public Map<String, Object> searchResults(@RequestParam("minValue") Integer minValue,
      @RequestParam("maxValue") Integer maxValue, @RequestParam("taskDescription") String taskDescription) {
    Map<String, Object> model = new HashMap<String, Object>();
    List<UserStory> userStories = userStoryRepository.findAllByEffortBetween(minValue, maxValue);
    List<Task> tasks = taskRepository.findAllByDescriptionLike("%" + taskDescription + "%");
    model.put("minValue", minValue);
    model.put("maxValue", maxValue);
    model.put("taskDescription", taskDescription);
    model.put("userStories", userStories);
    model.put("tasks", tasks);
    return model;
  }
}
    ]]></script>
  </div>
</div>

<div class="row">
  <div class="col-md-12">
    <h4><i class="icon-code"></i> search.jsp</h4>
    <script type="syntaxhighlighter" class="brush: java;"><![CDATA[
<div class="container">
  <h1>Search in projects</h1>
</div>

<div class="container">
  <form action="${pageContext.request.contextPath}/search" method="post">
    <div class="form-group">
      <label for="name">User Story effort</label>
      <!-- Hey ma! Help me to set the selected value -->
      <select class="form-control" name="minValue">
        <option>1</option>
        <option>2</option>
        <option>3</option>
        <option>4</option>
        <option>5</option>
      </select>
      And
      <select class="form-control" name="maxValue">
        <option>1</option>
        <option>2</option>
        <option>3</option>
        <option>4</option>
        <option>5</option>
      </select>
    </div>
    <div class="form-group">
      <label for="name">Task description</label>
      <input name="taskDescription" placeholder="What did you write?" class="form-control" value="${taskDescription}">
    </div>
    <button type="submit" class="btn btn-primery">Search in projects</button>
  </form>
</div>
<hr>
<div class="container">
  <div class="row">
    <div class="col-md-6">
      <h3>UserStories <small>${minValue} and ${maxValue}</small></h3>
      <ul>
      <c:forEach items="${userStories}" var="us">
        <li>${us.effort} - ${us.description}</li>
      </c:forEach>
      </ul>
    </div>
    <div class="col-md-6">
      <h3>Tasks <small>${taskDescription}</small></h3>
      <ul>
      <c:forEach items="${tasks}" var="task">
        <li>${task.description}</li>
      </c:forEach>
      </ul> 
    </div>
  </div>
</div>
    ]]></script>
  </div>
</div>

## Validaciones en formularios

El DataBinding es útil para permitir que la entrada del usuario sea dinámicamente atado a un modelo de dominio de una aplicación, o cualquier objeto que usemos para procesarlo. El `Validator` y el `DataBinder` son fundamentales en el paquete `validation`.

### Validando usando la interface `Validator`

Spring cuenta con la interfaz `Validation` que se puede utilizar para validar los objetos, la cual, trabaja con el objeto `Errors` mientras se valida, de tal manera que se pueden alimentar de datos a dicho objeto.

Proveeremos del comportamiento de validación implementando los métodos de la interfaz `org.springframework.validation.Validator`:

* `supports(Class)` - Puede esta clase validar a los objetos que se le indiquen?
* `validate(Object, org.springframework.validation.Errors)` - Validar el objeto dado y en caso de errores, entonces los registra dentro de `Errors`

<div class="row">
  <div class="col-md-6">
    <h4><i class="icon-code"></i> ProjectValidator.java</h4>
    <script type="syntaxhighlighter" class="brush: java;"><![CDATA[
package com.makingdevs.practica6;

import org.springframework.validation.Errors;
import org.springframework.validation.ValidationUtils;
import org.springframework.validation.Validator;

import com.makingdevs.model.Project;

public class ProjectValidator implements Validator {

  @Override
  public boolean supports(Class clazz) {
    return Project.class.equals(clazz);
  }

  @Override
  public void validate(Object object, Errors errors) {
    Project project = (Project) object;
    ValidationUtils.rejectIfEmptyOrWhitespace(errors, "name", "name.empty","The name is required");
    ValidationUtils.rejectIfEmptyOrWhitespace(errors, "codeName", "codename.empty","The code name is required");
    if(project.getCodeName().length() >= 20)
      errors.rejectValue("codeName", "codename.toolong", "The code name is too long");
  }

}
    ]]></script>
  </div>
  <div class="col-md-6">
    <h4><i class="icon-code"></i> ProjectValidationController.java</h4>
    <script type="syntaxhighlighter" class="brush: java;"><![CDATA[
package com.makingdevs.practica6;

import javax.validation.Valid;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.WebDataBinder;
import org.springframework.web.bind.annotation.InitBinder;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.servlet.ModelAndView;

import com.makingdevs.model.Project;
import com.makingdevs.repositories.ProjectRepository;
import com.makingdevs.services.ProjectService;

@Controller
public class ProjectValidationController {

  @Autowired
  ProjectRepository projectRepository;

  @Autowired
  ProjectService projectService;

  @RequestMapping("/project")
  public String allProjects(Model model) {
    model.addAttribute("message", "Welcome to projects manager!");
    model.addAttribute("projects", projectRepository.findAll());
    return "project/list";
  }

  @RequestMapping(value = "/project/new", method = RequestMethod.GET)
  public Project createNewProject() {
    return new Project();
  }

  @RequestMapping(value = "/saveProject", method = RequestMethod.POST)
  //public ModelAndView saveProject(@Validated Project project, BindingResult binding) {
  public ModelAndView saveProject(@Valid Project project, BindingResult binding) {
    if (binding.hasErrors()) {
      ModelAndView mv = new ModelAndView("project/new");
      mv.getModel().put("project", project);
      return mv;
    } else {
      projectService.createNewProject(project);
      return new ModelAndView("redirect:/project");
    }
  }

  @InitBinder
  public void initBinder(WebDataBinder binder) {
    binder.addValidators(new ProjectValidator());
  }

}
// You must add @ComponentScan(basePackages = { "com.makingdevs.practica6" })
// or <context:component-scan base-package="com.makingdevs.practica6"/>
// and remove the package scan com.makingdevs.practica2
    ]]></script>
  </div>
</div>

<div class="row">
  <div class="col-md-12">
    <h4><i class="icon-code"></i> ProjectValidator.java</h4>
    <script type="syntaxhighlighter" class="brush: html;"><![CDATA[
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags"%>
<!-- More head content -->
<div class="container">
  <div class="row">
    <div class="page-header">
      <h1>Create a new project</h1>
    </div>
  </div>
  <div class="row">
    <div class="col-md-12">

      <form:form commandName="project" method="post" action="${pageContext.request.contextPath}/saveProject">
        
        <div class="form-group">
          <label class="control-label" for="name">Name</label>
          <form:input path="name" htmlEscape="true" placeholder="New project" class="form-control"/>
          <span class="control-label">${status.errorCode}</span>
          <form:errors path="name" element="span"/>
        </div>
        
        
        <div class="form-group">
          <label class="control-label" for="codeName">Code Name</label>
          <form:input path="codeName" htmlEscape="true" placeholder="PROJECT-CODE" class="form-control"/>
          <span class="control-label">${status.errorCode}</span>
          <form:errors path="codeName" element="span"/>
        </div>
        
        <div class="form-group">
          <label class="control-label" for="description">Description</label>
          <form:textarea path="description" htmlEscape="true" class="form-control" rows="3"/>
          <span class="control-label">${status.errorCode}</span>
          <form:errors path="description" element="span"/>
        </div>
          
        <button type="submit" class="btn btn-default">Create a new project</button>
      </form:form>

    </div>
  </div>
</div>
    ]]></script>
  </div>
</div>

### Validando con el JSR-303

Para obtener información general sobre el JSR-303, consulta el [Bean Validation Specification](https://jcp.org/en/jsr/detail?id=303). Para obtener información sobre las capacidades específicas de la implementación de referencia predeterminada, consulta la documentación de [Hibernate Validator](http://hibernate.org/validator/).

<div class="row">
  <div class="col-md-6">
    <h4><i class="icon-code"></i> UserStoryCommand.java</h4>
    <script type="syntaxhighlighter" class="brush: java;"><![CDATA[
package com.makingdevs.practica7;

// A lot of imports

public class UserStoryCommand {
  
  private Long id;
  
  @NotNull
  @Size(min = 0, max = 1000)
  private String description;
  
  @Min(1)
  @Max(99)
  private Integer priority;
  
  @Range(min = 1, max = 5)
  private Integer effort;
  
  private Project project;

  public UserStoryCommand() {
    super();
    this.project = new Project();
  }
  
  public UserStory getUserStory(){
    UserStory us = new UserStory();
    us.setId(id);
    us.setDateCreated(new Date());
    us.setLastUpdated(new Date());
    us.setDescription(description);
    us.setPriority(priority);
    us.setEffort(effort);
    us.setProject(project);
    return us;
  }

  // Getters and setters

}
    ]]></script>
  </div>
  <div class="col-md-6">
    <h4><i class="icon-code"></i> UserCommand.java</h4>
    <script type="syntaxhighlighter" class="brush: java;"><![CDATA[
package com.makingdevs.practica7;

// More imports

public class UserCommand {
  private Long id;
  @Email
  @NotBlank
  private String username;
  @NotNull
  @AssertTrue
  private boolean enabled;
  
  public User getUser(){
    User user = new User();
    user.setId(id);
    user.setEnabled(enabled);
    user.setUsername(username);
    return user;
  }
  
  // Getters and setters
  
}

    ]]></script>
  </div>
</div>

<div class="row">
  <div class="col-md-12">
    <h4><i class="icon-code"></i> UserStoryController.java</h4>
    <script type="syntaxhighlighter" class="brush: java;"><![CDATA[
    ]]></script>
  </div>
</div>

<div class="row">
  <div class="col-md-12">
    <h4><i class="icon-code"></i> form.jsp</h4>
    <script type="syntaxhighlighter" class="brush: html;"><![CDATA[
    ]]></script>
  </div>
</div>

### Resolving codes to error messages

## Personalizando el paso de datos con `@InitBinder`

Anotando los métodos del controlador con `@InitBinder` permite configurar los datos del request directamente dentro de la misma clase. `@InitBinder` identifica los métodos que inicializan el `WebDataBinder` que se utiliza para rellenar el _command_ y el objeto de la forma.

<div class="row">
  <div class="col-md-6">
    <h4><i class="icon-code"></i> SprintCommand.java</h4>
    <script type="syntaxhighlighter" class="brush: java;"><![CDATA[
package com.makingdevs.practica8;

import java.util.Date;

import javax.validation.constraints.Future;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Size;

import org.hibernate.validator.constraints.NotBlank;

import com.makingdevs.model.Project;
import com.makingdevs.model.Sprint;

public class SprintCommand {
  private Long id;
  @NotBlank
  @Size(min = 1, max = 100)
  private String name;
  @Size(min = 0, max = 1000)
  private String description;
  @NotNull
  private Date startDate;
  @Future
  @NotNull
  private Date endDate;
  @NotNull
  private Project project;

  public SprintCommand() {
    super();
    project = new Project();
  }

  public Sprint getSprint() {
    Sprint sprint = new Sprint();
    sprint.setId(id);
    sprint.setDescription(description);
    sprint.setEndDate(endDate);
    sprint.setStartDate(startDate);
    sprint.setName(name);
    sprint.setProject(project);
    return sprint;
  }

  // Getters and setters

}

    ]]></script>
  </div>
  <div class="col-md-6">
    <h4><i class="icon-code"></i> SprintController.java</h4>
    <script type="syntaxhighlighter" class="brush: java;"><![CDATA[
package com.makingdevs.practica8;

import java.text.SimpleDateFormat;
import java.util.Date;

import javax.validation.Valid;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.propertyeditors.CustomDateEditor;
import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.validation.Errors;
import org.springframework.web.bind.WebDataBinder;
import org.springframework.web.bind.annotation.InitBinder;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

import com.makingdevs.repositories.ProjectRepository;
import com.makingdevs.services.SprintService;

@Controller
@RequestMapping("/sprint/")
public class SprintController {

  @Autowired
  ProjectRepository projectRepository;

  @Autowired
  SprintService sprintService;

  @RequestMapping(value = { "/new", "/create" }, method = RequestMethod.GET)
  public String crearSprint(ModelMap model) {
    model.addAttribute("sprintCommand", new SprintCommand());
    model.addAttribute(projectRepository.findAll());
    return "sprint/new";
  }

  @RequestMapping(value = { "/save", "/persist" }, method = RequestMethod.POST)
  public String persistirSprint(@Valid SprintCommand sprintCommand, Errors errors, ModelMap model) {
    model.addAttribute(projectRepository.findAll()); // Wuack!!!!, this again...
    if (errors.hasErrors()) {
      model.addAttribute("sprintCommand", sprintCommand);
      return "sprint/new";
    } else {
      sprintService.createSprintForOneproject(sprintCommand.getSprint());
      return "redirect:/";
    }
  }
}
    ]]></script>
  </div>
</div>

Dichos métodos de init-binder soportan todos los argumentos del `@RequestMapping`, excepto por commands y su correspondiente objeto de validación. Estos métodos no deben de regresar ningún valor, y tipicament, incluyen objetos como: `WebDataBinder`, `WebRequest` o `java.util.Locale`.

<div class="row">
  <div class="col-md-12">
    <h4><i class="icon-code"></i> SprintController.java</h4>
    <script type="syntaxhighlighter" class="brush: java;"><![CDATA[
  @InitBinder
  public void initBinder(WebDataBinder binder) {
    SimpleDateFormat dateFormat = new SimpleDateFormat("dd/MM/yyyy");
    dateFormat.setLenient(false);
    binder.registerCustomEditor(Date.class, new CustomDateEditor(dateFormat, false));
  }
    ]]></script>
  </div>
</div>

<div class="row">
  <div class="col-md-12">
    <h4><i class="icon-code"></i> new.jsp</h4>
    <script type="syntaxhighlighter" class="brush: html;"><![CDATA[
<div class="container">
  <div class="row">
    <div class="page-header">
      <h1>Create a new Sprint</h1>
    </div>
  </div>
  <div class="row">
    <div class="col-md-12">

      <form:form commandName="sprintCommand" method="post" action="${pageContext.request.contextPath}/sprint/save">
        
        <div class="form-group">
          <label class="control-label" for="name">Project</label>
          <form:select path="project.id" items="${projectList}" itemValue="id" itemLabel="codeName" class="form-control"/>
          <form:errors path="project" element="span"/>
        </div>
        
        <div class="form-group">
          <label class="control-label" for="name">Name</label>
          <form:input path="name" htmlEscape="true" placeholder="New project" class="form-control"/>
          <form:errors path="name" element="span"/>
        </div>
        
        <div class="form-group">
          <label class="control-label" for="description">Description</label>
          <form:textarea path="description" htmlEscape="true" class="form-control" rows="3"/>
          <form:errors path="description" element="span"/>
        </div>
        
        <div class="row">
          <div class="col-xs-6">
            <label class="control-label" for="startDate">Start date:</label>
            <form:input path="startDate" htmlEscape="true" placeholder="dd/mm/yyyy" class="form-control"/>
            <form:errors path="startDate" element="span"/>
          </div>
          <div class="col-xs-6">
            <label class="control-label" for="endDate">End date:</label>
            <form:input path="endDate" htmlEscape="true" placeholder="dd/mm/yyyy" class="form-control"/>
            <form:errors path="endDate" element="span"/>
          </div>
        </div>
        <hr>
        <button type="submit" class="btn btn-primary">Create a new project</button>
      </form:form>
    </div>
  </div>
</div>
    ]]></script>
  </div>
</div>

## Modelos y atributos - @ModelAttribute



## Elementos en sesión - @SessionAttributes



## Upload de archivos(MultipartResolver)


