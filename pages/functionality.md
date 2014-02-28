# Funcionalidad a desarrollar

------

Nuestro ejemplo estará basado en un tablero de tareas(Taskboard), el cual esta asignado a algun proyecto que a su vez tiene varias historias de usuario, dichas historias serán pobladas por las tareas. Todo este conjunto nos dará como resultado un tablero que potencialmente podrá ser visualizado en un front-end.

<div class="row">
  <div class="col-md-4">
    <div class="panel panel-primary">
      <div class="panel-heading">
        <h3 class="panel-title">Creación de proyectos</h3>
      </div>
      <div class="panel-body">
        <b>Como</b> product owner<br/>
        <b>Deseo</b> administrar proyectos<br/>
        <b>De tal manera que</b> pueda crearlos y visualizarlos
        <hr>
        Criterios de aceptación:
        <ul>
          <li>El proyecto debe tener un identificador único</li>
          <li>El identificador de proyecto de estar en mayúsculas y sin espacios</li>
          <li>Debe de tener una descripción</li>
          <li>Esta formado de varias historias de usuario</li>
          <li>Se deberá calcular el esfuerzo total del proyecto</li>
        </ul>
      </div>
    </div>
  </div>
  <div class="col-md-4">
    <div class="panel panel-primary">
      <div class="panel-heading">
        <h3 class="panel-title">Agregar historias de usuario</h3>
      </div>
      <div class="panel-body">
        <b>Como</b> product owner<br/>
        <b>Deseo</b> agregar la descripción de una funcionalidad<br/>
        <b>De tal manera que</b> pueda identificarla como una historia de usuario
        <hr>
        Criterios de aceptación:
        <ul>
          <li>Debe de tener el esfuerzo necesario en puntos</li>
          <li>Debe tener una prioridad</li>
          <li>Dos historias de usuario no pueden tener la misma prioridad</li>
          <li>Debe tener una descripción</li>
          <li>Es posible asignarle varias tareas</li>
          <li>Cuando todas sus tareas están terminadas entonces se considera hecho</li>
          <li>Se puede repriorizar una historia de usuario.</li>
        </ul>
      </div>
    </div>
  </div>
  <div class="col-md-4">
    <div class="panel panel-primary">
      <div class="panel-heading">
        <h3 class="panel-title">Crear tareas</h3>
      </div>
      <div class="panel-body">
        <b>Como</b> miembro del equipo<br/>
        <b>Deseo</b> agregar tareas<br/>
        <b>De tal manera que</b> puedan ser parte de una historia de usuario
        <hr>
        Criterios de aceptación:
        <ul>
          <li>Las tareas pueden tener tres estados: TODO, WIP y DONE</li>
          <li>Una tarea puede estar asignada a varios usuarios</li>
          <li>Tienen una descripción</li>
          <li>Pueden cambiar de estado</li>
          <li>Un usuario sólo puede tener una tarea en WIP</li>
          <li>Cuando se crea una tarea debe de tener el estado TODO.</li>
          <li>Sólo se pueden asignar tareas a usuarios dentro del proyecto.</li>
        </ul>
      </div>
    </div>
  </div>
</div>

<div class="row">
  <div class="col-md-4">
    <div class="panel panel-primary">
      <div class="panel-heading">
        <h3 class="panel-title">Agregar miembros al equipo</h3>
      </div>
      <div class="panel-body">
        <b>Como</b> miembro del equipo<br/>
        <b>Deseo</b> unirme al equipo<br/>
        <b>De tal manera que</b> agregar tareas y colaborar en un proyecto
        <hr>
        Criterios de aceptación:
        <ul>
          <li>Los nombres de usuario deben ser únicos</li>
          <li>El nombre de usuario debe tener la forma de un correo</li>
        </ul>
      </div>
    </div>
  </div>
  <div class="col-md-4">
    <div class="panel panel-primary">
      <div class="panel-heading">
        <h3 class="panel-title">Visualizar un tablero de tareas</h3>
      </div>
      <div class="panel-body">
        <b>Como</b> miembro del equipo<br/>
        <b>Deseo</b> un tablero con las tareas de una proyecto<br/>
        <b>De tal manera que</b> pueda visualizar el estado actual del proyecto
        <hr>
        Criterios de aceptación:
        <ul>
          <li>Debe de tener 3 líneas</li>
          <li>Las tareas deben mostrar el nombre de los participantes</li>
        </ul>
      </div>
    </div>
  </div>
  <div class="col-md-4">
    <div class="panel panel-primary">
      <div class="panel-heading">
        <h3 class="panel-title">Tu propia historia...</h3>
      </div>
      <div class="panel-body">
        <b>Como</b> un usuario<br/>
        <b>Deseo</b> hacer algo<br/>
        <b>De tal manera que</b> me aporte valor
        <hr>
        Criterios de aceptación:
        <ul>
          <li>Satisfacción</li>
          <li>Validación</li>
          <li>Restricción</li>
        </ul>
      </div>
    </div>
  </div>
