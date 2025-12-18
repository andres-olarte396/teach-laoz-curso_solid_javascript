# Métricas de Calidad: Cohesión y Acoplamiento

**Tiempo estimado**: 45 minutos
**Nivel**: Intermedio-Avanzado
**Prerrequisitos**: POO, Code Smells, arquitectura de software básica

## ¿Por qué importa?

**"No puedes mejorar lo que no mides"** - Peter Drucker

Las métricas de calidad transforman conceptos subjetivos ("este código es malo") en valores objetivos y medibles. Cohesión y acoplamiento son las dos métricas fundamentales que predicen:

- **Mantenibilidad**: Facilidad para cambiar el código
- **Testabilidad**: Facilidad para escribir tests
- **Reusabilidad**: Facilidad para usar en otros contextos

SOLID maximiza cohesión y minimiza acoplamiento.

## Definiciones formales

### Cohesión (Cohesion)

**Definición**: Grado en que los elementos dentro de un módulo/clase están relacionados entre sí.

- **Alta cohesión**: Todos los métodos/propiedades trabajan juntos hacia un objetivo común
- **Baja cohesión**: Métodos/propiedades hacen cosas no relacionadas

**Analogía**: Un cuchillo de cocina tiene alta cohesión (cortar). Una navaja suiza tiene baja cohesión (cortar, abrir botellas, destornillar, etc.)

### Acoplamiento (Coupling)

**Definición**: Grado de dependencia entre módulos/clases.

- **Bajo acoplamiento**: Módulos independientes, cambios en uno no afectan otros
- **Alto acoplamiento**: Módulos entrelazados, cambios en uno rompen otros

**Objetivo**: **Alta cohesión, bajo acoplamiento** (HCLC)

## Implementación práctica

### Ejemplo 1: Alta vs Baja Cohesión

```javascript
// ❌ BAJA COHESIÓN (clase hace demasiado)
class User {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // Responsabilidad 1: Validación
  validateEmail() {
    return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(this.email);
  }

  // Responsabilidad 2: Persistencia
  save() {
    database.insert('users', this);
  }

  // Responsabilidad 3: Notificaciones
  sendWelcomeEmail() {
    emailService.send(this.email, 'Welcome!');
  }

  // Responsabilidad 4: Formateo
  toJSON() {
    return { name: this.name, email: this.email };
  }

  // Responsabilidad 5: Autenticación
  checkPassword(password) {
    return bcrypt.compare(password, this.passwordHash);
  }

  // Responsabilidad 6: Logging
  logActivity(action) {
    logger.log(`User ${this.name} performed ${action}`);
  }
}
// Cohesión: 20% (6 responsabilidades distintas)
```

```javascript
// ✅ ALTA COHESIÓN (SRP aplicado)

// Cada clase tiene una responsabilidad clara
class User {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // Solo datos del dominio
  toJSON() {
    return { name: this.name, email: this.email };
  }
}

class EmailValidator {
  static validate(email) {
    return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
  }
}

class UserRepository {
  save(user) {
    return database.insert('users', user);
  }

  findById(id) {
    return database.findOne('users', { id });
  }
}

class NotificationService {
  sendWelcomeEmail(user) {
    emailService.send(user.email, 'Welcome!');
  }
}

class AuthService {
  checkPassword(user, password) {
    return bcrypt.compare(password, user.passwordHash);
  }
}

class ActivityLogger {
  log(user, action) {
    logger.log(`User ${user.name} performed ${action}`);
  }
}

// Cada clase: Cohesión 100%
```

### Ejemplo 2: Alto vs Bajo Acoplamiento

```javascript
// ❌ ALTO ACOPLAMIENTO (dependencias directas)
class OrderService {
  processOrder(orderData) {
    // Acoplamiento directo a MySQLDatabase
    const db = new MySQLDatabase();
    db.connect('localhost', 'user', 'pass');

    // Acoplamiento directo a SMTPEmailService
    const email = new SMTPEmailService();
    email.configure('smtp.gmail.com', 587);

    // Acoplamiento directo a StripePayment
    const payment = new StripePayment();
    payment.setAPIKey('sk_test_xxx');

    // Lógica
    const order = db.save(orderData);
    payment.charge(order.total);
    email.send(order.customerEmail, 'Order confirmed');

    return order;
  }
}

// Problemas:
// 1. No puedes testear sin DB real
// 2. No puedes cambiar de MySQL a PostgreSQL fácilmente
// 3. No puedes cambiar de Stripe a PayPal
// 4. Imposible usar mocks en tests
```

```javascript
// ✅ BAJO ACOPLAMIENTO (inyección de dependencias)
class OrderService {
  constructor(database, emailService, paymentGateway) {
    this.db = database;
    this.emailService = emailService;
    this.paymentGateway = paymentGateway;
  }

  async processOrder(orderData) {
    const order = await this.db.save(orderData);
    await this.paymentGateway.charge(order.total);
    await this.emailService.send(
      order.customerEmail,
      'Order confirmed'
    );
    return order;
  }
}

// Uso en producción
const service = new OrderService(
  new MySQLDatabase(),
  new SMTPEmailService(),
  new StripePayment()
);

// Uso en tests (FACIL de testear)
const testService = new OrderService(
  new InMemoryDatabase(),
  new MockEmailService(),
  new MockPaymentGateway()
);

// Ventajas:
// 1. Testeable con mocks
// 2. Fácil cambiar implementaciones
// 3. Sin dependencias hardcoded
```

