# Poyectos de POO

Este repositorio tiene como propósito realizar las tareas de la materia "Programación Orientada a Objetos" (POO) pero con uno que otro patrón de programación funcional (FP).

Cada directorio se destina a un proyecto, compuesto del código fuente (carpeta _src_) y una pequeña documentación (archivo _README.md_) con las instrucciones del profesor e instrucciones de cómo compilar y ejecutar el proyecto desde línea de comandos. Igual es posible copiar la carpeta _src_ en tu IDE de preferencia (_NetBeans_, _IntelliJ IDEA_ o _Eclipse_) y ejecutar los proyectos desde ahí. Yo codifico y ejecuto los proyectos en IntelliJ IDEA.

## Conceptos relacionados a un estilo funcional

Así como la programación orientada a objetos tiene los principios SOLID, la programación funcional tiene sus formas para lograr el desarrollo de código mantenible, robusto, legible, bonito, etc.

Los conceptos de POO deben ser abarcados por el profesor, así que no serán explicados en este documento.

### Transparencia

Esto quiere decir que una función debe recibir todos sus datos de entrada mediante parámetros. En otras palabras, se evita el uso de `this`. En realidad esto no es ninguna limitante, pues cualquier método puede ser visto como una función que además de recibir datos de forma explícita, mediante parámetros, recibe datos de forma implícita debido al `this`. A mí me funciona ser explícito al describir mis ideas.

Quizá te preguntes _¿cómo puedo llamar a otros métodos sin el uso de `this`?_, y la respuesta es que puedes pasar el método como parámetro. En programación funcional, las funciones también son valores. Las funciones que reciben o devuelven otras funciones se conocen como [funciones de orden superior](https://eloquentjavascript.net/05_higher_order.html). Java facilita este patrón con el [operador `::`](https://www.geeksforgeeks.org/double-colon-operator-in-java/) y las [interfaces funcionales](https://www.educative.io/edpresso/a-list-of-all-the-functional-interfaces-in-java).

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

Por ejemplo: se tiene un arreglo de números enteros **_arrayA_** y en una parte de un sistema se desean usar todos sus valores que no sean nulos. Una solución es crear un nuevo arreglo **_arrayB_** con todos los valores no-nulos de **_arrayA_**. En la forma imperativa tendríamos que crear una lista, guardar los elementos no-nulos en esa lista y luego convertirla a un arreglo.

```java
Integer[] arrayA = new Integer[] { 1, 2, null, 3, null, 4, 5 };

List<Integer> listB = new ArrayList<>();
for (Integer obj : arrayA) {
    if (Objects.nonNull(obj)) {
        listB.add(obj);
    }
}

Integer[] arrayB = listB.toArray(new Integer[listB.size()]);

// Hacer algo con B
// ...
```

Una forma declarativa de solucionar este problema es llamando a una función que filtre todos los valores de acuerdo al criterio "el valor no debe ser nulo". En java podemos usar [Stream.filter](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/stream/Stream.html).

```java
Integer[] arrayA = new Integer[] { 1, 2, null, 3, null, 4, 5 };


Integer[] arrayB = Arrays.stream(arrayA)
            .filter(Objects::nonNull)
            .toArray(Integer[]::new);

// Hacer algo con B
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
