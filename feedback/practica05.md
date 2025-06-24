# Práctica 5

## Protección de rutas

Dado el siguiente escenario:

Controlador:

```java
@WebServlet("/catalogo")
public class CatalogueController extends HttpServlet {

  protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws IOException, ServletException {

    req.getRequestDispatcher(
        "/protected/articles-list.jsp")
        .forward(req, resp);

```

Filtro:

```java
@WebFilter("/protected/*")
public class AuthFilter implements Filter {
```

Este filtro está protegiendo las vistas pero no está protegiendo las rutas correspondientes de los servlets. Por tanto, si navegamos a la URL "http://localhost:8080/backoffice/catalogo", la aplicación mostrará el contenido de los artículos sin pasar por la autenticación. Los filtros anotados con `@WebFilter` admiten más atributos. Uno de ellos es `dispatcherTypes` que permitiría definir que el filtro actúe para peticiones FORWARD, INCLUDE, REQUEST entre otras. Puedes consultar [la especificación de Servlet](https://jakarta.ee/specifications/servlet/6.0/apidocs/jakarta.servlet/jakarta/servlet/annotation/webfilter) cuál es el valor por defecto.

## Magical strings
Encontramos varios casos en los que se escriben constantes dentro del código: 

```java
String rol = usuarioDAO.obtenerRolUsuario(username);
if ("administrador".equals(rol)) {
```

La constante "administrador" es una cadena usada directamente en código sin más contexto o un sentido explícito. Si esta constante es usada en varias partes de nuestra base de código, tendremos un problema de mantenibilidad. Hay varias alternativas que mejoran este código, como refactorizar para sacar el valor a una enumeración, clase de constantes, etc. 

En esta línea, también cabe plantearse cuántas consultas a BD hay que realizar para validar que el usuario y contraseña cifrada se corresponden con alguna tupla en BD, y que el usuario correspondiente tiene otorgado el rol "administrador". ¿Podrías hacerlo en una única consulta con un `join`?

A efectos de evaluación, este criterio no se ha tenido en cuenta.

## Usuarios Vs Roles
El siguiente código se encuentra en `LoginController`:

```java
if (!username.equals("admin")) {
    request.setAttribute("error", "Usuario no tiene rol de administrador");
    request.getRequestDispatcher("/WEB-INF/views/login.jsp").forward(request, response);
    return;
}
```

El acceso de administrador a la aplicación no se debería restringir por usuario sino por rol. En la BD podría haber otros usuarios con el rol "administrador".

## Rutas estáticas
La soluciones propuestas para omitir del filtro los recursos estáticos han sido múltiples y variadas. Una de ellas plantea algo similar a lo siguiente:

```java
// Rutas que no requieren autenticación
boolean isLogin = path.equals(contextPath + "/auth/login");
boolean isLogout = path.equals(contextPath + "/auth/logout");
boolean isStaticResource = path.startsWith(contextPath + "/css/")
        || path.startsWith(contextPath + "/js/")
        || path.startsWith(contextPath + "/images/")
        || path.endsWith(".css")
        || path.endsWith(".js")
        || path.endsWith(".png")
        || path.endsWith(".jpg")
        || path.endsWith(".woff2")
        || path.endsWith(".ico")
        || path.endsWith(".ttf");

if (loggedIn || isLogin || isLogout || isStaticResource) {
    chain.doFilter(req, res); // El usuario puede continuar
} else {
    // Guardar URL original para redirigir luego
    HttpSession newSession = request.getSession(true);
    newSession.setAttribute("redirectUrl", path);

    // Redirigir a login
    response.sendRedirect(contextPath + "/auth/login");
}        
```
Esta aproximación adolece de un problema en combinación con el diseño de la vista. Y es que la vista usa imágenes vectoriales (svg), por lo que en el atributo `redirectURL` de sesión se termina guardando la URL de un recurso svg. Consecuentemente, al hacer login con éxito, la aplicación termina redirigiendo a la URL de esta imagen en vez de al catálogo de artículos u otro apartado de la aplicación.

Otras opciones planteadas que filtran los recursos estáticos en base a la ruta "assets" en principio son más robustas ya que no son sensibles a los tipos de archivos; dan por supuesto que todo recurso cuya ruta esté bajo "assets" es un estático que no tiene que pasar por el filtro de usuario autenticado.

## Mensajes de error de login

Los mensajes cuando el login no es correcto admiten multitud de opciones. No obstante, hay que tener los posibles problemas de fuga de información de forma no consciente. En algunas prácticas, si el usuario que se autentica es cliente en vez de administrador, el mensaje que se muestra es similar a "El usuario no es un administrador". 

Esta implementación revela información sobre el rol del usuario, lo que puede ayudar a un atacante a tener más información. 

Como recomendación general, el consejo desde un punto de vista de seguridad es no revelar más información de la necesaria.  


## Filtro y welcome-file

Cuando se está desarrollando el filtro de autenticación hay que tener cuidado con los patrones de URL que se van a filtrar o los que se van a excluir.

Si tenemos el siguiente `welcome-file-list` en web.xml:

```xml
<welcome-file-list>
    <welcome-file>catalogo.jsp</welcome-file>
    <welcome-file>almacenes.jsp</welcome-file>
</welcome-file-list>
```

Y el código del filtro similar al siguiente:

```java
@WebFilter(urlPatterns = {
    "/cliente", "/clientes", 
    "/article", 
    "/image", "/catalogue", 
    "/pedido"}) 
public class Filter implements jakarta.servlet.Filter {
```

¿Qué vista se verá al navegar al inicio de la aplicación "http://localhost:8080/backoffice/"? ¿Esta URL estará restringida por el filtro?

Reflexiona sobre ello. La respuesta admite matices pero como recomendación de seguridad en esta práctica, es mejor denegar todo por defecto y permitir ciertas URL (login, logout y recursos estáticos).