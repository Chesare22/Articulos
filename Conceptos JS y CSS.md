# Conceptos de JavaScript y CSS que me hubiera gustado aprender en la escuela
No me malentiendan, aprendí mucho en la facultad. Desarrollo de Aplicaciones Web fue una materia de la que muchos compañeros se enamoraron, sin ella no sabría jugar con el DOM, qué es Ajax, fundamentos de php, etc.

Es de esperar que en un curso no intensivo de 6 meses haya poco espacio para adentrarse en detalles y truquitos de cada lenguaje. El lado positivo es que todavía se cuenta con el resto de una vida para aprenderlos y hay muchas personas dispuestas a enseñar. Tener buenos compañeros de trabajo ayuda dramáticamente.

### No es necesario sumar cadenas
Bueno, este apartado será corto. Dudaba si designarle todo un subtítulo a las **Template literals** (o template strings), pero no podía dejar de lado a uno de mis trucos favoritos de ES6+. Estas dos líneas son equivalentes:
```javascript
'Bienvenido ' + nombre + ', son las ' + (new Date().getHours() + 1) + ' horas';

`Bienvenido ${nombre}, son las ${new Date().getHours() + 1} horas`;
```
Al usar acentos graves `` ` `` se pueden agregar expresiones dentro de un `${}`, evitando concatenar cadenas y expresiones.

### ¿A dónde apuntan esas flechas?
La primera vez que vi un símbolo de igual junto al "mayor que" creí que era un operador lógico. Leía y releía los `=>` pero nada cobraba sentido. Entonces lo encontré, aquel video de YouTube que decía _eso es una función flecha_. Desde ese entonces no he parado de usar tan conveniente notación.
#### Sintaxis
La sintaxis (simplificada) de una función anónima es `function(<argumentos>) {<cuerpo>}`. La sintaxis equivalente para la función flecha es `(<argumentos>) => {<cuerpo>}`, pero la segunda tiene un poquito más de azúcar.
+ **Paréntesis opcionales:** Si la función flecha recibe un solo argumento que no [se desestructura](https://developer.mozilla.org/es/docs/Web/JavaScript/Referencia/Operadores/Destructuring_assignment "Asignación Desestructurante - JavaScript | MDN"), se pueden omitir los paréntesis del argumento. Es decir, `(<argumento>) => {<cuerpo>}` equivale a `<argumento> => {<cuerpo>}`.
+ **Retorno implícito:** Si el cuerpo la función flecha solo consta de retornar una expresión, eliminar los corchetes hará que el `return` sea implícito. Es decir, `(<argumentos>) => {return <expresión>}` equivale a `(<argumentos>) => <expresión>`.

Por ejemplo: se tiene un arreglo con números y se desea saber cuáles son mayores a 3. Usemos al conocido método `Array.prototype.filter()`. Por si no habías escuchado de él, _crea un nuevo array con todos los elementos que cumplan la condición implementada por la función dada_ (Copypasteado de [MDN](https://developer.mozilla.org/es/docs/Web/JavaScript/Referencia/Objetos_globales/Array/filter "Array.prototype.filter() - JavaScript | MDN")).
```javascript
const numbers = [5, 8, 1, 0, 10, -4];

const usingFunction = numbers.filter(function(number){
  return number > 3
});

const usingArrow = numbers.filter(number => number > 3);

// Tanto usingFunction como usingArrow serán [5, 8, 10]
```

#### Más que un sustituto de `function`
Voy a plantear un problema antes de decir dónde entran las `=>`. Dentro del método de un objeto se quiere llamar a una promesa y se desea almacenar, en el mismo objeto, algún valor que diga si la promesa se cumplió o no. Por motivos de simplicidad, la promesa en cuestión se llamará _somePromise_ y no diremos qué hace.
```javascript
const obj = {
  promiseSuccessful: false,
  someMethod() {
    somePromise().then(function() {
      this.promiseSuccessful = true;
    });
  }
};
obj.someMethod();
```
Uno podría pensar que el valor de `obj.promiseSuccessful` sea `true` si la promesa es exitosa, pero [la manera en que this funciona](https://medium.com/better-programming/understanding-the-this-keyword-in-javascript-cb76d4c7c5e8 "Understanding the “this” Keyword in JavaScript - Better Programming - Medium") hace que este no sea el caso, porque el `this` dentro de la función anónima apunta a un _this global_ en lugar de a `obj`.

Conozco dos soluciones. Una es no usar `this` dentro de la función anónima.
```javascript
// ...
  someMethod() {
    const self = this;
    somePromise().then(function() {
      self.promiseSuccessful = true;
    });
  }
