# Práctica 1

## Tips
- Cuidado con el contraste del color de fore/back
- Checkbox cerca de su label
- Proporción de la fuente (títulos, etiquetas muy pequeñas y resto tamaño normal)
- Selectores CSS con clase, mejor que con id salvo excepciones ( <legend id="images-l">Imágenes</legend>) 

## Buen uso de nth-of-type
.main legend:before {
    content: '';
    display: inline-block;
    width: 20px;
    height: 20px;
    margin-right: 10px;
    background-size: cover;
    background-position: center;
}

.main fieldset:nth-of-type(1) legend:before {
    background-image: url('../images/forms/tags.svg');
}

## Nombrado de clases CSS

```html
<legend class="informaciónBásica">
    Información Básica
</legend>
```

Mejor no usas caracteres especiales o mayúsculas. En general, es mejor adoptar prácticas habituales como *kebab-case*.

## Uso de onclick

En general, el uso de manejadores de eventos *inline* está desaconsejado:

<fieldset class="form-section imagenes">    
    <label for="imagen">Imagen</label>
        <!-- Botón personalizado para seleccionar archivo -->
    <button type="button" class="btn seleccionar-archivo" onclick="document.getElementById('imagen').click();">
        Seleccionar archivo
    </button>
    <!-- Input tipo file que está oculto -->
    <input type="file" id="imagen" name="imagen" accept="image/*" style="display:none;">
    <span id="ningunArch">Ningún archivo seleccionado</span>
</fieldset>

## Inclusión de CSS

Dados los estilos CSS añadidos de esta forma:

```html
<link rel="stylesheet" href="./assets/css/electrosa.css">
<link rel="stylesheet" href="./assets/css/forms.css">
```

Cada archivo tendrá su contenido pero la idea de CSS es que los estilos se apliquen **en cascada**. No aporta nada que el archivo `forms.css` sea igual que `electrosa.css` al que hemos añadido estilos adicionales. Es decir, si `electrosa.css` tiene 180 líneas y `forms.css` tiene 270, de las cuales las 180 primeras son iguales, estas primeras líneas no son necesarias. Y además, desde el punto de vista de mantenimiento de código, consecuentemente será peor. 




