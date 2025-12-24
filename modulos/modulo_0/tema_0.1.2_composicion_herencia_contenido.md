# Composición vs Herencia en JavaScript

**Tiempo estimado**: 45 minutos
**Nivel**: Intermedio
**Prerrequisitos**: Clases, prototipos, herencia básica

## ¿Por qué importa este concepto?

El debate "Composición vs Herencia" es fundamental en diseño orientado a objetos. La famosa frase "Prefer composition over inheritance" (Gang of Four, Design Patterns) no significa que la herencia sea mala, sino que **la composición es más flexible** en la mayoría de casos.

En el contexto de SOLID:
- **Herencia** tiende a violar LSP (Liskov Substitution) y OCP (Open/Closed)
- **Composición** facilita DIP (Dependency Inversion) e ISP (Interface Segregation)

Frameworks modernos (React Hooks, Vue Composition API) están migrando de herencia a composición.

## Comprensión intuitiva

### Analogía: Construyendo un robot

**Herencia** (enfoque tradicional):
```
Robot
  ├── FlyingRobot
  │     ├── ArmedFlyingRobot
  │     └── UnarmedFlyingRobot
  └── WalkingRobot
        ├── ArmedWalkingRobot
        └── UnarmedWalkingRobot
```

**Problema**: ¿Qué pasa si quieres un robot que vuele Y camine? Herencia múltiple es problemática en JavaScript.

**Composición** (enfoque flexible):
```javascript
const robot = compose(
  canFly(),
  canWalk(),
  hasWeapon(),
  hasAI()
);
```

Cada habilidad es un **componente independiente** que se ensambla.

## Definición formal

### Herencia

```javascript
class Parent {
  parentMethod() {
    // lógica compartida
  }
}

class Child extends Parent {
  childMethod() {
    // lógica específica
  }
}
```

**Relación**: "is-a" (Child IS-A Parent)
**Acoplamiento**: Fuerte (Child depende de Parent)
**Flexibilidad**: Baja (jerarquía fija en tiempo de diseño)

### Composición

```javascript
class Component {
  constructor(behaviors) {
    Object.assign(this, ...behaviors);
  }
}

const flying = {
  fly() { console.log('Flying'); }
};

const walking = {
  walk() { console.log('Walking'); }
};

const robot = new Component([flying, walking]);
```

**Relación**: "has-a" (Robot HAS-A flying ability)
**Acoplamiento**: Bajo (componentes independientes)
**Flexibilidad**: Alta (combinación en tiempo de ejecución)

## Implementación práctica

### Ejemplo 1. Problema con Herencia (God Object)

```javascript
// ❌ Problema: Jerarquía rígida
class Employee {
  constructor(name, salary) {
    this.name = name;
    this.salary = salary;
  }

  work() {
    console.log(`${this.name} is working`);
  }

  calculatePay() {
    return this.salary;
  }
}

class Manager extends Employee {
  constructor(name, salary, team) {
    super(name, salary);
    this.team = team;
  }

  manage() {
    console.log(`${this.name} is managing team`);
  }

  calculatePay() {
    return this.salary * 1.5; // Bonus
  }
}

class Developer extends Employee {
  constructor(name, salary, language) {
    super(name, salary);
    this.language = language;
  }

  code() {
    console.log(`${this.name} codes in ${this.language}`);
  }
}

// 🚨 ¿Qué pasa con un Developer que es Manager?
// No podemos heredar de ambos en JavaScript
```

### Ejemplo 2: Solución con Composición

```javascript
// ✅ Solución: Comportamientos componibles

// Componentes de comportamiento
const canWork = (state) => ({
  work() {
    console.log(`${state.name} is working`);
  }
});

const canManage = (state) => ({
  manage() {
    console.log(`${state.name} manages team: ${state.team.join(', ')}`);
  },

  assignTask(task, member) {
    console.log(`${state.name} assigned "${task}" to ${member}`);
  }
});

const canCode = (state) => ({
  code() {
    console.log(`${state.name} codes in ${state.language}`);
  },

  review(pr) {
    console.log(`${state.name} reviewed PR #${pr}`);
  }
});

const calculatePay = (state) => ({
  getPay() {
    const base = state.salary;
    const bonus = state.isManager ? base * 0.5 : 0;
    return base + bonus;
  }
});

// Factory function para crear empleados
function createEmployee(name, salary, config = {}) {
  const state = {
    name,
    salary,
    ...config
  };

  const behaviors = [
    canWork(state),
    calculatePay(state)
  ];

  if (config.team) {
    state.isManager = true;
    behaviors.push(canManage(state));
  }

  if (config.language) {
    behaviors.push(canCode(state));
  }

  return Object.assign({}, ...behaviors, {
    getInfo() {
      return `${state.name} - Salary: $${this.getPay()}`;
    }
  });
}

// Uso flexible
const alice = createEmployee('Alice', 80000, { language: 'JavaScript' });
alice.work();     // "Alice is working"
alice.code();     // "Alice codes in JavaScript"

const bob = createEmployee('Bob', 100000, { team: ['Alice', 'Charlie'] });
bob.work();       // "Bob is working"
bob.manage();     // "Bob manages team: Alice, Charlie"

