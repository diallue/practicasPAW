# Spring MVC

## Anotaciones para controladores

Spring MVC utiliza la anotación `@RequestMapping` para mapear peticiones a los métodos de nuestros controladores. Además de esa anotación, tenemos otras que son atajos, entre las cuales encontramos:

- `@GetMapping` (equivalente a  `@RequestMapping(method = RequestMethod.GET`)
- `@PostMapping` (equivalente a ` @RequestMapping(method = RequestMethod.POST)`)


Si tenemos el siguiente controlador:

```java
@Controller
@RequestMapping("/catalogo")
public class CatalogoController {

    @GetMapping(value = {"/todos"})
    public String todosLosArticulos() { 
        return "catalog/product-list";
    }
```

Para conseguir que se ejecute el método `todosLosArticulos`, la URL que tendríamos que escribir sería "/catalogo/todos" porque hay que tener en cuenta el *mapping* a nivel de clase y el *mapping* a nivel de método.

Como se ve, el mapeo de peticiones a controladores es independiente de la resolución de vistas.

Más información en [https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-requestmapping.html](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-requestmapping.html)




## Resolución de vistas


Spring MVC tiene mucha versatilidad a la hora de resolver la vista. La primera alternativa se basa en hacer que los métodos de nuestros controladores devuelvan un `String`:

```java
@Controller
@RequestMapping("/catalogo")
public class CatalogoController {
    @GetMapping(value = {"", "/"})
    public String index() { 
        return "catalog/product-list";
    }
```

Según tenemos configurado el proyecto la vista, "catalog/product-list" se corresponde con una vista de Thymeleaf. O dicho de otro modo, se corresponde con un **archivo interno** de nuestro proyecto. El usuario nunca verá la ruta que indiquemos. De forma parecida al proyecto con el patrón MVC que sólo usaba Servlets y JSPs, el controlador termina haciendo un *forward* a la vista (no una redirección).

En el ejemplo, la vista es "catalog/product-list". ¿A qué archivo se corresponde en nuestro proyecto? Para responder a la pregunta hay que ver la configuración de Spring en `SpringWebConfig`:

```java
templateResolver.setPrefix("/WEB-INF/views/thymeleaf/");
templateResolver.setSuffix(".html");
```

Estas dos líneas hacen que el nombre de la vista se concatene con ese prefijo y sufijo, dando como resultado:

```
/WEB-INF/views/thymeleaf/catalog/product-list.html
```
Esta ruta es la ubicación física de la vista dentro de nuestro proyecto. 

Como característica particular, Spring MVC contempla casos especiales para intentar hacer el desarrollo más ágil. Entre ellos, tenemos que si el nombre de la vista incluye "redirect:", la resolución de la vista entenderá que se trata de una redirección y por tanto el resto del `String` se entenderá como una URL.

