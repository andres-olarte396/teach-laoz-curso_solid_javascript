# Cohesión y SRP

**Tiempo estimado**: 45 minutos
**Nivel**: Intermedio-Avanzado
**Prerrequisitos**: SRP básico, métricas de calidad

## ¿Por qué importa este concepto?

La **cohesión** es la métrica que mide cuán relacionados están los elementos dentro de un módulo. Es la forma cuantitativa de validar si una clase cumple con SRP.

**Relación directa**:
- **Alta cohesión** = SRP cumplido
- **Baja cohesión** = Violación de SRP

Medir cohesión te permite detectar violaciones de SRP objetivamente, sin depender de intuición.

## Definición formal

### Cohesión Funcional

**Definición**: Grado en que las tareas realizadas por un módulo están relacionadas funcionalmente.

**Niveles de cohesión** (de menor a mayor):

1. **Cohesión Coincidental** (peor): Elementos sin relación
2. **Cohesión Lógica**: Elementos realizan tareas similares pero no relacionadas
3. **Cohesión Temporal**: Elementos ejecutados al mismo tiempo
4. **Cohesión Procedural**: Elementos siguen una secuencia
5. **Cohesión Comunicacional**: Elementos operan sobre los mismos datos
6. **Cohesión Secuencial**: Output de uno es input de otro
7. **Cohesión Funcional** (mejor): Todos los elementos contribuyen a una única tarea

## Implementación práctica

### Ejemplo 1. Niveles de cohesión

```javascript
// ❌ COHESIÓN COINCIDENTAL (nivel 1 - peor)
class Utils {
  // Métodos sin relación entre sí
  calculateTax(amount) {
    return amount * 0.21;
  }

  sendEmail(to, subject, body) {
    // Enviar email
  }

  generateRandomId() {
    return Math.random().toString(36).substr(2, 9);
  }

  formatDate(date) {
    return date.toISOString();
  }
}

// Problema: No hay relación funcional entre los métodos
```

```javascript
// ⚠️ COHESIÓN LÓGICA (nivel 2)
class InputHandler {
  handle(type, data) {
    if (type === 'keyboard') {
      this.handleKeyboard(data);
    } else if (type === 'mouse') {
      this.handleMouse(data);
    } else if (type === 'touch') {
      this.handleTouch(data);
    }
  }

  handleKeyboard(data) { /* ... */ }
  handleMouse(data) { /* ... */ }
  handleTouch(data) { /* ... */ }
}

// Problema: Agrupa tareas similares pero no relacionadas
// Mejor: Clases separadas (KeyboardHandler, MouseHandler, TouchHandler)
```

```javascript
// ✅ COHESIÓN FUNCIONAL (nivel 7 - mejor)
class OrderTotalCalculator {
  constructor(taxRate, shippingCalculator) {
    this.taxRate = taxRate;
    this.shippingCalculator = shippingCalculator;
  }

  calculateSubtotal(items) {
    return items.reduce((sum, item) => {
      return sum + (item.price * item.quantity);
    }, 0);
  }

  calculateTax(subtotal) {
    return subtotal * this.taxRate;
  }

  calculateShipping(items, destination) {
    return this.shippingCalculator.calculate(items, destination);
  }

  calculateTotal(items, destination) {
    const subtotal = this.calculateSubtotal(items);
    const tax = this.calculateTax(subtotal);
    const shipping = this.calculateShipping(items, destination);

    return {
      subtotal,
      tax,
      shipping,
      total: subtotal + tax + shipping
    };
  }
}

// Todos los métodos contribuyen a UNA tarea: calcular el total de una orden
```

### Ejemplo 2: Midiendo cohesión con LCOM

**LCOM (Lack of Cohesion of Methods)**: Métrica que mide cohesión.

**Fórmula simplificada**:
```
LCOM = (P - Q) / (M * (M - 1) / 2)

P = Pares de métodos que NO comparten atributos
Q = Pares de métodos que SÍ comparten atributos
M = Número de métodos

Rango: 0 (perfecta cohesión) a 1 (sin cohesión)
```

```javascript
// Ejemplo de análisis LCOM
class Example {
  constructor() {
    this.a = 0;
    this.b = 0;
    this.c = 0;
  }

  method1() {
    return this.a + this.b;  // Usa: a, b
  }

  method2() {
    return this.a * 2;       // Usa: a
  }

  method3() {
    return this.c;           // Usa: c
  }
}

/*
Análisis de pares:
- method1-method2: Comparten 'a' → Q
- method1-method3: No comparten → P
- method2-method3: No comparten → P

P = 2, Q = 1, M = 3

LCOM = (2 - 1) / (3 * 2 / 2) = 1/3 = 0.33

Interpretación: Cohesión moderada (debería dividirse)
*/
```

