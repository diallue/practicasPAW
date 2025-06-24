# Maven

## Estructura de un proyecto maven

Un proyecto maven sigue una estructura de directorios para unificar la organización de carpetas de un proyecto a otro. Es una forma de homogeneizar e introducir cierta estandarización. 

![Proyecto maven](images/maven01.png)
 

El primer archivo a tener en cuenta es el "pom.xml". 

- `src/main/java`: Clases java del proyecto
- `src/main/resources`: Recursos de la aplicación. Por ejemplo, configuración de del sistema de logging (log4j, slf4j, etc)
- `src/main/webapp`: Fuentes de la aplicación web (páginas html, jsp, css, etc.)
- `src/test/java`: Clases para realizar tests
- `target`: Resultado de la construcción del proyecto

 Puedes encontrar más información sobre la estructura de proyectos maven en https://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html.

Ten en cuenta las siguientes indicaciones breves:

- Las clases de tests (src/test/java) no forman parte del war de la aplicación que se construirá normalmente. Nos servirán para incluir los tests unitarios.
- Dentro del archivo "pom.xml" se declaran las dependencias que necesita el proyecto. Es por ello que no encontraremos archivos .jar dentro de los fuentes de  nuestra aplicación. Es un aspecto importante. En un proyecto maven se declaran las dependencias; no se incluyen las rutas de los ficheros .jar de forma imperativa.
- El directorio "target" contiene el resultado de la compilación. Aquí sí que encontraremos archivos .jar porque es el proyecto listo para ejecutar o desplegar en Tomcat. Este directorio es generado cada vez que hacemos un "build" y no debería incluirse dentro del sistema de control de versiones.

