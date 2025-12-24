# Definición y Fundamentos del Single Responsibility Principle (SRP)

**Tiempo estimado**: 30 minutos
**Nivel**: Intermedio
**Prerrequisitos**: POO, clases, cohesión básica

## ¿Por qué importa este concepto?

El **Single Responsibility Principle** es el primero de los principios SOLID y quizás el más importante. Robert C. Martin lo define así:

> "Una clase debe tener una, y solo una, razón para cambiar."

**Traducción práctica**: Cada módulo o clase debe ser responsable de una sola parte de la funcionalidad, y esa responsabilidad debe estar completamente encapsulada.

**Por qué es crítico**:
1. **Mantenibilidad**: Cambios en un requerimiento solo afectan un módulo
2. **Testabilidad**: Clases con una responsabilidad son fáciles de testear
3. **Reutilización**: Módulos focalizados son más fáciles de reusar
4. **Comprensibilidad**: El código es más fácil de entender

Violaciones de SRP son la causa #1 de código legacy no mantenible.

## Comprensión intuitiva

### Analogía: El Chef Especialista vs El Chef Todero

**❌ Violación de SRP (Chef Todero)**:
```
Chef único que:
- Compra ingredientes
- Cocina todos los platos
- Lava platos
- Cobra a los clientes
- Limpia el restaurante
- Hace marketing

Problema: Si necesitas cambiar cómo se procesan pagos,
         tienes que modificar al Chef (alto riesgo).
```

**✅ SRP Aplicado (Especialización)**:
```
- Comprador de Ingredientes
- Chef de Cocina
- Lavaplatos
- Cajero
- Personal de Limpieza
- Gerente de Marketing

Ventaja: Cambiar el sistema de pagos solo afecta al Cajero.
```

## Definición formal

### Definición de Robert C. Martin (Uncle Bob)

**Formulación original**:
> "A class should have one, and only one, reason to change."

**Reformulación en términos de actores** (Clean Architecture, 2017):
> "Un módulo debe ser responsable ante uno, y solo uno, actor."

Donde **actor** = grupo de usuarios o stakeholders que requieren cambios específicos.

### Ejemplo de "razón para cambiar"

```javascript
// ❌ Múltiples razones para cambiar
class Employee {
  calculatePay() {
    // Razón 1. CFO cambia reglas de pago
  }

  reportHours() {
    // Razón 2: COO cambia formato de reporte
  }

  save() {
    // Razón 3: CTO cambia la base de datos
  }
}

// ✅ Una razón para cambiar
class PayCalculator {
  calculatePay(employee) {
    // Solo cambia si CFO lo requiere
  }
}

class HourReporter {
  reportHours(employee) {
    // Solo cambia si COO lo requiere
  }
}

class EmployeeRepository {
  save(employee) {
    // Solo cambia si CTO lo requiere
  }
}
```

## Implementación práctica

### Ejemplo 1. Violación clásica de SRP

```javascript
// ❌ VIOLACIÓN DE SRP: Clase con múltiples responsabilidades
class User {
  constructor(name, email, password) {
    this.name = name;
    this.email = email;
    this.password = password;
  }

  // Responsabilidad 1. Validación de datos
  validateEmail() {
    const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    if (!regex.test(this.email)) {
      throw new Error('Invalid email');
    }
  }

  validatePassword() {
    if (this.password.length < 8) {
      throw new Error('Password too short');
    }
  }

  // Responsabilidad 2: Encriptación
  hashPassword() {
    // Implementación de bcrypt
    const bcrypt = require('bcrypt');
    this.password = bcrypt.hashSync(this.password, 10);
  }

  // Responsabilidad 3: Persistencia en base de datos
  save() {
    const db = require('./database');
    db.query(
      'INSERT INTO users (name, email, password) VALUES (?, ?, ?)',
      [this.name, this.email, this.password]
    );
  }

  // Responsabilidad 4: Envío de emails
  sendWelcomeEmail() {
    const emailService = require('./emailService');
    emailService.send({
      to: this.email,
      subject: 'Welcome!',
      body: `Hi ${this.name}, welcome to our platform!`
    });
  }

  // Responsabilidad 5: Logging
  logActivity(action) {
    const logger = require('./logger');
    logger.log(`User ${this.name} performed ${action}`);
  }
}

// Problema: Esta clase tiene 5 razones para cambiar:
// 1. Cambios en reglas de validación
// 2. Cambios en algoritmo de encriptación
// 3. Cambios en la base de datos
// 4. Cambios en el proveedor de email
// 5. Cambios en el sistema de logging
```

