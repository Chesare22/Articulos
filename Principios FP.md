## Conceptos básicos de programación

### Valores (o datos)

Existen muchos tipos de valores. Existen números enteros, números decimales, colecciones de números enteros, cadenas de texto, archivos de excel, etc. Nótese que un dato no es lo mismo que la visualización de un dato. Por ejemplo, un archivo de excel es un dato que la computadora representa en bits, pero para su visualización es necesaria una interfaz gráfica que nos presente ese dato.

### Programas

Un programa es aquello que maneja datos, ya sea para modificarlos, visualizarlos, guardarlos, compartirlos, crearlos, etc. Un programa está compuesto por instrucciones. Cada instrucción es muy simple. Hay instrucciones para esperar datos del usuario, para hacer operaciones sobre datos, etc. Existen instrucciones que deciden qué otras instrucciones ejecutar.

### Funciones

En programación, las funciones son colecciones de instrucciones en secuencia. Una función recibe datos de entrada y al final siempre devuelve un dato. Existen funciones, llamadas funciones puras, que solo se limitan a convertir los datos de entrada en datos de salida. Más adelante se discutirán a fondo.

### Objetos

Un objeto junta funciones y datos en una misma entidad. Las funciones dentro de un objeto se llaman "métodos", y los datos se llaman "propiedades". Un método puede acceder a las propiedades y otros métodos del objeto mediante la palabra `this`.

## Conceptos relacionados a un estilo funcional

Así como la programación orientada a objetos tiene los principios SOLID, la programación funcional tiene sus formas para lograr el desarrollo de código mantenible, robusto, legible, bonito, etc.

### Transparencia

Esto quiere decir que una función debe recibir todos sus datos de entrada mediante parámetros. En otras palabras, se evita el uso de `this`. En realidad esto no es ninguna limitante, pues cualquier método puede ser visto como una función que además de recibir datos de forma explícita, mediante parámetros, recibe datos de forma implícita debido al `this`. A mí me funciona ser explícito al describir mis ideas.

Quizá te preguntes _¿cómo puedo llamar a otros métodos sin el uso de `this`?_, y la respuesta es que puedes pasar el método como parámetro. En programación funcional, las funciones también son valores. Las funciones que reciben o devuelven otras funciones se conocen como [funciones de orden superior](https://eloquentjavascript.net/05_higher_order.html).

Para algunos la sintaxis tiene mucha importancia. Al inicio ser explícito es verboso, pero patrones como [aplicación parcial](https://github.com/getify/Functional-Light-JS/blob/master/manuscript/ch3.md/#some-now-some-later), [_point-free style_](https://github.com/getify/Functional-Light-JS/blob/master/manuscript/ch3.md/#no-points) y [composición de funciones](https://youtu.be/srQt1NAHYC0?t=563) ayudan a mitigar esto. Existen lenguajes de programación como Elixir o Elm [cuya sintaxis facilita estos patrones](https://dennisreimann.de/articles/elm-functions.html).

**Nota técnica:** Para que la aplicación parcial sea posible es necesario que el lenguaje de programación en cuestión pueda hacer [_closures_](https://github.com/getify/You-Dont-Know-JS/tree/1st-ed/scope%20%26%20closures) o algo similar.

### Determinismo

Una función determinista es aquella que siempre calcula el mismo valor dados los mismos argumentos.

**TODO: Describir por qué datos y funciones son conceptos intercambiables.**

### Inmutabilidad

> El comando _GOTO_ era malvado porque hacía que nos preguntemos "¿cómo llegué a este punto en la ejecución?".
> La mutabilidad hace que nos preguntemos "¿cómo llegué a este estado?".
>
> — [Jessica Joy Kerr](https://twitter.com/jessitron/status/333228687208112128)

**TODO: Describir qué es la inmutabilidad.**

Mandar un objeto mutable a un método cualquiera puede llegar a dar miedo porque no siempre se sabrá el valor del objeto después de invocar el método. Una forma fácil pero poco optimizada para evitar esta situación es mandando copias del objeto. Otra forma de evitarla es con el uso de [estructuras de datos inmutables](https://youtu.be/Wo0qiGPSV-s).

### Evitar efectos secundarios

**TODO: Describir qué es un efecto secundario.**

Obsérvese que las funciones puras también son transparentes, deterministas y no mutan datos.

Recomiendo ver la plática [Functional architecture - The pits of success — Mark Seemann](https://youtu.be/US8QG9I1XW0), donde explica cómo se pueden construir sistemas grandes formados mayormente por funciones puras.

### La programación funcional es declarativa

En lugar de escribir los pasos para llegar a un resultado, en un [paradigma declarativo](https://www.ionos.mx/digitalguide/paginas-web/desarrollo-web/programacion-declarativa/) se escribe qué resultado se desea.

Por ejemplo: se tiene un arreglo de números enteros **_arrayA_** y en una parte de un sistema se desean usar todos sus elementos que no sean nulos. Una solución es crear un nuevo arreglo **_arrayB_** con todos los elementos no-nulos de **_arrayA_**. En una implementación imperativa seguimos los siguientes pasos:

1. Crear un nuevo arreglo.
1. Ciclar sobre el arreglo original. Dentro del ciclo preguntamos si un elemento no es nulo. Si la condición anterior se cumple, agregamos el elemento en cuestión al nuevo arreglo.

```javascript
const arrayA = [1, 2, null, 3, null, 4, 5];

const arrayB = [];
for (let i = 0; i < arrayA.length; i++) {
  if (arrayA[i] !== null) {
    arrayB.push(arrayA[i]);
  }
}

// Hacer algo con arrayB
// ...
```

Una implementación declarativa puede ser llamar a una función que filtre todos los elementos de acuerdo al criterio "el valor no debe ser nulo".

```javascript
function isNotNull(value) {
  return value !== null;
}

const arrayA = [1, 2, null, 3, null, 4, 5];

const arrayB = arrayA.filter(isNotNull);

// Hacer algo con arrayB
// ...
```

### Operadores de listas

¿Cuántos ciclos pueden ser reemplazados por funciones genéricas? Ya vimos la función `filter` como un sustituto del ciclo `for` en cierto escenario. Hay muchos otros escenarios donde los ciclos se pueden generalizar. Por ejemplo: aplicar la misma función sobre una secuencia de datos. La función [`List.map` en Elm](https://package.elm-lang.org/packages/elm/core/latest/List#map) recibe una función que trabaja en elementos y la convierte en una función que trabaja en listas:

```elm
sumarDos número = número + 2

sumarDos 4
-- Resultado: 6

sumarDosLista = List.map sumarDos

sumarDosLista [1, 2, 3, 4, 5]
-- Resultado: [3, 4, 5, 6, 7]
```

Algunas funciones, conocidas como operadores de listas, tienen como objetivo abstraer el código encargado de hacer ciclos. Para más información sobre los operadores de listas, leer [Functional-Light Javascript Chapter 9](https://github.com/getify/Functional-Light-JS/blob/master/manuscript/ch9.md). La plática [A Skeptics Guide To Functional STYLE JavaScript — Jonathan Mills](https://youtu.be/oF9XTJoScOE?t=430) menciona, entre otras cosas, cómo el uso ocasional de dichas funciones puede ayudar en el desarrollo de software.