// ¡El poder de la composición!
const charlie = createEmployee('Charlie', 120000, {
  language: 'TypeScript',
  team: ['Dave', 'Eve']
});
charlie.code();   // "Charlie codes in TypeScript"
charlie.manage(); // "Charlie manages team: Dave, Eve"
console.log(charlie.getInfo()); // "Charlie - Salary: $180000"
```

### Ejemplo 3: Mixins (patrón híbrido)

```javascript
// Mixin: función que agrega comportamiento a una clase
const TimestampMixin = (Base) => class extends Base {
  constructor(...args) {
    super(...args);
    this.createdAt = new Date();
    this.updatedAt = new Date();
  }

  touch() {
    this.updatedAt = new Date();
  }

  getAge() {
    return Date.now() - this.createdAt.getTime();
  }
};

const ValidationMixin = (Base) => class extends Base {
  validate() {
    for (const [key, value] of Object.entries(this)) {
      if (value === undefined || value === null) {
        throw new Error(`${key} is required`);
      }
    }
    return true;
  }
};

// Aplicación de múltiples mixins
class User {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }
}

const EnhancedUser = ValidationMixin(TimestampMixin(User));

const user = new EnhancedUser('Alice', 'alice@example.com');
console.log(user.getAge()); // Milliseconds since creation
user.validate(); // true
user.touch(); // Update timestamp
```

### Casos de prueba

```javascript
// Test composición
const emp1 = createEmployee('Test', 50000, { language: 'Python' });
console.assert(typeof emp1.code === 'function', 'Should have code method');
console.assert(typeof emp1.manage === 'undefined', 'Should NOT have manage method');
console.assert(emp1.getPay() === 50000, 'Pay should be 50000');

const emp2 = createEmployee('Manager', 80000, { team: ['A', 'B'] });
console.assert(typeof emp2.manage === 'function', 'Should have manage method');
console.assert(emp2.getPay() === 120000, 'Pay with bonus should be 120000');

console.log('✓ Composition tests passed');
```

## Cuándo usar cada enfoque

| Criterio | Herencia | Composición |
|----------|----------|-------------|
| Relación "is-a" clara | ✅ Apropiado | ⚠️ Funciona pero no ideal |
| Relación "has-a" | ❌ Forzado | ✅ Natural |
| Jerarquía profunda (>3 niveles) | ❌ Frágil | ✅ Escalable |
| Comportamiento dinámico | ❌ Difícil | ✅ Fácil |
| Polimorfismo | ✅ Nativo | ⚠️ Requiere interfaces |
| Testing | ⚠️ Difícil (dependencias) | ✅ Fácil (componentes aislados) |
| Performance | ✅ Lookup rápido | ⚠️ Ligeramente más lento |

**Regla de oro**: Si dudas, usa composición. Es más fácil cambiar composición a herencia que viceversa.

## Errores frecuentes

### ❌ Error 1. Herencia para reutilización de código

```javascript
// Mal uso de herencia
class Utils {
  log(message) {
    console.log(`[LOG] ${message}`);
  }

  formatDate(date) {
    return date.toISOString();
  }
}

// ❌ No tiene sentido semántico
class User extends Utils {
  constructor(name) {
    super();
    this.name = name;
  }

  register() {
    this.log(`User ${this.name} registered`); // Funciona pero está mal
  }
}
```

**Problema**: User NO ES UN Utils. Esto rompe el principio LSP.

**Solución**:

```javascript
// ✅ Composición
class Logger {
  log(message) {
    console.log(`[LOG] ${message}`);
  }
}

class User {
  constructor(name, logger = new Logger()) {
    this.name = name;
    this.logger = logger;
  }

  register() {
    this.logger.log(`User ${this.name} registered`);
  }
}
```

### ❌ Error 2: Jerarquía frágil (Fragile Base Class)

```javascript
class Animal {
  constructor() {
    this.energy = 100;
  }

  move() {
    this.energy -= 10;
  }
}

class Bird extends Animal {
  fly() {
    this.move();  // Depende de implementación de parent
    this.move();  // Volar gasta el doble
  }
}

// 🚨 Si Animal.move() cambia, Bird.fly() se rompe
class Animal {
  move() {
    this.energy -= 20; // Cambio interno
  }
}

const bird = new Bird();
bird.fly(); // Ahora gasta 40 en lugar de 20
```

## Aplicaciones reales

### React: De clases a Hooks (Composición)

```javascript
// ❌ Antes (Herencia)
class MyComponent extends React.Component {
  componentDidMount() {
    // setup
  }

  render() {
    return <div>{this.state.data}</div>;
  }
}

// ✅ Ahora (Composición con Hooks)
function MyComponent() {
  const data = useFetch('/api/data');
  const theme = useTheme();
  const auth = useAuth();

  return <div style={theme}>{data}</div>;
}
```

## Resumen

**Herencia**: Útil para relaciones "is-a" claras y estables. Usar con moderación.

**Composición**: Preferible en la mayoría de casos. Mayor flexibilidad y testabilidad.

**Mixins**: Compromiso útil cuando necesitas agregar comportamiento a clases existentes.

**Prerequisito crítico**: Entender objetos, prototipos y functions

**Siguiente paso**: Testing unitario para validar que nuestros diseños funcionan correctamente