### Ejemplo 2: Solución aplicando SRP

```javascript
// ✅ SRP APLICADO: Separación de responsabilidades

// RESPONSABILIDAD 1. Validación
class UserValidator {
  static validateEmail(email) {
    const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    if (!regex.test(email)) {
      throw new Error('Invalid email format');
    }
  }

  static validatePassword(password) {
    if (!password || password.length < 8) {
      throw new Error('Password must be at least 8 characters');
    }
    if (!/[A-Z]/.test(password)) {
      throw new Error('Password must contain uppercase letter');
    }
    if (!/[0-9]/.test(password)) {
      throw new Error('Password must contain number');
    }
  }

  static validateUser(userData) {
    this.validateEmail(userData.email);
    this.validatePassword(userData.password);

    if (!userData.name || userData.name.trim().length === 0) {
      throw new Error('Name is required');
    }
  }
}

// RESPONSABILIDAD 2: Encriptación
class PasswordHasher {
  constructor(bcryptRounds = 10) {
    this.rounds = bcryptRounds;
  }

  hash(plainPassword) {
    const bcrypt = require('bcrypt');
    return bcrypt.hashSync(plainPassword, this.rounds);
  }

  verify(plainPassword, hashedPassword) {
    const bcrypt = require('bcrypt');
    return bcrypt.compareSync(plainPassword, hashedPassword);
  }
}

// RESPONSABILIDAD 3: Modelo de dominio (solo datos)
class User {
  constructor(name, email, passwordHash) {
    this.name = name;
    this.email = email;
    this.passwordHash = passwordHash;
    this.createdAt = new Date();
  }

  // Métodos de dominio (no infraestructura)
  getDisplayName() {
    return this.name.split(' ')[0];
  }

  isEmailVerified() {
    return !!this.emailVerifiedAt;
  }
}

// RESPONSABILIDAD 4: Persistencia
class UserRepository {
  constructor(database) {
    this.db = database;
  }

  async save(user) {
    const result = await this.db.query(
      'INSERT INTO users (name, email, password_hash, created_at) VALUES (?, ?, ?, ?)',
      [user.name, user.email, user.passwordHash, user.createdAt]
    );
    user.id = result.insertId;
    return user;
  }

  async findByEmail(email) {
    const rows = await this.db.query(
      'SELECT * FROM users WHERE email = ?',
      [email]
    );

    if (rows.length === 0) return null;

    const row = rows[0];
    const user = new User(row.name, row.email, row.password_hash);
    user.id = row.id;
    user.createdAt = row.created_at;
    return user;
  }

  async update(user) {
    await this.db.query(
      'UPDATE users SET name = ?, email = ? WHERE id = ?',
      [user.name, user.email, user.id]
    );
    return user;
  }
}

// RESPONSABILIDAD 5: Notificaciones
class UserNotifier {
  constructor(emailService) {
    this.emailService = emailService;
  }

  async sendWelcomeEmail(user) {
    await this.emailService.send({
      to: user.email,
      subject: 'Welcome to Our Platform!',
      template: 'welcome',
      data: {
        name: user.getDisplayName()
      }
    });
  }

  async sendPasswordResetEmail(user, resetToken) {
    await this.emailService.send({
      to: user.email,
      subject: 'Reset Your Password',
      template: 'password-reset',
      data: {
        name: user.getDisplayName(),
        resetLink: `https://example.com/reset/${resetToken}`
      }
    });
  }
}

// RESPONSABILIDAD 6: Logging de actividades
class UserActivityLogger {
  constructor(logger) {
    this.logger = logger;
  }

