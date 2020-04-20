# Conceptos de JavaScript que me hubiera gustado aprender en la escuela
No me malentiendan, aprendí mucho en la facultad. Desarrollo de Aplicaciones Web fue una materia de la que muchos compañeros se enamoraron, sin ella no sabría jugar con el DOM, qué es Ajax, fundamentos de php, etc.

Es de esperar que en un curso no intensivo de 6 meses haya poco espacio para adentrarse en detalles y truquitos de cada lenguaje. El lado positivo es que todavía se cuenta con el resto de una vida para aprenderlos. Yo he sido muy afortunado, pues tener buenos compañeros de trabajo ayuda dramáticamente.

### No es necesario sumar cadenas
Bueno, este apartado será corto. Dudaba si designarle todo un subtítulo a las **Template literals** (o template strings), pero no podía dejar de lado a uno de mis trucos favoritos de ES6+. Estas dos líneas son equivalentes:
```javascript
'Bienvenido ' + nombre + ', son las ' + (new Date().getHours() + 1) + ' horas';

`Bienvenido ${nombre}, son las ${new Date().getHours() + 1} horas`;
```
Al usar acentos graves `` ` `` se pueden agregar expresiones dentro de un `${}`, haciendo más bonita la concatenación de cadenas y expresiones.

### ¿A dónde apuntan esas flechas?
La primera vez que vi un símbolo de "igual" junto al "mayor que" creí que era un operador lógico. Leía y releía los `=>` pero nada cobraba sentido. Entonces lo encontré, aquel video de YouTube que decía _eso es una función flecha_. Desde ese entonces no he parado de usar tan conveniente notación.
#### Sintaxis
La sintaxis (simplificada) de una función anónima es `function(<argumentos>) {<cuerpo>}`. La sintaxis equivalente para la función flecha es `(<argumentos>) => {<cuerpo>}`, pero la segunda tiene un poquito más de azúcar.
+ **Paréntesis opcionales:** Si la función flecha recibe un solo argumento que no [se desestructura](https://developer.mozilla.org/es/docs/Web/JavaScript/Referencia/Operadores/Destructuring_assignment "Asignación Desestructurante - JavaScript | MDN"), se pueden omitir los paréntesis del argumento. Es decir, `(<argumento>) => {<cuerpo>}` equivale a `<argumento> => {<cuerpo>}`.
+ **Retorno implícito:** Si el cuerpo la función flecha solo consta de retornar una expresión, eliminar los corchetes hará que el `return` sea implícito. Es decir, `(<argumentos>) => {return <expresión>}` equivale a `(<argumentos>) => <expresión>`.

Por ejemplo: se tiene un arreglo con números y se desea saber cuáles son mayores a 3. Usemos al método `Array.prototype.filter()`. Por si no habías escuchado de él, _crea un nuevo array con todos los elementos que cumplan la condición implementada por la función dada_ (Copypasteado de [MDN](https://developer.mozilla.org/es/docs/Web/JavaScript/Referencia/Objetos_globales/Array/filter "Array.prototype.filter() - JavaScript | MDN")).
```javascript
const numbers = [5, 8, 1, 0, 10, -4];

const usingFunction = numbers.filter(function(number){
  return number > 3
});

const usingArrow = numbers.filter(number => number > 3);

