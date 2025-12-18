# Testing Unitario con Jest/Vitest

**Tiempo estimado**: 60 minutos
**Nivel**: Intermedio
**Prerrequisitos**: JavaScript ES6+, funciones, asincronía básica

## ¿Por qué importa este concepto?

El testing unitario es la base de SOLID. **No puedes refactorizar con confianza sin tests**. Los principios SOLID producen código testeable, y el testing revela violaciones de SOLID.

- **SRP**: Clases con una responsabilidad son fáciles de testear
- **DIP**: La inyección de dependencias facilita mocking
- **OCP**: Tests aseguran que extensiones no rompan funcionalidad existente

Empresas como Google, Facebook y Netflix requieren >80% de cobertura de tests.

## Comprensión intuitiva

### Analogía: Control de calidad en manufactura

Testing es como tener **inspectores de calidad en cada etapa de producción**:
- **Test unitario**: Inspeccionar cada tornillo individualmente
- **Test de integración**: Verificar que los tornillos ensamblen correctamente
- **Test end-to-end**: Probar el producto completo

Sin tests, solo descubres defectos cuando el cliente se queja (en producción).

## Definición formal

### Estructura de un Test (AAA Pattern)

```javascript
test('descripción de lo que se prueba', () => {
  // 1. ARRANGE (Preparar)
  const input = setupTestData();

  // 2. ACT (Actuar)
  const result = functionUnderTest(input);

  // 3. ASSERT (Afirmar)
  expect(result).toBe(expectedOutput);
});
```

### Características de un buen test unitario

1. **Fast**: Ejecuta en milisegundos
2. **Independent**: No depende de orden de ejecución
3. **Repeatable**: Mismo resultado siempre
4. **Self-validating**: Pass o fail automático (no inspección manual)
5. **Timely**: Escrito antes o junto con el código (TDD)

## Implementación práctica

### Instalación y configuración

```bash
# Opción 1: Jest (más maduro)
npm install --save-dev jest

# Opción 2: Vitest (más rápido, ESM nativo)
npm install --save-dev vitest
```

**package.json**:
```json
{
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage"
  }
}
```

### Ejemplo 1: Funciones puras (fácil de testear)

```javascript
// calculator.js
export function add(a, b) {
  return a + b;
}

export function divide(a, b) {
  if (b === 0) {
    throw new Error('Cannot divide by zero');
  }
  return a / b;
}

export function sum(numbers) {
  return numbers.reduce((acc, n) => acc + n, 0);
}
```

```javascript
// calculator.test.js
import { add, divide, sum } from './calculator.js';

describe('Calculator', () => {
  describe('add()', () => {
    test('adds two positive numbers', () => {
      expect(add(2, 3)).toBe(5);
    });

    test('adds negative numbers', () => {
      expect(add(-1, -2)).toBe(-3);
    });

    test('adds zero', () => {
      expect(add(5, 0)).toBe(5);
    });
  });

  describe('divide()', () => {
    test('divides two numbers', () => {
      expect(divide(10, 2)).toBe(5);
    });

    test('handles decimals', () => {
      expect(divide(7, 2)).toBeCloseTo(3.5);
    });

    test('throws error when dividing by zero', () => {
      expect(() => divide(10, 0)).toThrow('Cannot divide by zero');
    });
  });

  describe('sum()', () => {
    test('sums array of numbers', () => {
      expect(sum([1, 2, 3, 4])).toBe(10);
    });

    test('returns 0 for empty array', () => {
      expect(sum([])).toBe(0);
    });

    test('handles negative numbers', () => {
      expect(sum([10, -5, 3])).toBe(8);
    });
  });
});
```

### Ejemplo 2: Testing con mocks (dependencias)

