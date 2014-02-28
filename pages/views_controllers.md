# Vistas y controllers

------

## Declaración de controllers

* Proveen el acceso al comportamiento de la aplicación que definimos a través de interfaces de servicios
* Interpretan  las entradas del usuario y la transforman en un modelo que es representado para el usuario por la vista
* Spring implementa los controllers de una manera muy abstracta, la cual habilita una forma muy variada de declararlos
* Desde la versión 2.5 el modelo de programación está basado en anotaciones
* No necesitan extender clases base o implementar interfaces
* No tienen dependencias directas a la API de Servlet

### `@Controller`

* Indica que una clase en particular sirve del rol de controller
* Actúa como estereotipo para la clase anotada, se pudo haber usado `@Component`
* El dispatcher busca métodos y clases anotados con `@RequestMapping`
* Gracias a las firmas de los métodos variados en un controller se pueden formular muchos tipos de los mismos
* Debemos de habilitar la autodetección de dichas clases para agregarlas al AppCtx

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

<blockquote>
  <p>Los métodos de los controllers siempre regresarán de form explícita o implícita un <code>ModelAndView</code></p>
</blockquote>

## Uso de @RequestMapping



## URI Templates
