# Clases y Prototipos en JavaScript

**Tiempo estimado**: 45 minutos
**Nivel**: Básico-Intermedio
**Prerrequisitos**: Conocimientos de JavaScript básico (funciones, objetos)

## ¿Por qué importa este concepto?

JavaScript es un lenguaje con herencia basada en prototipos, no en clases tradicionales (como Java o C++). Aunque ES6 introdujo la sintaxis `class`, bajo el capó sigue utilizando prototipos. Comprender esta diferencia es fundamental para:

1. **Escribir código SOLID**: Los principios SOLID asumen comprensión de POO, pero JavaScript la implementa de manera única
2. **Evitar errores sutiles**: El comportamiento de `this`, herencia, y métodos difiere de lenguajes clásicos
3. **Optimizar rendimiento**: Saber cómo funcionan los prototipos permite diseñar jerarquías eficientes

En aplicaciones reales, esta comprensión es crítica cuando trabajas con frameworks (React, Vue, Angular) o diseñas arquitecturas escalables.

## Conexión con conocimientos previos

Si has trabajado con objetos JavaScript (`const obj = {}`), ya conoces la sintaxis básica. Ahora vamos a formalizar cómo se crean "plantillas" reutilizables de objetos y cómo se relacionan entre sí.

## Comprensión intuitiva

### La diferencia fundamental: Clases vs Prototipos

**Analogía**: Piensa en una clase tradicional (Java/C++) como un **plano arquitectónico**. Creas instancias (casas) a partir del plano. El plano no cambia una vez que las casas están construidas.

En JavaScript, los prototipos son más como **una casa modelo en exhibición**. Todas las "copias" de la casa **siguen apuntando a la casa modelo**. Si modificas la casa modelo (agregas una ventana), todas las copias la reflejan automáticamente.

### Ejemplo motivador

```javascript
// Problema: Crear múltiples usuarios sin repetir código

// ❌ Enfoque ingenuo (repetitivo)
const user1 = {
  name: 'Alice',
  email: 'alice@example.com',
  login: function() {
    console.log(`${this.name} logged in`);
  }
};

const user2 = {
  name: 'Bob',
  email: 'bob@example.com',
  login: function() {
    console.log(`${this.name} logged in`);
  }
};
// Problema: El método login se duplica en memoria

// ✅ Solución con prototipos (eficiente)
function User(name, email) {
  this.name = name;
  this.email = email;
}

User.prototype.login = function() {
  console.log(`${this.name} logged in`);
};

const user1 = new User('Alice', 'alice@example.com');
const user2 = new User('Bob', 'bob@example.com');
// Ambos comparten el mismo método login en memoria
```

## Definición formal

### Funciones Constructoras (pre-ES6)

```javascript
function Constructor(param1, param2) {
  // Propiedades de instancia
  this.property1 = param1;
  this.property2 = param2;
}

// Métodos en el prototipo
Constructor.prototype.method1 = function() {
  // Lógica del método
};
```

### Clases ES6 (syntactic sugar)

```javascript
class ClassName {
  constructor(param1, param2) {
    this.property1 = param1;
    this.property2 = param2;
  }

  method1() {
    // Lógica del método
  }

  // Método estático
  static staticMethod() {
    // No requiere instancia
  }
}
```

### Propiedades fundamentales

1. **Cadena de prototipos**: Todo objeto en JavaScript tiene una propiedad interna `[[Prototype]]` (accesible vía `__proto__` o `Object.getPrototypeOf()`)
2. **Lookup de propiedades**: Si una propiedad no existe en un objeto, JavaScript busca en su prototipo, luego en el prototipo del prototipo, hasta `null`
3. **Constructor property**: Todo prototipo tiene una propiedad `constructor` que apunta a la función que lo creó
4. **`new` operator**: Crea un nuevo objeto, asigna su `[[Prototype]]`, ejecuta el constructor con `this` vinculado

## Implementación práctica

### Pseudocódigo