// Tanto usingFunction como usingArrow serán [5, 8, 10]
```

#### Más que un sustituto de `function`
Voy a plantear un problema antes de decir dónde entran las `=>`. Dentro de un método de un objeto se quiere llamar a una promesa y se desea almacenar, en el mismo objeto, algún valor que diga si la promesa se cumplió o no. Por motivos de simplicidad, la promesa en cuestión se llamará _somePromise_ y no diremos qué hace.
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
Uno podría pensar que el valor de `obj.promiseSuccessful` sea `true` si la promesa es exitosa, pero [la manera en que `this` funciona](https://medium.com/better-programming/understanding-the-this-keyword-in-javascript-cb76d4c7c5e8 "Understanding the “this” Keyword in JavaScript - Better Programming - Medium") hace que este no sea el caso, porque el `this` dentro de la función anónima apunta a un _this global_ en lugar de a `obj`.

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
Si al crear un objeto se usan funciones flecha para declarar sus métodos, el `this` de tales métodos no va a apuntar al objeto, sino al `this` del lugar donde se creó. Por ejemplo: las siguientes líneas tienen un problema similar al que teníamos inicialmente.
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
Debido al uso de `=>` al declarar _someMethod_, el `this` hace referencia al lugar donde se creó `obj`, no al mismo `obj`.

<p align="center">
  <img src="assets/images/This-is-complicated-2.jpeg" width="350">
</p>

### Expandiendo la idea de clave-valor
Cuando empecé a programar en JavaScript me llamó mucho la atención que los objetos aquí no son instancias de una clase. Los veía como sacos que permiten agregar y quitar cosas a lo desgraciado y sin privacidad. Sigo viéndolos de esa forma, pero ahora sé que pueden tener un par de reglas más.

Este apartado no es para hablar de los objetos en sí, sino una parte fundamental de ellos: las propiedades.
```javascript
const persona = {
  nombre: 'Esteban Dido'
};
```
Viendo el objeto anterior, podemos dar por hecho una serie de características sobre _nombre_:
+ Guarda un valor (`value`). En este caso, el valor es _'Esteban Dido'_
+ Se le puede asignar otro valor (`writable`).
+ Se puede configurar (`configurable`): Nada impide que más adelante alguien elimine _nombre_ o modifique sus descriptores (en breve diremos qué es eso).
+ Aparece al enumerar las propiedades (`enumerable`): Esto quiere decir que los métodos que usen las propiedades de _persona_, como [JSON.stringify()](https://developer.mozilla.org/es/docs/Web/JavaScript/Referencia/Objetos_globales/JSON/stringify "JSON.stringify() - JavaScript | MDN") u [Object.keys()](https://developer.mozilla.org/es/docs/Web/JavaScript/Referencia/Objetos_globales/Object/keys "Object.keys() - JavaScript | MDN"), también incluyen a _nombre_.

Estas características [y un par más](https://www.jackfranklin.co.uk/blog/es5-getters-setters/ "JavaScript Getters and Setters - Jack Franklin") son los **_descriptores_** de una propiedad. La forma de modificar los descriptores de una propiedad es con `Object.defineProperty()`, un método muy especial que recibe 3 argumentos:
1. El objeto al cual se le va a agregar o configurar la propiedad.
2. La clave de la propiedad.
3. Un objeto con sus descriptores.

```javascript
const persona = {};
Object.defineProperty(
  persona, // Objeto a modificar
  'nombre', // Clave de la propiedad
  { // Descriptores
    value: 'Esteban Dido',
    writable: true,
    configurable: true,
    enumerable: true
  }
);
```
#### Propiedades semi ocultas
Lo que me inspiró a hablar de descriptores en este artículo fue `enumerable`, pues me parece el más interesante de todos.

Si quiero que _persona_ tenga la propiedad _secreto_ pero no quiero que esa propiedad aparezca en el resultado de `JSON.stringify(persona)`, puedo definir _secreto_ como no enumerable.
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
Anécdota: En una ocasión quería guardar (en formato _JSON_) un arreglo de objetos, pero cierta dependencia requería que esos objetos tengan un valor que no me interesaba almacenar. El mayor problema era que los objetos tenían estructuras diferentes, así que no podía simplemente eliminar ese valor. Después de fallar la implementación de una solución complicada le pedí ayuda a un amigo, quien me habló de `Object.defineProperty()`. No podía creer que sea posible configurar una propiedad.

## Un último comentario
Hay muchos conceptos que los Programadores Senior deben dominar y estoy seguro de no conocer ni un cuarto de ellos. Saber usar un lenguaje no lo es todo, también hay que estar enterado de cosas como modularidad, buenas prácticas, principios SOLID, etc. A veces me siento algo tonto, pero a fin de cuentas es difícil ser un buen desarrollador. Para concluir este artículo, no puedo sino alentarlos a seguir ampliando su base de conocimiento.