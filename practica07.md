# Práctica 7

## Internacionalización
En Thymeleaf existen algunos objetos disponibles que podemos usar en expresiones para una mayor flexibilidad. Uno de ellos nos permite obtener información sobre la *locale* actual. Por ejemplo:

```html
Locale actual: <span th:text="${#locale.country}">ES</span>.
```

Tomando como base este objeto, la vista para seleccionar el idioma puede hacer uso de las siguientes expresiones:

```html
<div>
    <label>[[ #{lang.settings.current} ]]:</label>
    [[ ${#locale.displayLanguage} ]] ([[ ${#locale} ]])
</div>

<section>    
    <form>
       
        <select name="lang">
            <option th:each="langOption : ${languageCollection}"
                    th:value="${langOption}"
                    th:selected="${#locale.toString()} == ${langOption}">
                [[ #{'lang.' + ${langOption}} ]]
            </option>
        </select> 
    
    </form>
</section>
```

Si no le hemos dado valor a la variable `languageCollection` en el contralador para tener una colección de los idiomas disponibles, entonces habrá que construir el `select` manualmente. En cualquier caso, habrá que dejar seleccionado el valor dado por la *locale* actual:

```html
<select name="lang" id="language-selector">
    <option value="es" th:text="#{lang.es}" th:selected="${#strings.equals(#locale.toString(), 'es')}"></option>
    <option value="en" th:text="#{lang.en}" th:selected="${#strings.equals(#locale.toString(), 'en')}"></option>
    <option value="fr" th:text="#{lang.fr}" th:selected="${#strings.equals(#locale.toString(), 'fr')}"></option>
    <option value="de" th:text="#{lang.de}" th:selected="${#strings.equals(#locale.toString(), 'de')}"></option>
</select>
```

## Filtros en JPA

Al usar repositorios que extienden `JpaRepository` podemos seguir aprovechando las ventajas que aporta Spring. Esta interfaz hereda a su vez de las siguientes interfaces:

```java
public interface JpaRepository<T,ID> extends ListCrudRepository<T,ID>, ListPagingAndSortingRepository<T,ID>, QueryByExampleExecutor<T>
```

Podemos emplear un método que viene dado por extender de `QueryByExampleExecutor`:

```java
<S extends T> Page<S> findAll(Example<S> example, Pageable pageable)
```

Se parece al que ya vimos que tenía la forma `Page<T> findAll(Pageable pageable)`. En este caso, añade un parámetro para realizar una búsqueda por ejemplo o QBE (*Query by example*). Con ello, el código del controlador podría quedar más sencillo:

```java
import org.springframework.data.domain.Example;

// crear entidad con los valores por los que queremos filtrar
ArticuloEntity e = new ArticuloEntity();
e.set...
Page<ArticuloEntity> page = articuloRepository.findAll(Example.of(e), pageable);
```

Esta vía no es la única para resolver el filtro de artículos de forma paginada. Depende de cada estudiante encontrar la solución que mejor entiende y sepa aplicar de forma autónoma.


## Modificación de la base de datos

En los scripts de alteración del esquema de la BD no se ha alterado la longitud del varchar de la clave ajena correspondiente al código de cliente. Para corregirlo es necesario alterar las tablas de **pedido** y **pedidoanulado**. Una forma rápida de conseguirlo es a través de MySQL Workbench.

![Alter table](images/mysql-workbench01.png)

Asegúrate de que la columna CODIGOCLIENTE queda con una longitud de 40.

![CODIGOCLIENTE](images/mysql-workbench02.png)

Una vez cambiado pulsa en **Apply** para aplicar los cambios.

Repite este proceso para la tabla **pedidoanulado**.

