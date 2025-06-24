# Práctica 4

## Vistas 
La ubicación de las vistas es importante desde el punto de vista de seguridad. Si son públicamente accesibles, se corre un riesgo de seguridad. Su acceso debería restringirse al uso interno de la aplicación. Por tanto, debe ubicarse bajo el directorio WEB-INF (consultar la especificación Servlet) o bien añadir restricciones de seguridad en el web.xml para evitar que se puedan acceder públicamente.

## Seguridad en URLs

Ejemplo de código para guardar artículo:

```java
articuloDAO.saveEntity(articuloModificado);
response.sendRedirect(request.getContextPath() + "/article?artId=" + URLEncoder.encode(codigo, "UTF-8") + "&saved=true");
```

Hay que tener cuidado con los parámetros que van en la URL y tratarlos como posibles entradas hostiles, ya que están sujetos a la manipulación. En este caso la variable `saved` se usa para mostrar el mensaje "Cambios realizados correctamente". Este comportamiento permite mostrar el mensaje o no manipulando la URL, algo que desde el punto de vista de seguridad no es aconsejable.

Ocurre algo similar con el siguiente código del controlador de catálogo:

```java
String pagina = request.getParameter("pagina");
// 1. Lectura y validación de la entrada
if(pagina==null || pagina.isBlank()){
    logger.info("clientId null or blank");
    response.sendRedirect("/backoffice/catalogue?pagina=1");
    return;
}
int actual=Integer.parseInt(pagina);
```

Para empezar, el parámetro debería haber sido "p" en vez de "pagina". En este caso, el problema está en el `parseInt`. Si el valor de "pagina" no es un entero se producirá un error 500 con el mensaje "java.lang.NumberFormatException".

## Patrón PRG

Hay entregas que no implementan el patrón Post/Redirect/Get (PRG). Cuando el usuario hace submit del formulario de ficha de artículo, (si se ha hecho correctamente) se envía una petición POST. Si no se implementa el patrón PRG y se recarga la página (F5), el navegador intenta reenviar los datos del formulario. 

En estos casos, tras enviar el formulario de la ficha de artículo mediante una petición POST, la aplicación responde directamente con una vista sin redirección. Esto puede provocar problemas como comportamientos inesperados o errores del lado del servidor. Además, el id del artículo se pierde.

Es importante aplicar el patrón PRG: después de procesar la petición POST que actualiza el artículo en BD , la aplicación debe realizar una redirección a una URL que cargue los datos mediante una petición GET. Esto evita la repetición de acciones si el usuario recarga la página y mejora la experiencia de uso.


## Guardar artículo cambiando la URL
En algunas entregas se ha observado que la URL al guardar artículo cambia. Es decir, se empieza navegando a "/backoffice/article?artId=Ede-338FO" para ver el detalle del artículo. En esta vista se muestra el formulario para modificar:

```html
<form class="formularioArticulo" action="fichaArticulo?artId=${articulo.codigo}" method="post">
```
Al hacer submit del formulario, se realizará una petición POST contra la URL "/backoffice/fichaArticulo?artId=Ede-338FO". Si bien el guardado en BD y el feedback que se da al usuario puede ser correcto, queda extraño en cuanto a la navegación. Lo que ocurre además en algunas prácticas es que no se implementa el patrón PRG, por lo que se producen efectos incorrectos. 

## Tests

En la práctica se indica que se deben escribir test para ArticuloDAO.findTiposArticulo y FabricanteDAO.findAll. Ciertamente, el estilo de redacción puede dar lugar a confusión por un abuso del lenguaje del día a día. 

Como aclaración, las interfaces no se testean, sino que se escriben tests para la implementaciones concretas. En este caso, el objetivo es testear los métodos públicos de la implementación correspondiente. Y de todos los métodos públicos aquellos que implementan el contrato expuesto por su interfaz.   


## Preguntas

### Cadena de conexión
Las cadenas de conexión a la BD que puede encontrarse en las indicaciones o en los scripts de creación de entidades y poblado de datos son así:

```
jdbc:mysql://localhost:3306/electrosa?serverTimezone=UTC
jdbc:mysql://localhost:3306/electrosa?serverTimezone=GMT%2B2
```
¿Son URL, URI, otra cosa? ¿Qué representa cada parte de la misma? 


