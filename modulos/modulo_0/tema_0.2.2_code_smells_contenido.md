# Code Smells Básicos

**Tiempo estimado**: 45 minutos
**Nivel**: Intermedio
**Prerrequisitos**: POO, refactoring básico

## ¿Por qué importa?

Los **Code Smells** (olores de código) son señales de que algo podría estar mal diseñado. No son bugs, pero indican violaciones potenciales de SOLID y dificultad para mantener el código.

Martin Fowler: "Un code smell es un síntoma superficial que usualmente corresponde a un problema más profundo en el sistema".

## Principales Code Smells

###  1. Long Method (Método largo)

**Síntoma**: Métodos con >20 líneas, difíciles de entender de un vistazo

```javascript
// ❌ Smell: Long Method
class OrderProcessor {
  processOrder(order) {
    // Validación (10 líneas)
    if (!order.customer) throw new Error('No customer');
    if (!order.items || order.items.length === 0) throw new Error('No items');
    if (!order.payment) throw new Error('No payment');

    // Cálculo (15 líneas)
    let subtotal = 0;
    for (const item of order.items) {
      subtotal += item.price * item.quantity;
    }
    const tax = subtotal * 0.21;
    const shipping = subtotal > 50 ? 0 : 5;
    const total = subtotal + tax + shipping;

    // Persistencia (10 líneas)
    order.total = total;
    this.database.save(order);

    // Notificación (8 líneas)
    this.emailService.send({
      to: order.customer.email,
      subject: 'Order confirmed',
      body: `Your order #${order.id} total is $${total}`
    });

    return order;
  }
}
```

**Solución**: Extract Method (SRP)

```javascript
// ✅ Refactorizado
class OrderProcessor {
  processOrder(order) {
    this.validateOrder(order);
    const total = this.calculateTotal(order);
    this.saveOrder(order, total);
    this.notifyCustomer(order, total);
    return order;
  }

  validateOrder(order) {
    if (!order.customer) throw new Error('No customer');
    if (!order.items?.length) throw new Error('No items');
    if (!order.payment) throw new Error('No payment');
  }

  calculateTotal(order) {
    const subtotal = order.items.reduce((sum, item) =>
      sum + (item.price * item.quantity), 0
    );
    const tax = subtotal * 0.21;
    const shipping = subtotal > 50 ? 0 : 5;
    return subtotal + tax + shipping;
  }

  saveOrder(order, total) {
    order.total = total;
    this.database.save(order);
  }

  notifyCustomer(order, total) {
    this.emailService.send({
      to: order.customer.email,
      subject: 'Order confirmed',
      body: `Your order #${order.id} total is $${total}`
    });
  }
}
```

### 2. Large Class (Clase grande)

**Síntoma**: Clase con >200 líneas o >10 métodos públicos

```javascript
// ❌ Smell: God Object
class User {
  constructor() { /* ... */ }

  // Autenticación
  login() { /* ... */ }
  logout() { /* ... */ }
  changePassword() { /* ... */ }

  // Perfil
  updateProfile() { /* ... */ }
  uploadAvatar() { /* ... */ }
  getProfile() { /* ... */ }

  // Permisos
  hasPermission() { /* ... */ }
  addRole() { /* ... */ }
  removeRole() { /* ... */ }

  // Notificaciones
  sendEmail() { /* ... */ }
  sendSMS() { /* ... */ }
  getNotifications() { /* ... */ }

  // Actividad
  logActivity() { /* ... */ }
  getHistory() { /* ... */ }

  // ... 20 métodos más
}
```

**Solución**: Extract Class (SRP)

```javascript
// ✅ Separar responsabilidades
class User {
  constructor(id, email) {
    this.id = id;
    this.email = email;
  }
}

class AuthService {
  login(user, password) { /* ... */ }
  logout(user) { /* ... */ }
  changePassword(user, newPassword) { /* ... */ }
}

class UserProfile {
  constructor(user) {
    this.user = user;
  }

  update(data) { /* ... */ }
  uploadAvatar(file) { /* ... */ }
  get() { /* ... */ }
}

class PermissionService {
  hasPermission(user, permission) { /* ... */ }
  addRole(user, role) { /* ... */ }
}

class NotificationService {
  sendEmail(user, message) { /* ... */ }
  sendSMS(user, message) { /* ... */ }
}
```

### 3. Duplicate Code (Código duplicado)

**Síntoma**: Mismo código en múltiples lugares

```javascript
// ❌ Smell: Duplicación
class ReportGenerator {
  generatePDFReport(data) {
    const header = `Report Generated: ${new Date().toISOString()}`;
    const footer = `Page ${this.currentPage}`;
    // PDF logic
  }

  generateExcelReport(data) {
    const header = `Report Generated: ${new Date().toISOString()}`;
    const footer = `Page ${this.currentPage}`;
    // Excel logic
  }

