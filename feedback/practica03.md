# Práctica 3

## Error de Jasper en JSP

La primera pregunta cuando tenemos un error 500 en un JSP es si entendemos qué es Jasper y lo que significa un error de compilación como el siguiente:

```
org.apache.jasper.JasperException: No se puede compilar la clase para JSP: 

Ha tenido lugar un error en la línea: [20] en el archivo jsp: [/catalogo.jsp]
CatalogoDaoSQL cannot be resolved to a type
17:     <body>
18:         <h1>Catálogo de porductos</h1>
19:         <%
20:             CatalogoDaoSQL dao= new CatalogoDaoSQL();
21:             List<Articulo> articulos= dao.getTodosLosArticulos();
22:         %>
23:         <p> Se han encontrado <%= articulos.size() %> articulos</p>
```

## Foto en catálogo
Dado el siguiente código:

```html
 <img src="../assets/images/<%= articulo.getFoto() %>" alt="<%= articulo.getNombre() %>" style="max-width: 100px;">
```

Al acceder a "http://localhost:8080/backoffice/catalogo.jsp", la pregunta a realizarse es cuál será la URL resultante de la imagen. El resultado en este caso es que no se verá la imagen ya que la URL es "http://localhost:8080/assets/images/Placa/placa151.jpg".

## Atributo target de enlaces
En la página de `catalogo.jsp` se incluyen enlaces al artículo. Dado el siguiente enlace:

```html
<a target="blank_" href
```
Si se quería que el enlace se abriese en otra pestaña cada vez, el valor correcto es "_blank". Como el valor es "blank_" se interpreta como el destino de un frame o pestaña donde queremos que se abra el enlace. Al no encontrarlo, el navegador abre una nueva pestaña (o ventana en navegadores antiguos) y reutilizará esa pestaña para los siguientes enlaces.

De todas formas, si la navegación es interna dentro del sitio web, la recomendación general sería no utilizarlo ya que en términos de accesibilidad y usabilidad no se considera una buena práctica. No obstante, este aspecto no se ha valorado negativamente ya que no es un criterio de evaluación.   

## Tests

Los tests son para, como su nombre indica, testear el proyecto. No se trata de tener una clase `CatalogoDaoMySQLTest` con un método `main`. Con JUnit, basta con anotar los métodos del test con  `@Test` y para ejecutarlos se invoca a `mvn test` o "Test project" en NetBeans.

## Información de la aserción

Dada la siguiente aserción:

```java
assertTrue(result.size() == 4);
```

Dará la siguiente información: 

Sin embargo, si se cambia el assert por: org.opentest4j.AssertionFailedError: expected: <true> but was: <false>

```java
assertEquals(4, result.size());
```
Cuando el test falle, dará más información: org.opentest4j.AssertionFailedError: expected: <4> but was: <0>

## fail en JUnit
La siguiente instrucción en JUnit hace que el test falle de forma deliberada. 

```java
  fail("The test case is a prototype.");
```

En NetBeans al generar un test automáticamente se produce este código pero es labor del desarrollador (o tester) reemplazarlo por el código adecuado, incluyendo los `assert` pertinentes.



## Fragilidad de los tests
Por otra parte, hay que tener cuidado con las aserciones en los tests para que no sean frágiles. Por ejemplo:

```java
@Test
public void testFindAllReturns() throws ExcepcionDeAplicacion {
    assertEquals(447, catalog.findAll().size(), "El tamaño de la lista devuelta por findAll debería ser " + 447);
}
```

Está fijando que la BD siempre debe tener 447 elementos. Lo ideal sería preparar un entorno limpio de pruebas para procurrar el aislamiento de pruebas unitarias. Las pruebas deben ser independientes y reproducibles. 

Esta cuestión no ha sido valorada pero es muy importante tenerlo en cuenta para proyectos futuros.

## Preguntas

### jsp-config
¿Qué efectos produce la siguiente configuración de web.xml?

```xml
<jsp-config>
    <jsp-property-group>
        <url-pattern>*.jsp</url-pattern>
        <url-pattern>*.html</url-pattern>
        <page-encoding>UTF-8</page-encoding>
    </jsp-property-group>
</jsp-config>
```

Si una página HTML contiene scriptlets, ¿será interpretada como una página JSP?