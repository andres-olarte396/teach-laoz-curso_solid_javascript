# Evaluación Diagnóstica - Módulo 0

**Tiempo estimado**: 90 minutos
**Nivel**: Diagnóstico
**Objetivo**: Validar que dominas los prerrequisitos antes de iniciar SOLID

## Instrucciones

Esta evaluación mide tu nivel actual en los conceptos fundamentales necesarios para el curso SOLID. **No es una evaluación con nota**, sino una herramienta de auto-diagnóstico.

**Criterios de aprobación**:
- **70% o más**: Estás listo para el curso
- **50-69%**: Revisa los temas marcados antes de continuar
- **Menos de 50%**: Refuerza los fundamentos del Módulo 0

**Reglas**:
- Sin consultar documentación externa
- Usa Node.js v18+ o navegador moderno
- Implementa soluciones reales (no pseudocódigo)

---

## PARTE 1: POO en JavaScript (30 puntos)

### Ejercicio 1.1: Clases y Prototipos (10 puntos)

Implementa una clase `BankAccount` con las siguientes especificaciones:

```javascript
class BankAccount {
  // TODO: Implementar

  /**
   * Constructor
   * @param {string} accountNumber - Número de cuenta
   * @param {number} initialBalance - Balance inicial (por defecto 0)
   */

  /**
   * Deposita dinero en la cuenta
   * @param {number} amount
   * @throws {Error} Si amount es negativo o cero
   */
  deposit(amount) {
    // TODO
  }

  /**
   * Retira dinero de la cuenta
   * @param {number} amount
   * @throws {Error} Si amount > balance o amount <= 0
   */
  withdraw(amount) {
    // TODO
  }

  /**
   * Retorna el balance actual
   * @returns {number}
   */
  getBalance() {
    // TODO
  }

  /**
   * Transfiere dinero a otra cuenta
   * @param {BankAccount} targetAccount
   * @param {number} amount
   */
  transfer(targetAccount, amount) {
    // TODO
  }
}

// Tests
const account1 = new BankAccount('001', 1000);
const account2 = new BankAccount('002', 500);

account1.deposit(500);
console.assert(account1.getBalance() === 1500, 'Deposit failed');

account1.withdraw(300);
console.assert(account1.getBalance() === 1200, 'Withdraw failed');

account1.transfer(account2, 200);
console.assert(account1.getBalance() === 1000, 'Transfer sender failed');
console.assert(account2.getBalance() === 700, 'Transfer receiver failed');

try {
  account1.withdraw(2000); // Should throw
  console.assert(false, 'Should throw error');
} catch (e) {
  console.assert(e.message.includes('Insufficient'), 'Wrong error message');
}

console.log('✓ Exercise 1.1 passed');
```

**Criterios de evaluación**:
- Validación de entrada: 3 puntos
- Implementación correcta: 5 puntos
- Manejo de errores: 2 puntos

---

### Ejercicio 1.2: Composición sobre Herencia (10 puntos)

Implementa un sistema de vehículos usando **composición** (NO herencia):

```javascript
// Crear behaviors/capabilities componibles

// TODO: Implementar estas funciones que retornan objetos con métodos
const canFly = (state) => ({
  fly() {
    console.log(`${state.name} is flying at ${state.altitude}m`);
  },
  land() {
    state.altitude = 0;
    console.log(`${state.name} has landed`);
  }
});

const canDrive = (state) => ({
  drive() {
    console.log(`${state.name} is driving at ${state.speed}km/h`);
  },
  stop() {
    state.speed = 0;
    console.log(`${state.name} has stopped`);
  }
});

const canFloat = (state) => ({
  float() {
    console.log(`${state.name} is floating`);
  }
});

// TODO: Factory function
function createVehicle(name, capabilities) {
  const state = {
    name,
    speed: 0,
    altitude: 0
  };

  // Componer capabilities
  // TODO: Combinar todos los behaviors
}

// Tests
const car = createVehicle('Tesla', [canDrive]);
car.drive();    // "Tesla is driving at 0km/h"
car.stop();     // "Tesla has stopped"

const plane = createVehicle('Boeing', [canFly, canDrive]);
plane.fly();    // "Boeing is flying at 0m"
plane.drive();  // "Boeing is driving at 0km/h"

const boat = createVehicle('Yacht', [canFloat, canDrive]);
boat.float();   // "Yacht is floating"
boat.drive();   // "Yacht is driving at 0km/h"

console.log('✓ Exercise 1.2 passed');
```

