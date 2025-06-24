# JavaScript

## Encadenamiento opcional
El operador de encadenamiento opcional (*optional chaining*) `?.` permite acceder de forma segura a propiedades anidadas de un objeto sin causar errores si alguna propiedad intermedia es null o undefined.

Ejemplo:

```js
const userStreet = user.address ? user.address.street : undefined;
```

```js
const userStreet = user?.address?.street;
```

Este operador tiene múltiples usos: acceder a propiedades de objetos, llamar a funciones si existen o acceder a elementos de arrays.

## Operador "nullish coalescing"

El operador "nullish coalescing" (fusión nula) resulta muy útil para proporcionar un valor por defecto cuando el valor de la izquierda es null o undefined (a diferencia de ||, que también considera falsy otros valores como 0, false o ""). 

Es decir, el resutlado de la expresión `a ?? b` será `b` si `a`  es null o undefined. Y será `a` en caso contrario.

Por ejemplo, el siguiente código:

```js
let result = (a !== null && a !== undefined) ? a : b;
```

Se puede reescribir como `let result = a ?? b;`

Siguiendo en esta línea, podemos seguir con el operador de asignación nula. Si tenemos el siguiente código:

```js
if (value === null || value === undefined)
    value = newValue;
```

Podemos reducir al siguiente:

```js
value ??= newValue
```

Compara este comportamiento con el operador `||` que en general tiene en cuenta los valores *falsy*.


## `this`

`this` es una palabra clave que hace referencia al contexto de ejecución actual, es decir, al objeto al que pertenece la función que se está ejecutando. Su alcance o *scope* es dinámico, lo cual significa que viene determinado en ejecución y su valor depende de cómo se llama la función, no de dónde está definida.

En el *scope* global, `this` se refiere al objeto global, `window` en el caso de JS en el navegador.

En el contexto de función, `this` se refiere al objeto global (en modo no estricto).

En el contexto de un objeto, `this` se refiere al objeto propietario del método.

```js
const persona = {
  nombre: 'Leticia',
  saludar() {
    console.log(`Hola, soy ${this.nombre}`);
  }
};

persona.saludar();
```

En los manejadores de eventos, `this` referencia al elemento HTML que dispara el evento cuando se usan funciones tradicionales. En el siguiente ejemplo, `this` se refiere al botón, el objeto que dispara el evento:

```html
<button id="miBoton">Haz clic</button>

<script>
  const boton = document.getElementById('miBoton');

  boton.addEventListener('click', function() {
    console.log(this); 
  });
</script>
```

Las *arrow functions* no tienen su propio `this`, sino que heredan el `this` del contexto donde fueron definidas. En su lugar, heredan `this` del contexto léxico en el que están definidas. 

