# Práctica 7

## Preguntas

3. A lo largo de las prácticas has venido desarrollando dos aplicaciones (*frontoffice* y *backoffice*) para Electrosa. La administración de fotos de artículos se realiza en *backoffice*. ¿Qué ocurrirá si el usuario administrador cambia la foto de un artículo? ¿Se verá actualizada en el catálogo de *frontoffice*? Si te parece que se presentaría una situación problemática, indica qué solución adoptarías.

En esta pregunta se han observado multitud de respuestas diversas. Por favor, reflexiona sobre ello y revisa el comportamiento de las dos aplicaciones. Prueba a cambiar la imagen de un artículo desde el backoffice. ¿La ves desde el frontoffice? ¿Cuál es la URL de la imagen? ¿Con qué ruta del sistema de ficheros de tu equipo se corresponde?

Piensa que este comportamiento sería similar si las aplicaciones están desplegadas en producción.

Siguiendo en esta línea, imagina que la aplicación backoffice estuviera desplegada en dos tomcat de servidores distintos, con un servidor web o balanceador que reparte la carga entre los dos servidores. ¿Qué pasaría con las imágenes entonces? 