```
FUNCIÓN CrearObjeto(Constructor, argumentos):
    1. Crear nuevo objeto vacío: obj = {}
    2. Establecer prototipo: obj.[[Prototype]] = Constructor.prototype
    3. Ejecutar constructor: Constructor.apply(obj, argumentos)
    4. Si constructor retorna objeto, usar ese objeto, sino retornar obj
```

### Implementación en JavaScript

#### Ejemplo 1: Función Constructora

```javascript
// Constructor
function Animal(name, species) {
  // Validación de entrada
  if (!name || !species) {
    throw new Error('Name and species are required');
  }

  // Propiedades de instancia
  this.name = name;
  this.species = species;
  this.energy = 100;
}

// Métodos en el prototipo (compartidos entre instancias)
Animal.prototype.eat = function(food) {
  console.log(`${this.name} is eating ${food}`);
  this.energy += 10;
  return this; // Fluent interface
};

Animal.prototype.sleep = function(hours) {
  console.log(`${this.name} is sleeping for ${hours} hours`);
  this.energy += hours * 5;
  return this;
};

Animal.prototype.getStatus = function() {
  return `${this.name} (${this.species}): Energy ${this.energy}`;
};

// Uso
const dog = new Animal('Rex', 'Dog');
const cat = new Animal('Whiskers', 'Cat');

dog.eat('bone').sleep(8); // Chaining
console.log(dog.getStatus()); // "Rex (Dog): Energy 150"

// Verificación de prototipos
console.log(dog.__proto__ === Animal.prototype); // true
console.log(dog.constructor === Animal); // true
console.log(Object.getPrototypeOf(dog) === Animal.prototype); // true
```

#### Ejemplo 2: Clase ES6

```javascript
class Vehicle {
  // Campos privados (ES2022)
  #mileage = 0;

  constructor(brand, model, year) {
    this.brand = brand;
    this.model = model;
    this.year = year;
  }

  // Método de instancia
  drive(distance) {
    this.#mileage += distance;
    console.log(`${this.brand} ${this.model} drove ${distance}km`);
  }

  // Getter
  get mileage() {
    return this.#mileage;
  }

  // Setter
  set mileage(value) {
    if (value < 0) {
      throw new Error('Mileage cannot be negative');
    }
    this.#mileage = value;
  }

  // Método estático
  static compare(v1, v2) {
    return v1.year - v2.year;
  }

  // Método para mostrar info
  toString() {
    return `${this.year} ${this.brand} ${this.model} (${this.#mileage}km)`;
  }
}

// Uso
const car1 = new Vehicle('Toyota', 'Corolla', 2020);
const car2 = new Vehicle('Honda', 'Civic', 2022);

car1.drive(150);
console.log(car1.mileage); // 150 (getter)
console.log(car1.toString()); // "2020 Toyota Corolla (150km)"

// Método estático
console.log(Vehicle.compare(car1, car2)); // -2 (car1 es más antiguo)

// Error al intentar acceder campo privado
// console.log(car1.#mileage); // SyntaxError
```

### Casos de prueba

```javascript
// Test 1: Crear instancias
const v1 = new Vehicle('Tesla', 'Model 3', 2023);
console.assert(v1.brand === 'Tesla', 'Brand should be Tesla');
console.assert(v1.year === 2023, 'Year should be 2023');

// Test 2: Métodos funcionan
v1.drive(100);
console.assert(v1.mileage === 100, 'Mileage should be 100');

// Test 3: Setter con validación
try {
  v1.mileage = -50;
  console.assert(false, 'Should throw error for negative mileage');
} catch (error) {
  console.assert(error.message.includes('negative'), 'Error message should mention negative');
}

// Test 4: Método estático
const v2 = new Vehicle('Ford', 'Mustang', 2021);
console.assert(Vehicle.compare(v1, v2) === 2, 'Compare should return 2');

// Test 5: Prototipos compartidos
const v3 = new Vehicle('BMW', 'X5', 2023);
console.assert(
  Object.getPrototypeOf(v1) === Object.getPrototypeOf(v3),
  'Instances should share prototype'
);