## Métricas cuantitativas

### LCOM (Lack of Cohesion of Methods)

Mide cohesión en una clase. **Menor es mejor**.

**Fórmula simplificada**:
```
LCOM = (Pares de métodos que NO comparten atributos) -
       (Pares de métodos que SÍ comparten atributos)

Si LCOM < 0, entonces LCOM = 0
```

**Ejemplo**:

```javascript
class Example {
  constructor() {
    this.a = 0;
    this.b = 0;
    this.c = 0;
  }

  method1() { return this.a + this.b; }  // Usa: a, b
  method2() { return this.b * 2; }       // Usa: b
  method3() { return this.c; }           // Usa: c
}

// Análisis:
// method1-method2: Comparten 'b' ✓
// method1-method3: No comparten nada ✗
// method2-method3: No comparten nada ✗

// LCOM = 2 (no comparten) - 1 (sí comparten) = 1
// LCOM > 0 indica baja cohesión
```

**Interpretación**:
- `LCOM = 0`: Alta cohesión (ideal)
- `LCOM > 0`: Baja cohesión (considerar dividir la clase)

### Afferent Coupling (Ca) y Efferent Coupling (Ce)

**Ca (Afferent)**: Número de clases que dependen de esta clase

**Ce (Efferent)**: Número de clases de las que esta clase depende

```javascript
// ModuleA.js
import { ModuleB } from './ModuleB.js';
import { ModuleC } from './ModuleC.js';

export class ModuleA {
  constructor() {
    this.b = new ModuleB();  // ModuleA depende de ModuleB
    this.c = new ModuleC();  // ModuleA depende de ModuleC
  }
}

// Ce(ModuleA) = 2 (depende de 2 módulos)

// Si otros 3 módulos importan ModuleA:
// Ca(ModuleA) = 3 (3 módulos dependen de él)
```

### Inestabilidad (I)

```
I = Ce / (Ca + Ce)

Rango: 0 (estable) a 1 (inestable)
```

- **I = 0**: Módulo muy estable (muchos dependen de él, él no depende de nadie)
  - Ejemplo: Utilidades base, librerías core
- **I = 1**: Módulo muy inestable (depende de muchos, nadie depende de él)
  - Ejemplo: Módulos de alto nivel, controllers

**Regla**: Módulos abstractos deben ser estables (I→0). Módulos concretos pueden ser inestables (I→1).

### Complejidad Ciclomática

Mide el número de caminos independientes a través del código.

```javascript
function complexFunction(x, y, z) {
  if (x > 0) {                    // +1
    if (y > 0) {                  // +1
      return x + y;
    } else {
      return x - y;               // +1
    }
  } else if (z > 0) {             // +1
    return z * 2;
  } else {
    return 0;
  }
}

// Complejidad Ciclomática: 5
```

**Interpretación**:
- `1-10`: Simple y testeable
- `11-20`: Moderadamente complejo
- `21-50`: Alto riesgo
- `>50`: No testeable, refactorizar urgentemente

## Herramientas de análisis

### ESLint con plugins

```bash
npm install --save-dev eslint eslint-plugin-complexity
```

**.eslintrc.json**:
```json
{
  "plugins": ["complexity"],
  "rules": {
    "complexity": ["error", 10],
    "max-lines-per-function": ["warn", 50],
    "max-params": ["error", 3]
  }
}
```

### SonarQube (análisis completo)

```bash
# Instalar scanner
npm install --save-dev sonarqube-scanner

# sonar-project.properties
sonar.projectKey=my-project
sonar.sources=src
sonar.tests=tests
sonar.javascript.lcov.reportPaths=coverage/lcov.info
```

Métricas que reporta:
- **Cognitive Complexity**: Similar a ciclomática pero más precisa
- **Code Smells**: Detección automatizada
- **Technical Debt**: Tiempo estimado para arreglar problemas

### Métricas en tiempo real con Plato

```bash
npm install --global plato
plato -r -d report src/
```

Genera reporte HTML con:
- LCOM
- Complejidad ciclomática
- Líneas de código
- Maintainability Index

## Objetivos recomendados

| Métrica | Objetivo | Crítico |
|---------|----------|---------|
| Cohesión (LCOM) | ≤ 1 | > 5 |
| Acoplamiento Eferente (Ce) | ≤ 5 | > 10 |
| Complejidad Ciclomática | ≤ 10 | > 20 |
| Cobertura de Tests | ≥ 80% | < 50% |
| Líneas por función | ≤ 30 | > 100 |
| Parámetros por función | ≤ 3 | > 5 |

## Aplicación en SOLID

| Principio SOLID | Métrica afectada |
|----------------|------------------|
| SRP | Cohesión ↑, LCOM ↓ |
| OCP | Acoplamiento ↓ |
| LSP | Complejidad ↓ (menos if-else) |
| ISP | Acoplamiento ↓ |
| DIP | Acoplamiento ↓, Testabilidad ↑ |

## Resumen

**En una frase**: Cohesión mide cuán relacionados están los elementos dentro de un módulo; acoplamiento mide cuán dependientes son los módulos entre sí.

**Objetivo**: Alta cohesión (LCOM~0), bajo acoplamiento (Ce<5), baja complejidad (<10)

**Herramientas**: ESLint, SonarQube, Plato

**Prerequisito crítico**: Entender módulos, dependencias, y arquitectura de software

**Siguiente paso**: Evaluación diagnóstica para medir tu nivel actual