```javascript
// userService.js
export class UserService {
  constructor(database, emailService) {
    this.db = database;
    this.emailService = emailService;
  }

  async createUser(userData) {
    // Validación
    if (!userData.email || !userData.name) {
      throw new Error('Email and name are required');
    }

    // Guardar en DB
    const user = await this.db.save(userData);

    // Enviar email de bienvenida
    await this.emailService.send({
      to: user.email,
      subject: 'Welcome!',
      body: `Hi ${user.name}, welcome to our platform!`
    });

    return user;
  }

  async deleteUser(userId) {
    const user = await this.db.findById(userId);

    if (!user) {
      throw new Error('User not found');
    }

    await this.db.delete(userId);
    return true;
  }
}
```

```javascript
// userService.test.js
import { UserService } from './userService.js';

describe('UserService', () => {
  let service;
  let mockDatabase;
  let mockEmailService;

  beforeEach(() => {
    // Crear mocks
    mockDatabase = {
      save: jest.fn(),
      findById: jest.fn(),
      delete: jest.fn()
    };

    mockEmailService = {
      send: jest.fn()
    };

    // Crear instancia con dependencias mockeadas
    service = new UserService(mockDatabase, mockEmailService);
  });

  describe('createUser()', () => {
    test('creates user successfully', async () => {
      // Arrange
      const userData = { name: 'Alice', email: 'alice@example.com' };
      const savedUser = { id: 1, ...userData };

      mockDatabase.save.mockResolvedValue(savedUser);
      mockEmailService.send.mockResolvedValue(true);

      // Act
      const result = await service.createUser(userData);

      // Assert
      expect(result).toEqual(savedUser);
      expect(mockDatabase.save).toHaveBeenCalledWith(userData);
      expect(mockEmailService.send).toHaveBeenCalledWith({
        to: 'alice@example.com',
        subject: 'Welcome!',
        body: 'Hi Alice, welcome to our platform!'
      });
    });

    test('throws error when email is missing', async () => {
      const invalidData = { name: 'Alice' };

      await expect(service.createUser(invalidData))
        .rejects
        .toThrow('Email and name are required');

      // No debe llamar a la base de datos
      expect(mockDatabase.save).not.toHaveBeenCalled();
    });

    test('handles database error', async () => {
      const userData = { name: 'Bob', email: 'bob@example.com' };
      mockDatabase.save.mockRejectedValue(new Error('DB connection failed'));

      await expect(service.createUser(userData))
        .rejects
        .toThrow('DB connection failed');
    });
  });

  describe('deleteUser()', () => {
    test('deletes existing user', async () => {
      mockDatabase.findById.mockResolvedValue({ id: 1, name: 'Alice' });
      mockDatabase.delete.mockResolvedValue(true);

      const result = await service.deleteUser(1);

      expect(result).toBe(true);
      expect(mockDatabase.findById).toHaveBeenCalledWith(1);
      expect(mockDatabase.delete).toHaveBeenCalledWith(1);
    });

    test('throws error when user not found', async () => {
      mockDatabase.findById.mockResolvedValue(null);

      await expect(service.deleteUser(999))
        .rejects
        .toThrow('User not found');

      expect(mockDatabase.delete).not.toHaveBeenCalled();
    });
  });
});
```

### Ejemplo 3: Testing con datos parametrizados

```javascript
// validator.js
export function isValidEmail(email) {
  const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return regex.test(email);
}
```

```javascript
// validator.test.js
import { isValidEmail } from './validator.js';

describe('isValidEmail()', () => {
  test.each([
    ['valid@example.com', true],
    ['user.name@domain.co.uk', true],
    ['user+tag@example.com', true],
    ['invalid', false],
    ['@example.com', false],
    ['user@', false],
    ['user @example.com', false],
    ['', false]
  ])('isValidEmail("%s") should return %s', (email, expected) => {
    expect(isValidEmail(email)).toBe(expected);
  });
});
```

## Matchers más comunes (Jest/Vitest)

