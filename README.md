
![enter image description here](https://sg.fiverrcdn.com/photos/103633736/original/6d3ecda29129fa02df17bdd5e8a8cfe79fa13549.png?1509169610)  
# This is Simple Groovy Grails MVC Web App  
  
> When ı write that code, use many web sites and examples from these web sites. This   ReadMe file is about project, process etc. All notes are for my  
> personel usage to source future groovy grails development.  
  
I use [bealdung tutorial](https://www.baeldung.com/grails-mvc-application) to create a groovy grail web app skeleton. 

For this app ı use `intellij` ide and `Aplication Forge`.
I use [Grail Application Forge Page](https://start.grails.org/) for start my project.

##  Anatomy of a Grails App

In this section, we will get a better understanding of the Grails application structure. Grails prefers convention over configuration, therefore the location of files defines their purpose. Let’s see what we have in the  _grails-app_  directory:

-   **assets**  – a place where we store static assets files like styles, javascript files or images
-   **conf**  – contains project configuration files:
    -   _application.yml_  contains standard web app settings like data source, mime types, and other Grails or Spring related settings
    -   _resources.groovy_  contains spring bean definitions
    -   _logback.groovy_  contains logging configuration
-   **controllers**  – responsible for handling requests and generating responses or delegating them to the views. By convention, when a file name ends with  _*Controller_, the framework creates a default URL mapping for each action defined in the controller class
-   **domain** – contains the business model of the Grails application. Each class living here will be mapped to database tables by GORM
-   **i18n**  – used for internationalization support
-   **init**  – an entry point of the application
-   **services**  – the business logic of the application will live here. By convention, Grails will create a Spring singleton bean for each service
-   **taglib** – the place for custom tag libraries
-   **views**  – contains views and templates

## A Simple Web Application

> You can find details at [baeldung web site](https://www.baeldung.com/grails-mvc-application)

To create a ***grail web application***

> grails create-app

To create ***Domain Class***

> grails create-domain-class com.kadiremreozcan.grails.Student
```groovy
class Student {
	String firstName
	String lastName
}
```
To generate ***Service***
> grails create-service com.kadiremreozcan.grails.Student

``` groovy
@Transactional  
class StudentService { 
   
	 def get(id){ 
		 Student.get(id) 
	 }    
	  
	 def list() {  
		 Student.list() 
	 }  
	    
	 def save(student){  
		 student.save() 
	 }   
	   
	 def delete(id){  
		 Student.get(id).delete() 
	 }
	 
 }
```
To create ***Controller***

> grails create-controller com.kadirermeozcan.grails.Student

```groovy
class StudentController {  
   
	 def studentService   
	   
	 def index() {  
		 respond studentService.list() 
	 }    
	  
	 def show(Long id) {  
		 respond studentService.get(id) 
	 }   
	   
	 def create() {  
		 respond new Student(params) 
	 }  
	    
	 def save(Student student) {  
		 studentService.save(student) redirect action:"index", method:"GET" 
	 }  
	    
	 def delete(Long id) {  
		 studentService.delete(id) redirect action:"index", method:"GET" 
	 }
	 
 }
```
###  View Layer

Having set up our controller actions, we can now proceed to create the UI views. We will create three Groovy Server Pages for listing, creating and removing Students.

By convention, Grails will render a view based on controller name and action. For example, _**the index()**_ **action from  _StudentController_  will resolve to  _/grails-app/views/student/index.gsp_**

Let’s start with implementing the view  _/grails-app/__views/student/index.gsp_, which will display a list of students. We’ll use the tag  _<f:table/>_  to create an HTML table displaying all students returned from the  _index()_  action in our controller.

By convention, when we respond with a list of objects,  **Grails will add the “List” suffix to the model name**  so that we can access the list of student objects with the variable  _studentList_:

```html
<!DOCTYPE html> 
<html> 
	<head> 
		<meta name="layout" content="main" /> 
	</head> 
	<body> 
		<div class="nav" role="navigation"> 
		<ul> 
			<li>
				<g:link class="create" action="create">Create</g:link>
			</li> 
		</ul> 
		</div> 
		<div id="list-student" class="content scaffold-list" role="main"> 
			<f:table collection="${studentList}" properties="['firstName', 'lastName']" /> 
		</div> 
	</body> 
</html>
```

We’ll now proceed to the view _/grails-app/__views/__student/create.gsp,_ which allows the user to create new Students. We’ll use the built-in _<f:all/>_ tag, which displays a form for all properties of a given bean:

```html
<!DOCTYPE html>
<html> 
	<head> 
		<meta name="layout" content="main" /> 
	</head> 
	<body> 
		<div id="create-student" class="content scaffold-create" role="main"> 
			<g:form resource="${this.student}" method="POST"> 
				<fieldset class="form"> 
					<f:all bean="student"/> 
				</fieldset> 
				<fieldset class="buttons"> 
					<g:submitButton name="create" class="save" value="Create" /> 
				</fieldset> 
			</g:form> 
		</div> 
	</body> 
</html>
```
Finally, let’s create the view  _/grails-app/__views/__student/show.gsp_  for viewing and eventually deleting students.

Among other tags, we’ll take advantage of  _<f:display/>_, which takes a bean as an argument and displays all its fields:

```html
<!DOCTYPE html> 
<html> 
<head> 
<meta name="layout" content="main" /> 
</head> 
	<body> 
		<div class="nav" role="navigation"> 
			<ul> 
				<li>
					<g:link class="list" action="index">Students list</g:link>
				</li> 
			</ul> 
		</div> 
		<div id="show-student" class="content scaffold-show" role="main"> 
			<f:display bean="student" /> 
			<g:form resource="${this.student}" method="DELETE"> 
				<fieldset class="buttons"> 
					<input class="delete" type="submit" value="delete" /> 
				</fieldset> 
			</g:form> 
		</div> 
	</body>
</html>
```