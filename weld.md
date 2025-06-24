# Inyección de dependencias con Weld

Spring es un framework muy versátil que ofrece una amplia gama de funcionalidades y ventajas destinadas a facilitar el trabajo del desarrollador. Entre sus características más destacadas se encuentran la definición flexible de controladores, el escaneo automático de paquetes para la inyección de dependencias, así como la capacidad de integrarse con otros frameworks o bibliotecas, entre los cuales se incluye el acceso a bases de datos o para el uso de motores de plantillas en la generación de vistas.

Debido a su robustez y extensibilidad, Spring suele considerarse la opción de facto para el desarrollo de aplicaciones web en Java. No obstante, existen alternativas viables que pueden cumplir funciones similares, dependiendo del contexto y los objetivos del proyecto.

De la misma forma que para aprender sistemas operativos, puede estudiarse el código fuente de Linux u otro sistema operativo de código abierto, para comprender el funcionamiento del patrón MVC podría estudiarse del [código fuente de Spring MVC](https://github.com/spring-projects/spring-framework).   

Con el propósito de ilustrar algunas de las capacidades que ofrece Spring, es posible prescindir de este framework e implementar un sistema propio que reproduzca ciertas funcionalidades equivalentes. 

Se dispone de un proyecto en el repositorio [Winter framework](https://github.com/unirioja-paw/winter-framework) que usa Weld para la inyección de dependencias, y prescinde de toda dependencia de Spring.  

La propuesta consiste en desarrollar un servlet que actúe como *front controller*, capaz de identificar y gestionar controladores definidos por el desarrollador mediante técnicas de reflexión. Aunque existen múltiples aproximaciones posibles, esta metodología guarda similitudes conceptuales con el funcionamiento interno de Spring.

En este sentido, el proyecto puede interpretarse como una etapa intermedia entre una arquitectura basada únicamente en servlets y vistas JSP, y un modelo más completo como el que ofrece Spring MVC.

El proyecto maven incluye dos paquetes bien diferenciados:

- `io.paw.winter`: clases propias del framework. Los nombres de las clases presentan similitudes para que las puedas relacionar con las correspondientes en Spring.
- `es.unirioja.paw`: clases que desarrolla el desarrollador que utiliza el framework.

Es interesante que estudies cómo funciona el proyecto ya que ayuda a entender cómo se podría haber desarrollado un framework web MVC sin la complejidad natural que puede presentar Spring después de los evolutivos y contribuciones al proyecto a lo largo del tiempo.  