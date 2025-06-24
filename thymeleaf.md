# Thymeleaf


## Devolver una vista Thymeleaf

Hay múltiples formas de indicarle a Spring qué vista renderizar. La más simple es devolver el nombre de la vista como un `String`:

```java
@Controller
@RequestMapping("/welcome")
public class IndexController {

    @GetMapping
    public String saludo(Model model) 
    {
        model.addAttribute("message", "Hola mundo");
        return "bienvenida";
    }
}
```

El `String` que se devuelve se corresponde con un fichero que contiene la vista. Así en el proyecto tendríamos un archivo "bienvenida.html":

```html
<!DOCTYPE HTML>
<html lang="es" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="utf-8">
</head>
<body>
    <h1>
        <span th:text="${message}"></span>
    </h1>
</body>
</html>
```

Según está configurada la aplicación no hace falta indicar ni la ruta de carpetas donde está este fichero, ni la extensión. Es algo que en nuestro proyecto ya tenemos configurado en el bean `SpringResourceTemplateResolver`.




## Fragmentos en Thymeleaf


En ocasiones, además de tener un *layout* Thymeleaf para tener un esqueleto o plantilla base de las páginas HTML, resulta conveniente poder reutilizar ciertos bloques HTML. Como ejemplo, se podría tener un menú de área del cliente que se reutiliza en todas las páginas del área privada del cliente.

Los fragmentos de Thymeleaf permiten definir bloques de código para poder reutilizarlos posteriormente. Normalmente, se definen en ficheros separados, aunque también se pueden agrupar varios dentro del mismo fichero. Para definir un fragmento se utiliza la siguiente sintaxis:

```html
th:fragment="un_nombre_cualquiera"
```
Para usarlos, en principio se tienen tres alternativas:

- *include*
- *insert*
- *replace*

Como ejemplo práctico, supongamos que queremos añadir un menú del área de cliente. Primero, definimos el fragmento en un archivo separado (`account_sidebar.html`) en la ruta "WEB-INF/views/thymeleaf/fragments":

```html
<!DOCTYPE html>
<html>
    <head>
    </head>
    <body>
        <div th:fragment="sidebar" 
            class="sidebar  has-scrollbar sidebar-user-account" data-mobile-menu>

            <div class="product-showcase">

                <div class="showcase-wrapper" style="margin-top: 80px;">

                    <div class="showcase-container">

                        <div class="showcase">

                            <a th:href="@{/client/purchases}" class="showcase-img-box">
                                <ion-icon name="cube" style="color: var(--sandy-brown);"></ion-icon>
                            </a>

                            <div class="showcase-content">
                                <a th:href="@{/client/purchases}">
                                    <h4 class="showcase-title">Compras realizadas</h4>
                                </a>
                            </div>

                        </div>

                        <div class="showcase">

                            <a th:href="@{/user/profile}" class="showcase-img-box">
                                <ion-icon name="person" style="color: var(--ocean-green);"></ion-icon>
                            </a>

                            <div class="showcase-content">

                                <a th:href="@{/user/profile}">
                                    <h4 class="showcase-title">Datos personales</h4>
                                </a>

                            </div>
                        </div>

                    </div>

                </div>

            </div>

        </div>

    </body>
</html>
```

Del código anterior, la parte relevante es cómo le damos nombre al fragmento (`th:fragment="sidebar"`).

Ahora, para poder incluir este fragmento en las vistas del área de cliente, escribiremos:

```html
  <div th:replace="fragments/account_sidebar :: sidebar"></div>
```  

Como hemos colocado el fragmento dentro de la carpeta "fragments" (podría haber sido cualquiera), es necesario incluirla para que Thymeleaf la pueda localizar. Además, según la configuración del proyecto tenemos que:

```java
@Bean
public SpringResourceTemplateResolver templateResolver() {
    SpringResourceTemplateResolver templateResolver = new SpringResourceTemplateResolver();
    templateResolver.setApplicationContext(this.applicationContext);

    templateResolver.setPrefix("/WEB-INF/views/thymeleaf/"); 
    templateResolver.setSuffix(".html"); 
```

Con lo que la ruta efectiva del fragmento es: "/WEB-INF/views/thymeleaf/fragments/account_sidebar.html". Dentro de este fichero podría haber varios fragmentos definidos por lo que es necesario indicar su nombre, en nuestro caso *`sidebar`*.

Con todo, habremos conseguido refactorizar una parte de código HTML reutilizable en varias vistas.

En cuanto a las opciones para incrustar el fragmento, `th:include` es obsoleto aunque se mantiene de forma legada para permitir la compatibilidad. Sobre la diferencia entre `th:insert` and `th:replace` puedes encontrar más información en el tutorial *Using Thymeleaf* disponible en el aula virtual y en [https://www.thymeleaf.org/documentation.html](https://www.thymeleaf.org/documentation.html).