**Criterios**:
- Implementación de behaviors: 4 puntos
- Factory function correcta: 4 puntos
- Composición adecuada: 2 puntos

---

### Ejercicio 1.3: Encapsulación (10 puntos)

Crea una clase `SecureVault` que use campos privados (`#`) para proteger datos:

```javascript
class SecureVault {
  // TODO: Campos privados para almacenar:
  // - pin (número de 4 dígitos)
  // - contents (array de items)
  // - isLocked (boolean, inicialmente true)
  // - failedAttempts (contador)

  constructor(pin) {
    // TODO: Validar que pin sea 4 dígitos
    // Inicializar campos privados
  }

  /**
   * Intenta desbloquear con un PIN
   * @param {number} pin
   * @returns {boolean} true si se desbloqueó
   * Bloquea permanentemente después de 3 intentos fallidos
   */
  unlock(pin) {
    // TODO
  }

  /**
   * Bloquea la bóveda
   */
  lock() {
    // TODO
  }

  /**
   * Añade un item si está desbloqueada
   * @param {any} item
   * @throws {Error} Si está bloqueada
   */
  addItem(item) {
    // TODO
  }

  /**
   * Lista items si está desbloqueada
   * @returns {Array}
   * @throws {Error} Si está bloqueada
   */
  listItems() {
    // TODO
  }
}

// Tests
const vault = new SecureVault(1234);

try {
  vault.addItem('Diamond'); // Should throw (locked)
  console.assert(false);
} catch (e) {
  console.assert(e.message.includes('locked'), 'Should be locked initially');
}

console.assert(vault.unlock(9999) === false, 'Wrong PIN should fail');
console.assert(vault.unlock(1234) === true, 'Correct PIN should unlock');

vault.addItem('Diamond');
vault.addItem('Gold');
console.assert(vault.listItems().length === 2, 'Should have 2 items');

vault.lock();
console.assert(vault.unlock(1234) === true, 'Should unlock again');

// Test permanent lock
const vault2 = new SecureVault(5678);
vault2.unlock(0000); // Attempt 1
vault2.unlock(1111); // Attempt 2
vault2.unlock(2222); // Attempt 3
console.assert(vault2.unlock(5678) === false, 'Should be permanently locked');

console.log('✓ Exercise 1.3 passed');
```

**Criterios**:
- Uso de campos privados: 3 puntos
- Lógica de bloqueo/desbloqueo: 4 puntos
- Validaciones: 3 puntos

---

## PARTE 2: Testing (25 puntos)

### Ejercicio 2.1: Tests Unitarios (15 puntos)

Escribe tests para la siguiente función usando Jest o Vitest:

```javascript
// stringUtils.js
export function capitalize(str) {
  if (typeof str !== 'string') {
    throw new TypeError('Input must be a string');
  }
  if (str.length === 0) {
    return '';
  }
  return str.charAt(0).toUpperCase() + str.slice(1).toLowerCase();
}

export function reverse(str) {
  if (typeof str !== 'string') {
    throw new TypeError('Input must be a string');
  }
  return str.split('').reverse().join('');
}

export function isPalindrome(str) {
  if (typeof str !== 'string') {
    throw new TypeError('Input must be a string');
  }
  const cleaned = str.toLowerCase().replace(/[^a-z0-9]/g, '');
  return cleaned === reverse(cleaned);
}
```

```javascript
// stringUtils.test.js
import { capitalize, reverse, isPalindrome } from './stringUtils.js';

describe('String Utilities', () => {
  describe('capitalize()', () => {
    // TODO: Escribir mínimo 5 tests cubriendo:
    // - Caso normal
    // - String vacío
    // - String todo mayúsculas
    // - String con espacios
    // - Error con no-string
  });

  describe('reverse()', () => {
    // TODO: Escribir mínimo 4 tests cubriendo:
    // - Caso normal
    // - String vacío
    // - Palíndromo
    // - Error con no-string
  });

  describe('isPalindrome()', () => {
    // TODO: Escribir mínimo 5 tests cubriendo:
    // - Palíndromo simple ("aba")
    // - Palíndromo con espacios ("a man a plan a canal panama")
    // - No palíndromo
    // - String vacío
    // - Error con no-string
  });
});
```

**Criterios**:
- Cobertura completa: 5 puntos
- Tests bien nombrados: 3 puntos
- Uso correcto de assertions: 4 puntos
- Casos edge correctos: 3 puntos