  logRegistration(user) {
    this.logger.info('User registered', {
      userId: user.id,
      email: user.email,
      timestamp: new Date()
    });
  }

  logLogin(user) {
    this.logger.info('User logged in', {
      userId: user.id,
      timestamp: new Date()
    });
  }

  logPasswordChange(user) {
    this.logger.warn('Password changed', {
      userId: user.id,
      timestamp: new Date()
    });
  }
}

// ORQUESTADOR: Coordina las responsabilidades
class UserRegistrationService {
  constructor(repository, hasher, notifier, activityLogger) {
    this.repository = repository;
    this.hasher = hasher;
    this.notifier = notifier;
    this.activityLogger = activityLogger;
  }

  async registerUser(userData) {
    // 1. Validar
    UserValidator.validateUser(userData);

    // 2. Verificar que no exista
    const existing = await this.repository.findByEmail(userData.email);
    if (existing) {
      throw new Error('Email already registered');
    }

    // 3. Encriptar password
    const passwordHash = this.hasher.hash(userData.password);

    // 4. Crear usuario
    const user = new User(userData.name, userData.email, passwordHash);

    // 5. Guardar
    await this.repository.save(user);

    // 6. Notificar
    await this.notifier.sendWelcomeEmail(user);

    // 7. Log
    this.activityLogger.logRegistration(user);

    return user;
  }
}
```

### Uso del código refactorizado

```javascript
// Configuración (normalmente en un DI container)
const database = new Database(/* config */);
const emailService = new EmailService(/* config */);
const logger = new Logger(/* config */);

const userRepository = new UserRepository(database);
const passwordHasher = new PasswordHasher(10);
const userNotifier = new UserNotifier(emailService);
const activityLogger = new UserActivityLogger(logger);

const registrationService = new UserRegistrationService(
  userRepository,
  passwordHasher,
  userNotifier,
  activityLogger
);

// Uso
async function example() {
  try {
    const newUser = await registrationService.registerUser({
      name: 'Alice Smith',
      email: 'alice@example.com',
      password: 'SecurePass123'
    });

    console.log('User registered:', newUser.id);
  } catch (error) {
    console.error('Registration failed:', error.message);
  }
}
```

## Ventajas de aplicar SRP

### 1. Facilidad de testing

```javascript
// Test de validación (aislado)
describe('UserValidator', () => {
  test('rejects invalid email', () => {
    expect(() => {
      UserValidator.validateEmail('invalid-email');
    }).toThrow('Invalid email format');
  });

  test('rejects short password', () => {
    expect(() => {
      UserValidator.validatePassword('short');
    }).toThrow('Password must be at least 8 characters');
  });
});

// Test de registro (con mocks)
describe('UserRegistrationService', () => {
  test('registers user successfully', async () => {
    const mockRepo = {
      findByEmail: jest.fn().mockResolvedValue(null),
      save: jest.fn().mockResolvedValue({ id: 1 })
    };
    const mockHasher = {
      hash: jest.fn().mockReturnValue('hashed_password')
    };
    const mockNotifier = {
      sendWelcomeEmail: jest.fn().mockResolvedValue(true)
    };
    const mockLogger = {
      logRegistration: jest.fn()
    };

    const service = new UserRegistrationService(
      mockRepo,
      mockHasher,
      mockNotifier,
      mockLogger
    );

    const result = await service.registerUser({
      name: 'Test User',
      email: 'test@example.com',
      password: 'Password123'
    });

    expect(mockRepo.save).toHaveBeenCalled();
    expect(mockNotifier.sendWelcomeEmail).toHaveBeenCalled();
    expect(result.name).toBe('Test User');
  });
});
```

### 2. Fácil sustitución de implementaciones

```javascript
// Cambiar de bcrypt a argon2 solo requiere cambiar PasswordHasher
class Argon2PasswordHasher {
  hash(plainPassword) {
    const argon2 = require('argon2');
    return argon2.hash(plainPassword);
  }