console.log('✓ All tests passed');
```

### Análisis de complejidad

**Creación de instancia**:
- Tiempo: O(1) - operación constante
- Espacio: O(n) donde n es el número de propiedades de instancia

**Lookup de método**:
- Tiempo: O(d) donde d es la profundidad de la cadena de prototipos (típicamente O(1) en diseños bien hechos)
- Espacio: O(1) - los métodos se comparten en el prototipo

## Variantes y optimizaciones

### Variante 1: Object.create() (creación explícita de prototipos)

```javascript
const animalMethods = {
  eat(food) {
    console.log(`${this.name} eats ${food}`);
  },
  sleep(hours) {
    this.energy += hours * 5;
  }
};

function createAnimal(name, species) {
  const animal = Object.create(animalMethods);
  animal.name = name;
  animal.species = species;
  animal.energy = 100;
  return animal;
}

const lion = createAnimal('Simba', 'Lion');
lion.eat('meat'); // "Simba eats meat"
```

**Cuándo usar**: Cuando necesitas control fino sobre la cadena de prototipos o quieres evitar `new`.

### Variante 2: Factory functions (sin prototipos)

```javascript
function createUser(name, email) {
  let _password = ''; // Privado vía closure

  return {
    name,
    email,
    setPassword(pwd) {
      _password = pwd;
    },
    validatePassword(pwd) {
      return _password === pwd;
    }
  };
}

const user = createUser('Alice', 'alice@example.com');
user.setPassword('secret123');
console.log(user.validatePassword('secret123')); // true
// No hay acceso a _password desde fuera
```

**Cuándo usar**: Cuando necesitas privacidad real (pre-ES2022) o cuando la herencia no es necesaria.

## Errores frecuentes

### ❌ Error 1: Olvidar `new` al crear instancias

```javascript
function Person(name) {
  this.name = name;
}

// Incorrecto
const alice = Person('Alice');
console.log(alice); // undefined
console.log(window.name); // 'Alice' (¡contaminación global!)
```

**Por qué falla**: Sin `new`, `this` apunta al objeto global (window/global), no a una nueva instancia.

### ✅ Solución correcta

```javascript
// Opción 1: Usar new
const alice = new Person('Alice');

// Opción 2: Hacer el constructor seguro
function Person(name) {
  if (!(this instanceof Person)) {
    return new Person(name);
  }
  this.name = name;
}

// Opción 3: Usar class (lanza error sin new)
class Person {
  constructor(name) {
    this.name = name;
  }
}
```

### ❌ Error 2: Métodos en el constructor (ineficiente)

```javascript
function User(name) {
  this.name = name;

  // Cada instancia crea una NUEVA función
  this.greet = function() {
    console.log(`Hi, I'm ${this.name}`);
  };
}

const u1 = new User('Alice');
const u2 = new User('Bob');
console.log(u1.greet === u2.greet); // false (desperdicio de memoria)
```

**Por qué es problema**: Con 1000 usuarios, tienes 1000 copias de `greet` en memoria.

### ✅ Solución correcta

```javascript
function User(name) {
  this.name = name;
}

// Un solo método compartido
User.prototype.greet = function() {
  console.log(`Hi, I'm ${this.name}`);
};

const u1 = new User('Alice');
const u2 = new User('Bob');
console.log(u1.greet === u2.greet); // true (eficiente)
```

### ❌ Error 3: Modificar `__proto__` directamente

```javascript
const obj = {};
obj.__proto__ = somePrototype; // ⚠️ Deprecated, lento
```

**Solución**:

```javascript
const obj = Object.create(somePrototype); // ✅ Forma correcta
// O
Object.setPrototypeOf(obj, somePrototype); // ✅ Pero lento, usar con cuidado
```

## Visualización del concepto

```mermaid
graph TD
    A[dog instance] -->|[[Prototype]]| B[Animal.prototype]
    C[cat instance] -->|[[Prototype]]| B
    B -->|[[Prototype]]| D[Object.prototype]
    D -->|[[Prototype]]| E[null]

    B -.contains.- F[eat method]
    B -.contains.- G[sleep method]
    B -.contains.- H[constructor: Animal]

    A -.own properties.- I[name: 'Rex']
    A -.own properties.- J[species: 'Dog']