En [https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/viewresolver.html#mvc-redirecting-redirect-prefix](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/viewresolver.html#mvc-redirecting-redirect-prefix) puedes encontrar más información sobre este aspecto.




## Paginación con Spring MVC

Al usar repositorios que extienden `JpaRepository` podemos aprovechar las ventajas que aporta en Spring, en concreto a nivel de paginación. Esta interfaz a su vez extiende `PagingAndSortingRepository` que es la que brinda estas capacidades de paginación. Entre ellas se dispone del método [`findAll`](https://docs.spring.io/spring-data/data-commons/docs/current/api/org/springframework/data/repository/PagingAndSortingRepository.html#findAll(org.springframework.data.domain.Pageable)) que tiene la siguiene forma:

```java
Page<T> findAll(Pageable pageable)
```
Como ejemplo concreto, se puede usar este método para recuperar la lista de artículos paginada:

```java
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
// resto de imports

@Controller
@RequestMapping("/catalogo")
public class CatalogoController {


int parsePageNumber(HttpServletRequest request) {
    String pageNumberAsString = request.getParameter("p");
    if (pageNumberAsString == null || pageNumberAsString.isBlank()) {
        // no llega el "p" en querystring
        return 1;
    }
    int result;
    try {
        result = Integer.parseInt(pageNumberAsString);
    } catch (NumberFormatException e) {
        // no se puede convertir a Int
        return 1;
    }
    return result;
}

@GetMapping(value = {"", "/"})
public String index(ModelMap model, HttpServletRequest request) {
    int pageNumber = parsePageNumber(request);
    Pageable pageable = PageRequest.of(pageNumber - 1, this.pageSize);
    logger.info("Pageable: {} , {}", pageable.getPageNumber(), pageable.getPageSize());
    Page<ArticuloEntity> page = articuloRepository.findAll(pageable);
    List<ArticuloEntity> articulos = page.getContent();
    model.addAttribute("articulos", articulos);
    model.addAttribute("pageInfo", page);
    return "catalogo/lista-articulos";
}
```

Y la parte de la vista "catalogo/lista-articulos" que muestra el paginador:

```html
<div class="paginator-wrapper">
    <a th:if="${!pageInfo.isFirst()}" 
        th:href="@{/catalogo/}">
        <i class="lni lni-shift-left"></i> Primero
    </a>
    <a th:if="${pageInfo.hasPrevious()}" 
        th:href="@{|/catalogo/?p=${pageInfo.previousPageable().getPageNumber()+1}|}">
        Anterior
    </a>    

    <span class="current">
        Mostrando página [[${pageInfo.number+1}]] de [[${pageInfo.totalPages}]] 
    </span>

    <a th:if="${pageInfo.hasNext()}" 
        th:href="@{|/catalogo/?p=${pageInfo.nextPageable().getPageNumber()+1}|}">
        Siguiente
    </a>    

    <a th:if="${!pageInfo.isLast()}" 
        th:href="@{|/catalogo/?p=${pageInfo.totalPages}|}">
            Último <i class="lni lni-shift-right"></i>
    </a>
</div>

<div class="pagination-info">
    <label>
        Artículos por página:
    </label>
    <span class="pagesize">[[${pageInfo.size} ]]</span>
    <span class="records-found">
        (Total: [[ ${pageInfo.totalElements} ]])
    </span>
</div>
```

Conviene repasar este código y hacer los ajustes correspondientes. No obstante, esta alternativa a construir la paginación manualmente puede resultar más fácil y directa para ahorrar tiempo de desarrollo.

# Actualización de pedidos

Las operaciones de nuestros controladores devuelven un `String` con el nombre de una vista lógica de Thymeleaf.

Para conseguir devolver un JSON en vez de una vista que eventualmente se transforme en el HTML de respuesta de la petición HTTP, tenemos varias alternativas: construir manualmente un JSON como un `String`, usar librerías de terceros ([Jackson](https://github.com/FasterXML/jackson), [GSON](https://github.com/google/gson), etc) o utilizar las propias funcionalidades del framework Spring.

Por sencillez y eficacia, usaremos esta última alternativa. Para que la respuesta de una operación de un controlador Spring sea un JSON en vez de una vista lógica de Thymeleaf basta con añadir la anotación `@ResponseBody` a nuestro método. Es decir, en el ejemplo siguiente conseguimos que la respuesta del método `schedule` sea la representación JSON de una entidad `PedidoEntity`

```java
@PostMapping(".....")
@ResponseBody
public PedidoEntity schedule() {
    PedidoEntity e = .....
    return e;
}
```

De forma análoga, también tenemos la anotación `@RequestBody` para admitir un JSON en la petición HTTP. Siguiendo con el mismo ejemplo, podemos añadir un parámetro al método para añadir el cuerpo de la petición:

```java
@PostMapping(".....")
@ResponseBody
public PedidoEntity schedule(
    @RequestBody ScheduleOrderRequest payload
) {
    payload
    PedidoEntity e = .....
    return e;
}
```

En este caso, esta clase `ScheduleOrderRequest` podría ser así una clase Java simple como la siguiente:

```java
public class ScheduleOrderRequest {

    private Integer ndays;

    public Integer getNdays() {
        return ndays;
    }

    public void setNdays(Integer ndays) {
        this.ndays = ndays;
    }

}
```

Tienes más información sobre esta anotación en [la documentación de Spring](https://docs.spring.io/spring-framework/reference/web/webflux/controller/ann-methods/requestbody.html).

Por último, la anotación `@PathVariable` permite definir variables dentro de la URL. Esto es muy útil cuando estamos construyendo URLs para servicios REST. Supongamos que queremos aceptar peticiones del tipo "/blog/p/2024-23-12" donde el último segmento es variable. Podemos plantear un controlador Spring como el siguiente:

```java
@Controller
@RequestMapping("/blog")
public class BlogController {

    @GetMapping("/p/{slug}")
    public String getBlogEntry(@PathVariable String slug) {
        logger.info("Slug={}", slug);
       
    }
```

En este caso, la variable `slug` contendrá ese valor "2024-23-12" del segmento de la URL.