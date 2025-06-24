# SQL

## Implementación muy ineficiente del método getArticulo

Imaginemos que se pide implementar el siguiente método:

```java
public Articulo findOneByCodigo(String codigo) throws ExcepcionDeAplicacion 
```

Algunas de las soluciones aportadas plantean un código como el siguiente:

```java
public Articulo findOneByCodigo(String codigo) throws ExcepcionDeAplicacion {
  for (Articulo a : getArticulos()) {
    if (codigo.equalsIgnoreCase(a.getCodigo())) {
        return a;
    }
  }
  return null;
}
```

Esta implementación es muy ineficiente desde el punto de vista del rendimiento:

- Se recuperan de la BD todos los datos de todos los artículos
- Se crea un objeto para cada uno de los artículos recuperados
- Se crea una lista para contenerlos
- Se recorre esa lista buscando el objeto que tenemos que encontrar (en el peor caso  se recorrerá la lista completa). Es un desperdicio de tiempo y memoria.

Imaginemos que en lugar de 447 artículos, en la BD hubiese 2 millones de artículos. Hay que tener en cuenta que en prácticas Tomcat y MySQL están en la misma máquina pero esto no necesariamente debe ser así. De hecho, con frecuencia se ubican en máquinas/nodos diferentes, con lo que el tráfico de red entre Tomcat y MySQL no es para nada despreciable.

Este método hay que implementarlo como todos los que acceden a la BD. Con una consulta, que en este caso, será muy rápida porque el campo codigo es clave primaria, lo que significa que la tabla tiene un índice que acelera las consultas. Además nos traeremos de la BD una única tupla y crearemos un único artículo. Mucho más rápido y con mucha menos memoria usada. 



Siguiendo en esta línea, si nos piden implementar el método siguiente:

```java
public List<Articulo> findAll() throws ExcepcionDeAplicacion
```

Podría pensarse en proponer la siguiente solución:

```java
PreparedStatement ps = con
    .prepareStatement(
        "select * from articulo"
    );
ResultSet rs = ps.executeQuery();
while (rs.next()) {
  l.add(this.getArticulo(rs.getString(1)));
}
```

Es una solución muy ineficiente porque, lo que se podría hacer con una única consulta (una única conexión a la BD), necesita de 1 + N consultas (siendo N el número de artículos). La solución es cómoda para el programador, pero muy ineficiente para la aplicación.

Si quisieras implementar una única vez la lógica de creación de los objetos `Articulo`, se podría haber optado por esta solución:

```java
private Articulo buildArticulo(ResultSet res) 
        throws SQLException {
  Articulo a = new Articulo(
            res.getString("codigo"), 
            res.getString("nombre"),
            res.getDouble("PVP"), 
            res.getString("tipo"),
            res.getString("fabricante"),
            res.getString("foto"),
            res.getString("descripcion")
    );
  return a;
}


public Articulo findOneByCodigo(String codigo) throws ExcepcionDeAplicacion {
  Articulo a = null;
  String sql = "SELECT * FROM Articulo WHERE codigo = ?";
  try (Connection con = ConnectionManager.getConnection();
       PreparedStatement ps = con.prepareStatement(sql);) {
    ps.setString(1, codigo);
    ResultSet res = ps.executeQuery();
    if (res.next()) {
      a = buildArticulo(res);
    }
    res.close();
  } catch (SQLException ex) {
    logger.error("No se puede componer articulo {}", codigo);
    logger.error("Error al recuperar articulo", ex);
    throw new ExcepcionDeAplicacion(ex);
  }
  return a;
}

public List<Articulo> findAll() throws ExcepcionDeAplicacion {
  List<Articulo> arts = new ArrayList<Articulo>();
  String sql = "select * from articulo";
  try (Connection con = ConnectionManager.getConnection();
       PreparedStatement ps = con.prepareStatement(sql);) {
    ResultSet res = ps.executeQuery();
    while (res.next()) {
      arts.add(buildArticulo(res));
    }
    res.close();
  } catch (SQLException ex) {
    throw new ExcepcionDeAplicacion(ex);
  }
  return arts;
}
```



## Uso de `upper`

Para evitar los problemas de comparación de cadenas que pueden estar
escritas con diferente patrón de mayúsculas/minúsculas es conveniente
convertir los textos a mayúsculas (o minúsculas) y hacer después la
comparación.

En primer lugar, hay que tener en cuenta que MySQL, por defecto, no tiene en cuenta las mayúsculas/minúsculas, ni los acentos, en las comparaciones de
cadenas de caracteres. La característica de los SGBD que regula ese
comportamiento se llama "*collation*"; y el collation que usa MySQL por
defecto no distingue esos aspectos.

Pero, imaginando que si distinguiese, algunos veces, al aplicar el
upper se hace mal:

```java
PreparedStatement ps = 
    con.prepareStatement(
        "select nombre from articulo" + 
        " where upper(nombre) like ?"
    );
ps.setStringg(1, "%" + nombre + "%");
ResultSet rs = ps.executeQuery();
```

Eso es solo parte de lo que hay que hacer. También hay que convertir a
mayúsculas el texto que os entregan en el parámetro nombre. Lo correcto
sería:


```java
PreparedStatement ps = 
    con.prepareStatement(
        "select nombre from articulo" + 
        " where upper(nombre) like ?"
    );
ps.setStringg(1, "%" + nombre.toUppercase() + "%");
ResultSet rs = ps.executeQuery();
```

Así la comparación es equitativa en ambas partes.

Naturalmente, habrá que asegurarse de que `nombre` no es `null`. De lo contrario, se desencadenará una `NullPointerException`. 