---

### Ejercicio 2.2: Mocking (10 puntos)

Testea esta clase que tiene dependencias externas:

```javascript
// notificationService.js
export class NotificationService {
  constructor(emailClient, smsClient, logger) {
    this.emailClient = emailClient;
    this.smsClient = smsClient;
    this.logger = logger;
  }

  async notify(user, message, channel = 'email') {
    this.logger.log(`Sending notification to ${user.name} via ${channel}`);

    try {
      if (channel === 'email') {
        await this.emailClient.send(user.email, message);
      } else if (channel === 'sms') {
        await this.smsClient.send(user.phone, message);
      } else {
        throw new Error(`Unsupported channel: ${channel}`);
      }

      this.logger.log(`Notification sent successfully`);
      return true;
    } catch (error) {
      this.logger.error(`Failed to send notification: ${error.message}`);
      return false;
    }
  }
}
```

```javascript
// notificationService.test.js
import { NotificationService } from './notificationService.js';

describe('NotificationService', () => {
  let service;
  let mockEmailClient;
  let mockSMSClient;
  let mockLogger;

  beforeEach(() => {
    // TODO: Crear mocks para todas las dependencias
  });

  test('sends email notification successfully', async () => {
    // TODO: Configurar mock
    // TODO: Llamar método
    // TODO: Verificar que emailClient.send fue llamado con parámetros correctos
    // TODO: Verificar que logger.log fue llamado
  });

  test('sends SMS notification successfully', async () => {
    // TODO
  });

  test('throws error for unsupported channel', async () => {
    // TODO
  });

  test('handles errors and logs them', async () => {
    // TODO: Configurar mock para que falle
    // TODO: Verificar que logger.error fue llamado
  });
});
```

**Criterios**:
- Mocks correctamente configurados: 3 puntos
- Verificación de llamadas: 4 puntos
- Manejo de errores: 3 puntos

---

## PARTE 3: Code Smells y Refactoring (25 puntos)

### Ejercicio 3.1: Identificar y corregir Code Smells (15 puntos)

Identifica los code smells en este código y refactorízalo:

```javascript
// ❌ Código con múltiples code smells
class OrderProcessor {
  processOrder(o) {
    // Validación
    if (!o.customer) throw new Error('No customer');
    if (!o.items) throw new Error('No items');
    if (!o.payment) throw new Error('No payment');
    if (!o.customer.email) throw new Error('No email');

    // Cálculo de total
    let t = 0;
    for (let i = 0; i < o.items.length; i++) {
      t += o.items[i].price * o.items[i].quantity;
    }

    let tax = 0;
    if (o.customer.country === 'US') {
      tax = t * 0.1;
    } else if (o.customer.country === 'EU') {
      tax = t * 0.21;
    } else if (o.customer.country === 'UK') {
      tax = t * 0.2;
    }

    let shipping = 0;
    if (t < 50) {
      shipping = 10;
    }

    let total = t + tax + shipping;

    // Guardar en DB
    o.total = total;
    const mysql = require('mysql');
    const conn = mysql.createConnection({
      host: 'localhost',
      user: 'root',
      password: 'password',
      database: 'shop'
    });
    conn.connect();
    conn.query('INSERT INTO orders SET ?', o, (err, result) => {
      if (err) throw err;
    });

    // Enviar email
    const nodemailer = require('nodemailer');
    const transporter = nodemailer.createTransporter({
      service: 'gmail',
      auth: { user: 'shop@example.com', pass: 'pass' }
    });
    transporter.sendMail({
      from: 'shop@example.com',
      to: o.customer.email,
      subject: 'Order Confirmed',
      text: `Your order total is $${total}`
    });

    return o;
  }
}
```

**Tareas**:
1. Lista todos los code smells que encuentres (mínimo 5)
2. Refactoriza el código aplicando:
   - Extract Method
   - Dependency Injection
   - Strategy Pattern (para impuestos)
   - Nombres descriptivos

**Criterios**:
- Identificación de smells: 5 puntos
- Refactoring correcto: 7 puntos
- Aplicación de patrones: 3 puntos

---

### Ejercicio 3.2: Reducir Acoplamiento (10 puntos)

Refactoriza para reducir el acoplamiento:

```javascript
// Alto acoplamiento
class UserController {
  createUser(req, res) {
    const { name, email, password } = req.body;

    // Dependencia directa de bcrypt
    const bcrypt = require('bcrypt');
    const hashedPassword = bcrypt.hashSync(password, 10);

    // Dependencia directa de MySQL
    const mysql = require('mysql');
    const db = mysql.createConnection({/* config */});
    db.query(
      'INSERT INTO users (name, email, password) VALUES (?, ?, ?)',
      [name, email, hashedPassword],
      (err, result) => {
        if (err) {
          res.status(500).json({ error: err.message });
          return;
        }

        // Dependencia directa de nodemailer
        const nodemailer = require('nodemailer');
        const transporter = nodemailer.createTransporter({/* config */});
        transporter.sendMail({
          to: email,
          subject: 'Welcome',
          text: `Hi ${name}`
        });

        res.status(201).json({ id: result.insertId, name, email });
      }
    );
  }
}
```

**Tarea**: Refactoriza usando:
- Inyección de dependencias
- Separación de responsabilidades
- Interfaces/abstracciones

**Criterios**:
- Eliminación de dependencias directas: 4 puntos
- Testabilidad mejorada: 3 puntos
- Arquitectura limpia: 3 puntos

---

## PARTE 4: Métricas de Calidad (20 puntos)

### Ejercicio 4.1: Calcular métricas (10 puntos)

Para la siguiente clase, calcula:

```javascript
class ProductCatalog {
  constructor() {
    this.products = [];
    this.categories = [];
    this.suppliers = [];
  }

  addProduct(product) {
    this.products.push(product);
  }

  getProduct(id) {
    return this.products.find(p => p.id === id);
  }

  addCategory(category) {
    this.categories.push(category);
  }

  getProductsByCategory(categoryId) {
    return this.products.filter(p => p.categoryId === categoryId);
  }

  addSupplier(supplier) {
    this.suppliers.push(supplier);
  }

  getProductsBySupplier(supplierId) {
    return this.products.filter(p => p.supplierId === supplierId);
  }
}
```

**Calcula**:
1. **LCOM** (Lack of Cohesion of Methods)
2. **Número de responsabilidades** (violaciones de SRP)
3. **Complejidad ciclomática** de cada método
4. **Propuesta de refactoring** para mejorar cohesión

**Criterios**:
- Cálculo correcto de LCOM: 3 puntos
- Identificación de responsabilidades: 3 puntos
- Complejidad ciclomática: 2 puntos
- Refactoring propuesto: 2 puntos

---

### Ejercicio 4.2: Reducir complejidad (10 puntos)

Reduce la complejidad ciclomática de esta función:

```javascript
function calculateDiscount(user, product, quantity, promoCode) {
  let discount = 0;

  if (user.isPremium) {
    if (product.category === 'electronics') {
      discount += 0.15;
    } else if (product.category === 'books') {
      discount += 0.10;
    } else {
      discount += 0.05;
    }
  }

  if (quantity >= 10) {
    discount += 0.10;
  } else if (quantity >= 5) {
    discount += 0.05;
  }

  if (promoCode === 'SUMMER2024') {
    discount += 0.20;
  } else if (promoCode === 'NEWYEAR') {
    discount += 0.15;
  } else if (promoCode === 'WELCOME') {
    discount += 0.10;
  }

  if (user.hasReferral) {
    discount += 0.05;
  }

  return Math.min(discount, 0.5); // Max 50%
}

// Complejidad Ciclomática: 12 (muy alta)
```

**Tarea**: Refactoriza para reducir complejidad a ≤6 usando:
- Strategy Pattern
- Lookup tables
- Guard clauses

**Criterios**:
- Reducción de complejidad: 5 puntos
- Mantenibilidad: 3 puntos
- Extensibilidad: 2 puntos

---

## Evaluación Final

**Puntaje total**: 100 puntos

### Interpretación:

- **90-100 puntos**: Excelente. Dominas completamente los prerrequisitos
- **70-89 puntos**: Bien. Listo para el curso, refuerza áreas débiles
- **50-69 puntos**: Regular. Revisa los módulos donde fallaste antes de continuar
- **< 50 puntos**: Necesitas repasar los fundamentos del Módulo 0

### Próximos pasos:

Si aprobaste (≥70%), estás listo para:
**Módulo 1: Single Responsibility Principle (SRP)**

Si no aprobaste, revisa:
- Clases y prototipos
- Composición sobre herencia
- Testing con mocks
- Refactoring básico
