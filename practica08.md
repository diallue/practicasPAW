# Práctica 8

## Local Storage Vs Session Storage

Los objetos de almacenamiento `localStorage` y `sessionStorage` permiten guardar pares de clave/valor en el navegador. En ambos casos son parte de la [API de almacenamiento web](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API).

En el caso de *Local Storage*, los datos almacenados persisten incluso después de que el navegador se haya cerrado y vuelto a abrir. No tienen tiempo de expiración a menos que se limpie de forma explícita por el usuario o por la aplicación. A nivel de ámbito, la información es compartida entre todas las pestañas o ventanas que tenga el mismo origen (protocolo, dominio y puerto). Resulta por tanto un objeto apropiado para guardar preferencias del usuario, caché de datos para uso offline o para la persistencia de datos que necesitan más tiempo de vida que las sesiones.

Por otra parte, en el caso de *Session Storage* la información sólo está disponible durante la sesión y se limpia cuando la pestaña o navegador se cierran. Mantiene un área de almacenamiento separada para cada origen que está disponible. 


## Conversión entre JSON y objetos JavaScript

Métodos para la manipulación de datos JSON:

- `JSON.parse(jsonString)`: Convierte texto JSON a objeto JavaScript.
- `JSON.stringify(obj)`: Convierte un objeto JavaScript a cadena JSON.

Ejemplos:

```js
const jsonString = '{"nombre":"Leticia", "edad": 45}';
const p1 = JSON.parse(jsonString);

console.log(p1.nombre);  
console.log(p1.edad);    


const p2 = {
  nombre: "Carlos",
  edad: 40
};

const json = JSON.stringify(p2);
console.log(json);
```

Una vez que se *parsea* el JSON en un objeto JS, se pueden modificar sus valores a través de sus respectivas propiedades, usando la notación punto o corchetes:

```js
p1.edad = 45;
p1['edad'] = 45;
```

