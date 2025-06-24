# JSP

## No repitas código JSP
Consideremos este extracto de JSP que, dependiendo de si recibe o no un
parámetro llamado nombre, pone el valor de este parámetro en el atributo
value de un input:


```jsp
<label for="nombre">
    Nombre del artículo: 
</label>
<%
    if (request.getParameter("nombre") == null) {
%>
   <input type="text"
        name="nombre"
        value="<%= request.getParameter("nombre")%>" />  
<%
    } else {
%>
   <input type="text" 
        name="nombre" 
        value=""/>
<%
    }
%>
```

Se está repitiendo la ejecución de una instrucción cuando no es necesario. Se podría pensar que sólo se hace dos veces, que el tiempo que cuesta ejecutar no es relevante. En cualquier caso, el ejemplo vale para llamar la
atención sobre este tipo de aspectos que lo único que hacen es oscurecer el
código y hacerlo más difícil de leer y más lento de ejecutar.

Lo correcto sería haber usado una variable para almacenar el valor del
parámetro, y usarla después todas las veces que fuese necesario:


```jsp
<label for="nombre">
    Nombre del artículo: 
</label>
<%
    String nombre = request.getParameter("nombre");
    if (nombre == null) {
%>
   <input type="text" 
        name="nombre" 
        value="<%= nombre%>"/>  
<%
    } else {
%>
   <input type="text" 
        name="nombre" 
        value=""/>
<%
    }
%>
```

## Operador ternario

Conviene recordar un operador que suele olvidarse. Se trata del operador ternario de Java, que naturalmente se puede usar en JSPs.

Sirve para simplificar el código y reducir el número de scriptlets en
los JSP. Usando este operador, el código de la entrada anterior puede quedar en lo siguiente:

```jsp
<label for="nombre">
    Nombre del artículo: 
</label>
<%
    String nombre = request.getParameter("nombre");
%>
   <input type="text" 
    name="nombre" 
    value="<%= nombre != null ? nombre : ""%>" 
    />  
```