**Implementación automática de cálculo LCOM**:

```javascript
class LCOMCalculator {
  calculate(classCode) {
    const methods = this.extractMethods(classCode);
    const attributes = this.extractAttributes(classCode);

    let P = 0; // No comparten
    let Q = 0; // Comparten

    // Comparar cada par de métodos
    for (let i = 0; i < methods.length; i++) {
      for (let j = i + 1; j < methods.length; j++) {
        const method1Attrs = this.getUsedAttributes(methods[i], attributes);
        const method2Attrs = this.getUsedAttributes(methods[j], attributes);

        const hasCommon = method1Attrs.some(attr =>
          method2Attrs.includes(attr)
        );

        if (hasCommon) {
          Q++;
        } else {
          P++;
        }
      }
    }

    const M = methods.length;
    const lcom = M > 1 ? (P - Q) / (M * (M - 1) / 2) : 0;

    return {
      lcom: Math.max(0, lcom), // LCOM no puede ser negativo
      P,
      Q,
      M,
      interpretation: this.interpret(lcom)
    };
  }

  interpret(lcom) {
    if (lcom === 0) return 'Perfecta cohesión';
    if (lcom < 0.3) return 'Alta cohesión';
    if (lcom < 0.7) return 'Cohesión moderada - considerar dividir';
    return 'Baja cohesión - dividir urgentemente';
  }

  // Métodos auxiliares (simplificados)
  extractMethods(code) {
    // Parse código y extraer métodos
    return [];
  }

  extractAttributes(code) {
    // Parse código y extraer atributos
    return [];
  }

  getUsedAttributes(method, attributes) {
    // Encontrar qué atributos usa el método
    return [];
  }
}
```

### Ejemplo 3: Refactoring guiado por cohesión

```javascript
// ❌ BAJA COHESIÓN (LCOM = 0.67)
class UserManager {
  constructor() {
    this.users = [];
    this.emailService = new EmailService();
    this.logger = new Logger();
  }

  // Grupo 1. Usa users
  addUser(user) {
    this.users.push(user);
  }

  findUser(id) {
    return this.users.find(u => u.id === id);
  }

  // Grupo 2: Usa emailService
  sendEmail(user, message) {
    this.emailService.send(user.email, message);
  }

  sendBulkEmail(users, message) {
    users.forEach(u => this.emailService.send(u.email, message));
  }

  // Grupo 3: Usa logger
  logAction(action) {
    this.logger.log(action);
  }

  logError(error) {
    this.logger.error(error);
  }
}

/*
Análisis LCOM:
- addUser - findUser: Comparten users → Q
- addUser - sendEmail: No comparten → P
- addUser - sendBulkEmail: No comparten → P
- addUser - logAction: No comparten → P
- addUser - logError: No comparten → P
- findUser - sendEmail: No comparten → P
... (15 pares totales)

P = 12, Q = 3, M = 6
LCOM = (12 - 3) / 15 = 0.6 (Cohesión moderada-baja)
*/
```

```javascript
// ✅ ALTA COHESIÓN (LCOM ≈ 0)
class UserRepository {
  constructor() {
    this.users = [];
  }

  add(user) {
    this.users.push(user);
    return user;
  }

  findById(id) {
    return this.users.find(u => u.id === id);
  }

  findByEmail(email) {
    return this.users.find(u => u.email === email);
  }

  getAll() {
    return [...this.users];
  }

  remove(id) {
    const index = this.users.findIndex(u => u.id === id);
    if (index > -1) {
      this.users.splice(index, 1);
      return true;
    }
    return false;
  }
}
// LCOM ≈ 0 (todos los métodos usan this.users)

class UserNotificationService {
  constructor(emailService) {
    this.emailService = emailService;
  }

  sendWelcome(user) {
    return this.emailService.send(user.email, 'Welcome!');
  }

  sendPasswordReset(user, token) {
    return this.emailService.send(user.email, `Reset: ${token}`);
  }

  sendBulk(users, message) {
    return Promise.all(
      users.map(u => this.emailService.send(u.email, message))
    );
  }
}
// LCOM ≈ 0 (todos los métodos usan this.emailService)

class ActivityLogger {
  constructor(logger) {
    this.logger = logger;
  }

  logUserAction(user, action) {
    this.logger.info(`User ${user.id}: ${action}`);
  }

  logSystemAction(action) {
    this.logger.info(`System: ${action}`);
  }

  logError(context, error) {
    this.logger.error(`Error in ${context}:`, error);
  }
}
// LCOM ≈ 0 (todos los métodos usan this.logger)
```

## Patrón: Extract Class basado en cohesión

