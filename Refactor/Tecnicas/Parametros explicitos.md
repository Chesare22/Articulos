## Cómo refactorizar parámetros explícitos

#### Objetos como parámetros explícitos
Cuando una función tiene muchos parámetros es más claro distinguir qué significa cada uno si en lugar de recibir los parámetros directamente se recibe un objeto que los contenga.
```javascript
search('something', 20);
// vs.
search({query: 'something', pageSize: 20});
```
El problema con este enfoque es que dentro de la función `search` es difícil darle un nombre relevante al _wrapper_ de los parámetros. Esto se puede solucionar con la [asignación desestructurante](https://developer.mozilla.org/es/docs/Web/JavaScript/Referencia/Operadores/Destructuring_assignment), permitiendo no nombrar al _wrapper_.
```javascript
function search(params){
  let query = params.query;
  let pageSize = params.pageSize;
}
//  vs.
function search({query, pageSize}) {}
```

#### Valores por defecto
Los parámetros explícitos pueden tener valores por defecto, haciendo que algunos argumentos sean opcionales para ciertos casos. A mí me ha sido muy útil para la función `constructor` de las clases. Igual es posible que los parámetros desestructurados tengan valores por defecto.

Imagina que la función `search` usa el valor de `pageSize` en más de una ocasión. En el caso de no enviarlo como argumento, ¿Cómo se comportaría la función? ¿Arrojo un error, agrego más condicionales dentro de la función, uso un valor por defecto o ignoro ese escenario? La respuesta puede variar de función a función. Si ignorar ese escenario no funciona, la solución más sencilla es usar valores por defecto.
```javascript
function search({query, pageSize = 20}) {
  // No es necesario comprobar la existencia de pageSize con condicionales
}
```

#### Currying
Este método es recomendado si se opta por el paradigma de [Programación Funcional](../Conceptos/Programacion%20funcional.md). En lugar de que una función sea invocada una sola vez con todos sus parámetros, la función recibe un parámetro y devuelve una función que espera otro parámetro. Esto se repite hasta que la función no tenga más parámetros, devolviendo el resultado final.
```javascript
function add(a, b) {
  return a + b;
}
add( 1, 2 );
// vs.
function add(a) {
  return function(b) {
    return a + b;
  }
}
add(1)(2)
```