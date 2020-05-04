## Parámetros

#### Distinción entre parámetro y argumento
Un argumento es un valor que se le manda a la función cuando es invocada, mientras que los parámetros de una función son las variables a las que accede.
```javascript
function something(parameter) {
  console.log(`The value of the parameter is: ${parameter}`);
}
const argument = 'some value';
something(argument);
```

### Tipos de parámetros
Existen 3 tipos de parámetros:
+ **Explícitos:** Aquellos que forman parte de la declaración de la función. Le permiten a la función recibir argumentos.
  - _Cuando no coincide el número de argumentos con los parámetros explícitos:_
+ **Implícitos:** Parámetros accedidos por medio de `this`.
  - _Cambiar los parámetros implícitos:_
+ **No locales:** Son las variables que pueden ser accedidas desde el lugar donde se declaró la función.
```javascript
const nonLocal = 'Parámetro no local';
const object = {
  implicitParameter: 'Parámetro implícito',
  someFunction(explicitParameter) {
    // Puede acceder a explicitParameter, implicitParameter y nonLocal
  }
}
```