```javascript
// Proceso sistemático de refactoring

// PASO 1. Identificar grupos de métodos cohesivos
class OrderService {
  // Grupo A: Validación (usan: validationRules)
  validateItems() { /* ... */ }
  validatePayment() { /* ... */ }

  // Grupo B: Cálculos (usan: taxRate, shippingRates)
  calculateSubtotal() { /* ... */ }
  calculateTax() { /* ... */ }
  calculateShipping() { /* ... */ }

  // Grupo C: Persistencia (usan: database)
  saveOrder() { /* ... */ }
  updateOrder() { /* ... */ }
}

// PASO 2: Extraer cada grupo a su propia clase
class OrderValidator {
  constructor(validationRules) {
    this.rules = validationRules;
  }

  validateItems(items) { /* ... */ }
  validatePayment(payment) { /* ... */ }
}

class OrderCalculator {
  constructor(taxRate, shippingRates) {
    this.taxRate = taxRate;
    this.shippingRates = shippingRates;
  }

  calculateSubtotal(items) { /* ... */ }
  calculateTax(subtotal) { /* ... */ }
  calculateShipping(items, destination) { /* ... */ }
}

class OrderRepository {
  constructor(database) {
    this.db = database;
  }

  save(order) { /* ... */ }
  update(order) { /* ... */ }
  findById(id) { /* ... */ }
}

// PASO 3: Orquestar con composición
class OrderService {
  constructor(validator, calculator, repository) {
    this.validator = validator;
    this.calculator = calculator;
    this.repository = repository;
  }

  async placeOrder(orderData) {
    this.validator.validateItems(orderData.items);
    this.validator.validatePayment(orderData.payment);

    const subtotal = this.calculator.calculateSubtotal(orderData.items);
    const tax = this.calculator.calculateTax(subtotal);
    const shipping = this.calculator.calculateShipping(
      orderData.items,
      orderData.destination
    );

    const order = new Order({
      ...orderData,
      subtotal,
      tax,
      shipping,
      total: subtotal + tax + shipping
    });

    return this.repository.save(order);
  }
}
```

## Testing de cohesión

```javascript
// Test que revela baja cohesión
describe('UserManager (low cohesion)', () => {
  let manager;

  beforeEach(() => {
    manager = new UserManager();
  });

  // ❌ Tests que requieren muchos mocks indican baja cohesión
  test('addUser requires minimal setup', () => {
    const user = { id: 1, name: 'Alice' };
    manager.addUser(user);
    expect(manager.users).toContain(user);
    // No necesita emailService ni logger
  });

  test('sendEmail requires email service mock', () => {
    const mockEmail = jest.fn();
    manager.emailService = { send: mockEmail };

    manager.sendEmail({ email: 'test@example.com' }, 'Hi');

    expect(mockEmail).toHaveBeenCalled();
    // No usa users ni logger
  });

  // SEÑAL: Cada test usa diferentes dependencias → baja cohesión
});

// ✅ Tests de clases cohesivas son simples
describe('UserRepository (high cohesion)', () => {
  let repository;

  beforeEach(() => {
    repository = new UserRepository();
  });

  // Todos los tests usan la misma estructura
  test('adds user', () => {
    const user = { id: 1, name: 'Alice' };
    repository.add(user);
    expect(repository.findById(1)).toEqual(user);
  });

  test('removes user', () => {
    const user = { id: 1, name: 'Alice' };
    repository.add(user);
    repository.remove(1);
    expect(repository.findById(1)).toBeUndefined();
  });

  // SEÑAL: Todos los tests usan repository.users → alta cohesión
});
```

## Herramientas para medir cohesión

### ESLint Plugin

```javascript
// .eslintrc.js
module.exports = {
  plugins: ['cohesion'],
  rules: {
    'cohesion/no-mixed-concerns': 'error',
    'cohesion/max-lcom': ['warn', { max: 0.5 }]
  }
};
```

### SonarQube

```javascript
// Configura reglas de cohesión
// sonar-project.properties
sonar.javascript.cohesion.threshold=0.7
```

## Resumen

**En una frase**: La cohesión mide cuán relacionados están los elementos dentro de un módulo; alta cohesión indica cumplimiento de SRP.

**Métrica clave**: LCOM (Lack of Cohesion of Methods)
- LCOM = 0: Perfecta cohesión
- LCOM < 0.3: Alta cohesión
- LCOM > 0.7: Baja cohesión (dividir clase)

**Señales de baja cohesión**:
- Tests que requieren mocks muy diferentes
- Métodos que usan conjuntos distintos de atributos
- Dificultad para nombrar la clase

**Prerequisito crítico**: Entender SRP y métricas de calidad

**Siguiente paso**: Violaciones comunes del SRP - patrones anti-pattern a evitar