  verify(plainPassword, hashedPassword) {
    const argon2 = require('argon2');
    return argon2.verify(hashedPassword, plainPassword);
  }
}

// El resto del código no cambia
const hasher = new Argon2PasswordHasher();
const service = new UserRegistrationService(
  repository,
  hasher, // Nueva implementación
  notifier,
  logger
);
```

## Identificando responsabilidades

### Técnica: Pregunta "¿Por qué cambiaría esta clase?"

```javascript
class ReportGenerator {
  generatePDFReport() { /* ... */ }
  sendReportByEmail() { /* ... */ }
  saveReportToDatabase() { /* ... */ }
}

// Preguntas:
// 1. ¿Cambiaría si cambia el formato de reporte? → Sí (generación)
// 2. ¿Cambiaría si cambia el servidor de email? → Sí (envío)
// 3. ¿Cambiaría si cambia la base de datos? → Sí (persistencia)

// CONCLUSIÓN: 3 responsabilidades → Violación de SRP
```

### Técnica: Análisis de cohesión

```javascript
class ProductService {
  // Grupo 1. Gestión de productos
  createProduct(data) { /* usa: database */ }
  updateProduct(id, data) { /* usa: database */ }
  deleteProduct(id) { /* usa: database */ }

  // Grupo 2: Cálculos de negocio
  calculateDiscount(product, quantity) { /* usa: product */ }
  calculateTax(product) { /* usa: product */ }

  // Grupo 3: Notificaciones
  notifyLowStock(product) { /* usa: emailService */ }
  notifyPriceChange(product) { /* usa: emailService */ }
}

// OBSERVACIÓN: 3 grupos de métodos cohesivos → 3 responsabilidades
```

## Errores frecuentes

### ❌ Error 1. SRP extremo (sobre-ingeniería)

```javascript
// Demasiado granular (innecesario)
class UserNameValidator {
  validate(name) { /* ... */ }
}

class UserEmailValidator {
  validate(email) { /* ... */ }
}

class UserPasswordValidator {
  validate(password) { /* ... */ }
}

// ✅ Mejor: Un validador cohesivo
class UserValidator {
  validateName(name) { /* ... */ }
  validateEmail(email) { /* ... */ }
  validatePassword(password) { /* ... */ }
}

// REGLA: Si siempre usas las clases juntas, probablemente son una sola responsabilidad
```

### ❌ Error 2: Confundir responsabilidad con cantidad de métodos

```javascript
// NO es violación de SRP (todos relacionados con autenticación)
class AuthenticationService {
  login(credentials) { /* ... */ }
  logout(userId) { /* ... */ }
  refreshToken(token) { /* ... */ }
  validateSession(sessionId) { /* ... */ }
  revokeAllSessions(userId) { /* ... */ }
}

// Todos los métodos tienen UNA responsabilidad: Autenticación
```

## Aplicaciones reales

### Caso: Stripe (Procesador de Pagos)

```javascript
// Separación de responsabilidades en Stripe SDK
const stripe = require('stripe')('sk_test_...');

// Responsabilidad 1. Gestión de clientes
stripe.customers.create({ email, name });
stripe.customers.retrieve(customerId);

// Responsabilidad 2: Gestión de pagos
stripe.charges.create({ amount, currency, source });
stripe.charges.retrieve(chargeId);

// Responsabilidad 3: Gestión de suscripciones
stripe.subscriptions.create({ customer, items });
stripe.subscriptions.cancel(subscriptionId);

// Cada módulo tiene UNA responsabilidad clara
```

## Resumen

**En una frase**: Una clase debe tener una, y solo una, razón para cambiar.

**Cuándo aplicarlo**: Siempre que diseñes una clase o módulo.

**Señales de violación**:
- Múltiples razones para cambiar
- Clase con >10 métodos públicos
- Dificultad para nombrar la clase sin "y" o "Manager"
- Tests difíciles de escribir

**Prerequisito crítico**: Entender cohesión, acoplamiento, y modularidad

**Siguiente paso**: Cohesión y SRP - medir cuantitativamente si una clase cumple SRP
