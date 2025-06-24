# Migración javax a jakarta

El cambio de Java EE a Jakarta EE trajo consigo la necesidad de evolucionar los proyectos. Para empezar, la versión de JDK es la 21. Esto lleva a que la versión de Servlet sea `jakarta.servlet` en vez de `javax.servlet`.

Esto se puede ver en el pom.xml de partida según se clona el repositorio git:

```xml
<dependency>
    <groupId>jakarta.platform</groupId>
    <artifactId>jakarta.jakartaee-api</artifactId>
    <version>10.0.0</version>
    <scope>provided</scope>
</dependency>
```

En algunos casos se observa que en el proyecto el estudiante añade la dependencia:

```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>servlet-api</artifactId>
</dependency>
```

Esta coexistencia de dependencias va a hacer que el proyecto no se ejecute correctamente. Y lleva a situaciones en las que si por ejemplo se hace un *import* de  `HttpSession` terminaríamos teniendo el código:

```java
import javax.servlet.http.HttpSession;
```

**Esto no es correcto**. En su lugar debería ser:

```java
import jakarta.servlet.http.HttpSession;
```

Como resumen, en el pom.xml la dependencia deberá ser de Jakarta. No se debe añadir la dependencia de javax.servlet. Y en cualquier caso, no se debe hacer `import javax.servlet.`.


Más información en [https://blogs.oracle.com/javamagazine/post/transition-from-java-ee-to-jakarta-ee](https://blogs.oracle.com/javamagazine/post/transition-from-java-ee-to-jakarta-ee)