```javascript
// Igualdad
expect(value).toBe(4);                    // Igualdad estricta (===)
expect(obj).toEqual({ a: 1 });            // Deep equality
expect(array).toStrictEqual([1, 2]);      // + verifica tipos

// Veracidad
expect(value).toBeTruthy();
expect(value).toBeFalsy();
expect(value).toBeNull();
expect(value).toBeUndefined();
expect(value).toBeDefined();

// Números
expect(value).toBeGreaterThan(3);
expect(value).toBeLessThanOrEqual(5);
expect(0.1 + 0.2).toBeCloseTo(0.3);       // Floats

// Strings
expect(str).toMatch(/regex/);
expect(str).toContain('substring');

// Arrays/Objects
expect(array).toContain(item);
expect(array).toHaveLength(3);
expect(obj).toHaveProperty('key', value);

// Funciones
expect(fn).toThrow();
expect(fn).toHaveBeenCalled();
expect(fn).toHaveBeenCalledWith(arg1, arg2);
expect(fn).toHaveBeenCalledTimes(2);

// Async
await expect(promise).resolves.toBe(value);
await expect(promise).rejects.toThrow();
```

## Cobertura de código

```bash
# Ejecutar tests con reporte de cobertura
npm test -- --coverage
```

**Interpretación del reporte**:
```
File           | % Stmts | % Branch | % Funcs | % Lines |
---------------|---------|----------|---------|---------|
calculator.js  | 100     | 100      | 100     | 100     |
userService.js | 95.5    | 87.5     | 100     | 95.2    |
```

- **Statements**: Líneas ejecutadas
- **Branches**: Condiciones if/else cubiertas
- **Functions**: Funciones llamadas
- **Lines**: Líneas físicas ejecutadas

**Meta recomendada**: >80% en proyectos serios

## Errores frecuentes

### ❌ Error 1: Tests dependientes

```javascript
// Mal: Tests que dependen de orden
let counter = 0;

test('increment', () => {
  counter++;
  expect(counter).toBe(1);
});

test('increment again', () => {
  counter++; // Depende del test anterior
  expect(counter).toBe(2); // Falla si se ejecuta solo
});
```

**Solución**:
```javascript
// Bien: Tests independientes
test('increment from 0', () => {
  let counter = 0;
  counter++;
  expect(counter).toBe(1);
});

test('increment from 0 again', () => {
  let counter = 0;
  counter++;
  expect(counter).toBe(1); // Independiente
});
```

### ❌ Error 2: Tests que testean implementación, no comportamiento

```javascript
// Mal: Testing interno
class ShoppingCart {
  constructor() {
    this._items = []; // Detalle de implementación
  }

  addItem(item) {
    this._items.push(item);
  }
}

test('bad test', () => {
  const cart = new ShoppingCart();
  cart.addItem('apple');

  expect(cart._items).toHaveLength(1); // Frágil
  expect(cart._items[0]).toBe('apple'); // Accede a privado
});
```

**Solución**:
```javascript
// Bien: Testing comportamiento público
class ShoppingCart {
  constructor() {
    this._items = [];
  }

  addItem(item) {
    this._items.push(item);
  }

  getItemCount() {
    return this._items.length;
  }

  hasItem(item) {
    return this._items.includes(item);
  }
}

test('good test', () => {
  const cart = new ShoppingCart();
  cart.addItem('apple');

  expect(cart.getItemCount()).toBe(1);
  expect(cart.hasItem('apple')).toBe(true);
});
```

## Aplicaciones reales

### TDD en e-commerce (Stripe)

Stripe escribe tests ANTES del código:

```javascript
// Test primero
describe('Payment processing', () => {
  test('charges customer successfully', async () => {
    const payment = await processPayment({
      amount: 1000,
      currency: 'usd',
      source: 'tok_visa'
    });

    expect(payment.status).toBe('succeeded');
    expect(payment.amount).toBe(1000);
  });
});

// Luego implementación
async function processPayment(data) {
  // Implementación guiada por el test
}
```

## Resumen

**En una frase**: Testing unitario valida que cada componente funciona aisladamente, permitiendo refactoring seguro.

**Cuándo usarlo**: Siempre. Cada función/clase debe tener tests.

**Herramientas**: Jest (completo) o Vitest (rápido, moderno)

**Prerequisito crítico**: Entender funciones, asincronía, y promesas

**Siguiente paso**: Code Smells - identificar código que necesita refactoring (y tests)