  generateHTMLReport(data) {
    const header = `Report Generated: ${new Date().toISOString()}`;
    const footer = `Page ${this.currentPage}`;
    // HTML logic
  }
}
```

**Solución**: Extract Method / Template Method

```javascript
// ✅ Eliminar duplicación
class ReportGenerator {
  generateReport(data, format) {
    const header = this.createHeader();
    const footer = this.createFooter();
    return this.formatReport(data, format, header, footer);
  }

  createHeader() {
    return `Report Generated: ${new Date().toISOString()}`;
  }

  createFooter() {
    return `Page ${this.currentPage}`;
  }

  formatReport(data, format, header, footer) {
    const formatters = {
      pdf: () => this.toPDF(data, header, footer),
      excel: () => this.toExcel(data, header, footer),
      html: () => this.toHTML(data, header, footer)
    };

    return formatters[format]();
  }
}
```

### 4. Long Parameter List (Lista larga de parámetros)

**Síntoma**: Funciones con >3-4 parámetros

```javascript
// ❌ Smell: Demasiados parámetros
function createUser(
  firstName,
  lastName,
  email,
  phone,
  address,
  city,
  country,
  postalCode,
  role,
  department
) {
  // ...
}
```

**Solución**: Parameter Object

```javascript
// ✅ Usar objeto de configuración
function createUser(userConfig) {
  const {
    firstName,
    lastName,
    email,
    phone,
    address,
    role
  } = userConfig;
  // ...
}

// Uso
createUser({
  firstName: 'Alice',
  lastName: 'Smith',
  email: 'alice@example.com',
  phone: '123456789',
  address: {
    street: '123 Main St',
    city: 'NYC',
    country: 'USA',
    postalCode: '10001'
  },
  role: 'admin'
});
```

### 5. Primitive Obsession

**Síntoma**: Uso excesivo de tipos primitivos en lugar de objetos de dominio

```javascript
// ❌ Smell: Primitivos en lugar de objetos
class Order {
  constructor() {
    this.customerEmail = '';  // String primitivo
    this.amount = 0;          // Number primitivo
    this.currency = 'USD';    // String primitivo
  }

  setAmount(value, currency) {
    if (value < 0) throw new Error('Invalid amount');
    this.amount = value;
    this.currency = currency;
  }
}
```

**Solución**: Value Objects

```javascript
// ✅ Crear objetos de valor
class Email {
  constructor(value) {
    if (!this.isValid(value)) {
      throw new Error('Invalid email');
    }
    this.value = value;
  }

  isValid(email) {
    return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
  }

  toString() {
    return this.value;
  }
}

class Money {
  constructor(amount, currency = 'USD') {
    if (amount < 0) throw new Error('Amount cannot be negative');
    this.amount = amount;
    this.currency = currency;
  }

  add(other) {
    if (this.currency !== other.currency) {
      throw new Error('Cannot add different currencies');
    }
    return new Money(this.amount + other.amount, this.currency);
  }

  toString() {
    return `${this.amount} ${this.currency}`;
  }
}

class Order {
  constructor(customerEmail, amount) {
    this.customerEmail = new Email(customerEmail);
    this.amount = new Money(amount);
  }
}
```

### 6. Feature Envy

**Síntoma**: Un método usa más datos de otra clase que de la suya propia

```javascript
// ❌ Smell: Feature Envy
class ShoppingCart {
  calculateTax(order) {
    let total = 0;
    for (const item of order.items) {
      total += item.price * item.quantity;
    }
    return total * order.customer.taxRate;
  }
}
```

**Solución**: Move Method

```javascript
// ✅ Mover a la clase correcta
class Order {
  calculateTax() {
    const subtotal = this.items.reduce((sum, item) =>
      sum + (item.price * item.quantity), 0
    );
    return subtotal * this.customer.taxRate;
  }
}

class ShoppingCart {
  processOrder(order) {
    const tax = order.calculateTax(); // Delegación
    // ...
  }
}
```

## Herramientas para detectar Code Smells

```bash
# ESLint (JavaScript)
npm install --save-dev eslint
npx eslint --init

# SonarQube (análisis profundo)
# Detecta: complejidad ciclomática, duplicación, cobertura

# CodeClimate (CI/CD)
# Reporta: maintainability, test coverage, duplication
```

**Ejemplo de .eslintrc.json**:
```json
{
  "rules": {
    "max-lines-per-function": ["error", 30],
    "max-params": ["error", 3],
    "complexity": ["error", 10],
    "max-depth": ["error", 3]
  }
}
```

## Resumen

| Code Smell | Violación SOLID | Refactoring |
|------------|----------------|-------------|
| Long Method | SRP | Extract Method |
| Large Class | SRP | Extract Class |
| Duplicate Code | DRY | Extract Method/Class |
| Long Parameter List | ISP | Parameter Object |
| Primitive Obsession | OCP | Value Object |
| Feature Envy | SRP | Move Method |

**En una frase**: Code smells son síntomas de diseño pobre que indican necesidad de refactoring.

**Prerequisito crítico**: Entender refactoring y principios de diseño

**Siguiente paso**: Métricas de calidad para medir objetivamente cohesión y acoplamiento
