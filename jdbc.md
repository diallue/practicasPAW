# JDBC

# Mal uso de `PreparedStatement`

Algunas veces se observa el siguiente código en las entregas:

```java
PreparedStatement ps = con.prepareStatement(
    "select nombre from articulo where nombre like '%"
    + nombre + "%'"
    );
ResultSet rs = ps.executeQuery();
```

En el SQL a preparar no hay parámetros, cuando sí debería haberlos. Se está usando un `PreparedStatement` como un `Statement` normal, sin aprovechar su potencialidad. Perdéis estas ventajas:

-   El SQL es susceptible de sufrir ataques SQL injection

-   Os tenéis que preocupar del formato de los datos en el SQL (notar el
    uso de los apóstrofos en \...like \'%\"**+**nombre**+**\"%\'\".

Podríais haber evitado esto usando bien los `PreparedStatement`. Deberíais
haber usado:


```java
PreparedStatement ps = con.prepareStatement(
    "select nombre from articulo" + 
    " where nombre like ?"
    );
ps.setStringg(1,"%" + nombre + "%");
ResultSet rs = ps.executeQuery();
```



## Recuerda cerrar la conexión

Cuando se abre una conexión a base de datos manualmente es necesario cerrarla en cualquier caso. Si no se cierra, después de un número limitado de usos se sufrirá un fallo similar al siguiente:
 
```
java.sql.SQLNonTransientConnectionException: Data source rejected
establishment of connection, message from server: \"Too many connections\"
at com.mysql.cj.jdbc.exceptions.SQLError.createSQLException(SQLError.java:110)
at com.mysql.cj.jdbc.exceptions.SQLError.createSQLException(SQLError.java:97)
at com.mysql.cj.jdbc.exceptions.SQLExceptionsMapping.translateException(SQLExceptionsMapping.java:122)
at com.mysql.cj.jdbc.ConnectionImpl.createNewIO(ConnectionImpl.java:836)
at com.mysql.cj.jdbc.ConnectionImpl.\<init\>(ConnectionImpl.java:456)
at com.mysql.cj.jdbc.ConnectionImpl.getInstance(ConnectionImpl.java:246)
```

Y si no se cierra en un bloque finally, se corre el riesgo de que la
conexión quede sin cerrar debido a la ocurrencia de algún error de SQL
que aborte la ejecución del método antes de que termine.


## Crear una única conexión para todos los metodos

A la hora de crear una conexión se puede tener la tentación de hacerlo en el constructor. En el siguiente ejemplo,  GestorBD obtiene una conexión a la BD que se usa en todos los métodos de la clase.

```java
public class GestorBD {
  
private Connection con = null;
  
public GestorBD() {  
  try {
    InputStream is = 
        GestorBD.class
            .getResourceAsStream("/conexion.properties");
    Properties props = new Properties();
    props.load(is);
    ...
    
    Class.forName("com.mysql.jdbc.Driver");
    
    this.con = DriverManager.getConnection("...", "...", "...");
  } catch (ClassNotFoundException | SQLException ex) {
    ex.printStackTrace();
  }
}

public List<String> getArticulosLike(String nombre) {
  ...
  try {
    Statement st = con.createStatement();
    ...
  } finally {
      if(con!=null) {
        con.close();
```


Este código tiene varios problemas:

-   La conexión es compartida por todos los métodos de la clase. Si se crease un único objeto `GestorBD` para toda la aplicación web, todos los usuarios compartirían la misma conexión, es decir, el mismo contexto transaccional. Un commit de una operación de un usuario, confirmaría también las operaciones de otro usuario, aunque no estuviesen terminadas. También perderíamos el aislamiento entre las transacciones de los diferentes usuarios.

-   Si la conexión se crea en el constructor del objeto, ¿dónde se cierra? El lugar escogido (en los métodos) no es adecuado por lo siguiente.

-   Si se tuviese mucho cuidado y se usase un objeto `GestorBD` diferente para cada usuario, este no podría invocar varios métodos sobre el mismo objeto, porque en cada invocación de método se cierra la conexión, que es una propiedad del objeto. El siguiente método se encontraría con la conexión cerrada. Un método no debería cerrar algo que él no ha abierto

Lo correcto es que cada método consiga su propia conexión, la use en aislamiento con otras operaciones del mismo o diferente usuario, y al acabar se asegure de cerrar la conexión usada (o de devolverla al pool de conexiones usado).