```

**Explicación del diagrama**:
- `dog` y `cat` son instancias que apuntan al mismo prototipo `Animal.prototype`
- Los métodos `eat`, `sleep` están en el prototipo (compartidos)
- Las propiedades `name`, `species` son propias de cada instancia
- La cadena termina en `null`

## Aplicaciones reales

### Aplicación 1: Frameworks de UI (React, Vue)

**Contexto**: Los componentes de clase en React extienden `React.Component`

```javascript
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  render() {
    return <div>{this.state.count}</div>;
  }
}
```

**Solución**: La herencia prototípica permite que `MyComponent` acceda a `setState`, `componentDidMount`, etc. del prototipo de `React.Component`.

### Aplicación 2: ORMs (Sequelize, TypeORM)

**Contexto**: Modelos de base de datos que necesitan métodos como `save()`, `delete()`

```javascript
class User extends Model {
  async promote() {
    this.role = 'admin';
    return this.save(); // Método heredado de Model
  }
}

const user = await User.findOne({ where: { id: 1 } });
await user.promote();
```

**Escala**: En aplicaciones con millones de registros, usar prototipos en lugar de copiar métodos ahorra gigabytes de memoria.

## ¿Cuándo usar este enfoque?

| Escenario                           | Función Constructora | Clase ES6 | Factory Function |
| ----------------------------------- | -------------------- | --------- | ---------------- |
| Herencia multinivel                 | ❌ Verboso            | ✅ Clara   | ❌ Manual         |
| Privacidad de datos (pre-ES2022)    | ❌ No soporta         | ❌ No soporta | ✅ Closures       |
| Campos privados (ES2022)            | ❌ No soporta         | ✅ # fields | ❌ No aplica      |
| Compartir métodos eficientemente    | ✅ prototype          | ✅ (auto)  | ❌ Duplica        |
| Compatibilidad con `new`            | ⚠️ Frágil            | ✅ Segura  | ❌ No usa new     |
| TypeScript/IDEs autocompletado      | ⚠️ Básico            | ✅ Excelente | ⚠️ Básico        |

**Regla de decisión**:
- **Usa clases ES6** para código moderno con herencia
- **Usa factory functions** para objetos simples sin herencia y privacidad real
- **Evita constructoras** a menos que mantengas código legacy

## Para ir más allá

### Papers fundamentales

1. **Douglas Crockford (2008)** - "JavaScript: The Good Parts" - Capítulo sobre prototipos y herencia
   - Explica los patrones prototípicos antes de ES6

2. **Brendan Eich (2006)** - "Popularity of Prototype-Based Inheritance"
   - Perspectiva histórica del creador de JavaScript

### Implementaciones de referencia

- **MDN Web Docs**: [Herencia y cadena de prototipos](https://developer.mozilla.org/es/docs/Web/JavaScript/Inheritance_and_the_prototype_chain) - Referencia definitiva
- **ECMAScript Spec**: [Constructor y [[Prototype]]](https://tc39.es/ecma262/#sec-ordinary-object-internal-methods-and-internal-slots-construct-argumentslist-newtarget) - Especificación formal

### Preguntas abiertas en investigación

- **Rendimiento de prototipos vs closures**: ¿Cuál es más eficiente en engines modernos (V8, SpiderMonkey)?
- **Evolución de privacidad**: ¿Los campos privados `#` reemplazarán completamente las closures?

## Resumen del concepto

**En una frase**: Las clases en JavaScript son azúcar sintáctico sobre herencia prototípica, donde los objetos delegan propiedades a prototipos compartidos.

**Cuándo usarlo**: Siempre que necesites crear múltiples objetos con comportamiento compartido (métodos) pero datos únicos (propiedades).

**Complejidad**: Tiempo O(1) para creación | Espacio O(1) para métodos (compartidos)

**Prerequisito crítico**: Comprensión de `this`, objetos, y funciones en JavaScript

**Siguiente paso**: Composición vs Herencia - cuando delegar a prototipos no es la mejor solución
