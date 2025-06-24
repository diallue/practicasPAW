# Práctica 9

## Credenciales en github

Se han detectado varios casos en los que el archivo `mail.properties`. Comprueba por favor que no has entregado en ninguna práctica este fichero con las credenciales de tu CUASI. **Si es tu caso, deja de leer y corre a cambiar tu contraseña.**

Añadir credenciales en la historia de tu repositorio es una mala práctica, aunque el repositorio sea privado.



## Eliminación de historia en git

Borrar información de la historia de commits de un repositorio git no es una tarea trivial. Por suerte, contamos con herramientas como [BFG Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner) que facilitan el uso de comandos como `git filter-repo`. 

Ten en cuenta por favor que aunque elimines el commit en el que añadías un commit con tus credenciales, el daño ya está hecho; tu contraseña se ha visto comprometida.




## Carga de scripts

Hay que evitar la colisión de nombres de funciones al trabajar con múltiples scripts.
Cuando se definen funciones con el mismo nombre en archivos JavaScript distintos que se cargan en el navegador, el contexto global es el navegador. Por tanto, la última implementación sobrescribirá a las anteriores. Esta sobrescritura no genera un error explícito, pero puede conducir a un comportamiento inesperado o difícil de depurar. 

Una solución a esta casuística sería utilizar IIFEs (Immediately Invoked Function Expressions). No obstante, para desarrollos sencillos bastaría con elegir nombres diferentes de funciones.


## DOMContentLoaded

En teoría hemos visto el uso del evento load y DOMContentLoaded, prefiriendo en general este último para la mayoría de los casos. A diferencia de ello, se ha observado en algunos casos dentro de los scripts que se invoca directamente a la función que hace un fetch. Por ejemplo:

```js
function getProperMessage(url) {
    fetch(url)
            .then(response => response.text())
            .then(response => renderBanner(response))
            .catch(err => console.error(err));

}

getProperMessage(url);
```

Esto hará que la función se invoque en el momento en que se cargue en este script, en vez de en un momento más propicio, cuando esté el "árbol DOM cargado" ("DOM content loaded").

## Inyección de dependencias

En algunas entregas se observa que para un *bean* como el siguiente, no se aprovecha la inyección de dependencias:

```java
@Service
public class ArticuloFinderImpl implements ArticuloFinder
```
Justamente `@Service` es una de las anotaciones que permiten poner a disposición el *bean* en contenedor de inyección de dependencias.

Así pues, no tiene mucho sentido utilizar el constructor y acoplarnos a una implementación determinada:

```java
ArticuloFinder finder = new ArticuloFinderImpl();
``` 

En general, no es la práctica recomendada en una aplicación Spring, ya que rompe la inyección de dependencias y la gestión de *beans* del framework. Una de las grandes ventajas de la inyección es el **desacoplamiento**. Es decir, permite que las clases no dependan de implementaciones concretas, sino de interfaces o abstracciones, así como facilitar los **tests**. 

Destaco de nuevo dos palabras clave que deberían acompañarte en tu carrera profesional: **desacoplamiento** y **testing**.


## Búsqueda ineficiente 

Usar los métodos `findAll` de los repositorios sin parámetros de filtro es un *bad smell*. 

```java
List<Oferta3X2Entity> list = o.findAll();
Oferta3X2Entity o1 = null;
if (list.size() != 0) {
    o1 = list.getLast();
}
List<ArticuloEntity> as = finder.findAll();
OfertaArticuloRestEntity oa = null;
if (o1 != null) {
    for (ArticuloEntity ae : as) {
        if (ae.getCodigo().compareTo(articleId) == 0) {
            oa = new OfertaArticuloRestEntity(o1, ae);
            break;
        }
    }
}
return oa;
```

La primera desventaja clara es que devuelve muchos artículos, al realizar una búsqueda lineal. Resulta mucho mejor delegar esa búsqueda a la base de datos. De hecho, en este caso habría sido mucho más eficiente hacer un `articuloRepository.findById` y `ofertaRepository.findById` para recuperar una única tupla (un único objeto) de base de datos.  