// ...
```
La otra solución es usar una función flecha. **La característica más distintiva** de este tipo de funciones es que su `this` apunta al mismo `this` donde fue declarada.
```javascript
// ...
  someMethod() {
    somePromise().then(() => {
      this.promiseSuccessful = true;
    });
  }
// ...
```
En este ejemplo, la función flecha fue declarada dentro de _someMethod_. Como el `this` dentro de _someMethod_ apunta a `obj`, el `this` dentro de la función flecha también apuntará a `obj`.

#### Cuándo no usarlas
Si al crear un objeto se usan funciones flecha para declarar sus métodos, el `this` de tales métodos no va a apuntar al objeto, sino al `this` del lugar donde se creó. Por ejemplo: las siguientes líneas tienen un problema muy similar al que teníamos inicialmente. Debido al uso de `=>` al declarar _someMethod_, el `this` hace referencia al lugar donde se creó `obj`, no al mismo `obj`.
```javascript
const obj = {
  promiseSuccessful: false,
  someMethod: () => {
    somePromise().then(() => {
      this.promiseSuccessful = true;
    });
  }
};
```

<p align="center">
  <img src="assets/images/This-is-complicated-2.jpeg" width="350">
</p>

### ¿Cómo se posiciona un elemento encima de otro?
He aquí la parte de CSS que prometí en el título

### Expandiendo la idea de clave-valor
Recuerdo el día que me hablaron de [Object.defineProperty()](https://developer.mozilla.org/es/docs/Web/JavaScript/Referencia/Objetos_globales/Object/defineProperty "Object.defineProperty() - JavaScript | MDN") como si fuera ayer. Quería almacenar (en formato _JSON_) un arreglo de objetos y cierta dependencia requería que los objetos tengan un atributo que no me interesaba guardar, pero lo complicado era que los objetos podían tener una estructura diferente. Después de intentar una complicada solución (y fallar) le pedí ayuda a un amigo. Su respuesta cambió mi manera de ver los objetos en JavaScript.
#### "Usa `Object.defineProperty()`"
```javascript
const persona = {
  nombre: 'Esteban Dido'
};
```
Viendo la sentencia anterior, podemos dar por hecho una serie de características sobre _nombre_:
+ Guarda un valor (`value`).
+ Se le puede asignar otro valor (`writable`).
+ Se puede configurar (`configurable`): Nada impide que más adelante alguien elimine _nombre_ de _persona_ o modifique sus descriptores (en breve diremos qué es eso).
+ Aparece al enumerar las propiedades (`enumerable`): Esto quiere decir que los resultados de métodos como `JSON.stringify(persona)` u `Object.keys(persona)` incluyen la propiedad _nombre_ .

Estas características [y un par más](https://www.jackfranklin.co.uk/blog/es5-getters-setters/ "JavaScript Getters and Setters - Jack Franklin") son los **_descriptores_** de una propiedad, los cuales se pueden configurar con `Object.defineProperty()`, un método muy especial que recibe 3 argumentos:
1. El objeto al cual se le va a agregar o configurar la propiedad.
2. La clave de la propiedad.
3. Un objeto con sus descriptores.
```javascript
// Forma explícita de declarar "persona".
const persona = {};
Object.defineProperty(
  persona,
  'nombre',
  {
    value: 'Esteban Dido',
    writable: true,
    configurable: true,
    enumerable: true
  }
);
```
Si quiero que _persona_ tenga la propiedad _secreto_ pero no quiero que aparezca en el resultado de `JSON.stringify(persona)`, defino _secreto_ como no enumerable.
```javascript
const persona = {
  nombre: 'Esteban Dido'
};
Object.defineProperty(persona, 'secreto', {
  value: 'Robo chicles del Oxxo',
  enumerable: false
});
JSON.stringify(persona); // '{"nombre":"Esteban Dido"}'
persona.secreto; // 'Robo chicles del Oxxo'
```

## Un último comentario
Hay muchos conceptos que los Programadores Senior deben dominar y estoy seguro de no conocer ni un cuarto de ellos. Saber usar un lenguaje no lo es todo, también hay que estar enterado de cosas como modularidad, buenas prácticas, principios SOLID, etc. A veces me siento algo tonto, pero a fin de cuentas es difícil ser un buen desarrollador. Para concluir este artículo, no puedo sino alentarlos a seguir ampliando su base de conocimiento.