</div>

------

## Estructura de las clases de dominio

<div class="row">
  <div class="col-md-4">
    <h4><i class="icon-file"></i> Project.java</h4>
    <script type="syntaxhighlighter" class="brush: java"><![CDATA[
package com.makingdevs.model;

import java.util.Date;
import java.util.List;

public class Project {
  private Long id;
  private String name;
  private String codeName;
  private String description;
  private Date dateCreated;
  private Date lastUpdated;
  
  private List<UserStory> userStories;
  private List<User> participants;
  
  // Getters y Setters
  // Constructores
}
    ]]></script>
  </div>
  <div class="col-md-4">
    <h4><i class="icon-file"></i> UserStory.java</h4>
    <script type="syntaxhighlighter" class="brush: java"><![CDATA[
package com.makingdevs.model;

import java.util.Date;
import java.util.List;

public class UserStory {
  private Long id;
  private String description;
  private Integer priority;
  private Integer effort;
  private Date dateCreated;
  private Date lastUpdated;
  
  private Project project;
  private List<Task> tasks;
  // Getters y Setters
  // Constructores
}
    ]]></script>
  </div>
  <div class="col-md-4">
    <h4><i class="icon-file"></i> Task.java</h4>
    <script type="syntaxhighlighter" class="brush: java"><![CDATA[
package com.makingdevs.model;

import java.util.Date;
import java.util.List;

public class Task {
  private Long id;
  private String description;
  private TaskStatus status;
  private Date dateCreated;
  private Date lastUpdated;
  
  private UserStory userStory;
  private List<User> participants;
  // Getters y Setters
  // Constructores
}
    ]]></script>
  </div>
</div>

<div class="row">
  <div class="col-md-6">
    <h4><i class="icon-file"></i> TaskStatus.java</h4>
    <script type="syntaxhighlighter" class="brush: java"><![CDATA[
package com.makingdevs.model;

public enum TaskStatus {
  TODO,WIP,DONE;
}
    ]]></script>
  </div>
  <div class="col-md-6">
    <h4><i class="icon-file"></i> User.java</h4>
    <script type="syntaxhighlighter" class="brush: java"><![CDATA[
package com.makingdevs.model;

import java.util.Date;

public class User {
  private Long id;
  private String username;
  private boolean enabled;
  private Date dateCreated;
  private Date lastUpdated;
  // Getters y Setters
  // Constructores
}
    ]]></script>
  </div>
</div>

------

## Funcionalidad que deseamos implementar a nivel de interfaces

<div class="row">
  <div class="col-md-6">
    <h4><i class="icon-file"></i> ProjectService.java</h4>
    <script type="syntaxhighlighter" class="brush: java"><![CDATA[
package com.makingdevs.model;

public interface ProjectService {
  void createNewProject(Project project);
  Project findProjectByCodeName(String codeName);
  Integer totalEffortForProject(String codeName);
}
    ]]></script>
  </div>
  <div class="col-md-6">
    <h4><i class="icon-file"></i> UserStoryService.java</h4>
    <script type="syntaxhighlighter" class="brush: java"><![CDATA[
package com.makingdevs.model;

import java.util.List;

public interface UserStoryService {
  void createUserStory(UserStory userStory);
  List<UserStory> findUserStoriesByProject(String codeName);
  boolean isUserStoryDone(Long userStoryId);
  UserStory findUserStoryByIdentifier(Long userStoryId);
}
    ]]></script>
  </div>
</div>

<div class="row">
  <div class="col-md-6">
    <h4><i class="icon-file"></i> TaskService.java</h4>
    <script type="syntaxhighlighter" class="brush: java"><![CDATA[
package com.makingdevs.model;

public interface TaskService {
  Task createTaskForUserStory(String taskDescription, Long userStoryId);
  void assignTaskToUser(Long taskId, String username);
  void changeTaskStatus(Long taskId, TaskStatus taskStatus);
}
    ]]></script>
  </div>
  <div class="col-md-6">
    <h4><i class="icon-file"></i> UserService.java</h4>
    <script type="syntaxhighlighter" class="brush: java"><![CDATA[
package com.makingdevs.model;

public interface UserService {
  User findUserByUsername(String username);
  User createUser(String username);
  void addToProject(String username, String codeName);
}
    ]]></script>
  </div>
</div>

<div class="bs-callout bs-callout-info">
<h4><i class="icon-coffee"></i> Información de utilidad</h4>
  <p>
    Aunque esta es la funcionalidad de negocio que deseamos implementar, debes recordar que aún necesitarás otros componentes que te permitan almacenar los datos de la estructura; tales componentes podrían implementarse con acceso a datos(relacionales o no relacionales) y sus respectivas abstracciones.
  </a>
  </p>
</div>