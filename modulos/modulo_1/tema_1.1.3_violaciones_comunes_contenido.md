# Violaciones Comunes del Single Responsibility Principle

**Tiempo estimado**: 45 minutos
**Nivel**: Intermedio
**Prerrequisitos**: SRP básico, cohesión, refactoring

## ¿Por qué importa este concepto?

Conocer las **violaciones típicas de SRP** te permite identificarlas rápidamente en código legacy y evitarlas en código nuevo. Estas violaciones tienen nombres establecidos (God Object, Feature Envy, etc.) que facilitan la comunicación en equipos de desarrollo.

Martin Fowler cataloga estas violaciones como **code smells** - señales de que el diseño necesita refactoring.

## Violación 1. God Object (Objeto Dios)

### Definición

**God Object**: Clase que sabe demasiado o hace demasiado. Controla múltiples aspectos del sistema y tiene dependencias con muchas otras clases.

### Señales de detección

- ✅ Clase con >15 métodos públicos
- ✅ Clase con >500 líneas de código
- ✅ Nombre genérico: `Manager`, `Controller`, `Handler`, `Service`, `Utils`
- ✅ Muchas dependencias inyectadas (>5)
- ✅ Difícil de testear (requiere muchos mocks)

### Ejemplo

```javascript
// ❌ GOD OBJECT
class OrderManager {
  constructor() {
    this.database = new Database();
    this.emailService = new EmailService();
    this.paymentGateway = new PaymentGateway();
    this.inventoryService = new InventoryService();
    this.shippingService = new ShippingService();
    this.taxCalculator = new TaxCalculator();
    this.logger = new Logger();
    this.analyticsService = new AnalyticsService();
  }

  // Responsabilidad 1. Validación
  validateOrder(order) {
    if (!order.customer) throw new Error('No customer');
    if (!order.items.length) throw new Error('No items');
    if (!order.payment) throw new Error('No payment');
  }

  // Responsabilidad 2: Cálculos
  calculateTotal(order) {
    const subtotal = order.items.reduce((sum, item) =>
      sum + item.price * item.quantity, 0
    );
    const tax = this.taxCalculator.calculate(subtotal);
    const shipping = this.shippingService.calculateCost(order.items);
    return subtotal + tax + shipping;
  }

  // Responsabilidad 3: Inventario
  checkInventory(order) {
    for (const item of order.items) {
      if (!this.inventoryService.isAvailable(item.id, item.quantity)) {
        throw new Error(`Insufficient stock for ${item.name}`);
      }
    }
  }

  // Responsabilidad 4: Procesamiento de pago
  processPayment(order) {
    const total = this.calculateTotal(order);
    return this.paymentGateway.charge(order.payment, total);
  }

  // Responsabilidad 5: Actualización de inventario
  updateInventory(order) {
    for (const item of order.items) {
      this.inventoryService.reduce(item.id, item.quantity);
    }
  }

  // Responsabilidad 6: Persistencia
  saveOrder(order) {
    this.database.insert('orders', order);
  }

  // Responsabilidad 7: Notificaciones
  sendConfirmation(order) {
    this.emailService.send(
      order.customer.email,
      'Order Confirmation',
      `Your order #${order.id} has been confirmed`
    );
  }

  // Responsabilidad 8: Envío
  scheduleShipping(order) {
    this.shippingService.schedule(order);
  }

  // Responsabilidad 9: Analytics
  trackOrder(order) {
    this.analyticsService.track('order_placed', {
      orderId: order.id,
      total: order.total
    });
  }

  // Responsabilidad 10: Logging
  logOrder(order) {
    this.logger.info(`Order ${order.id} processed`);
  }

  // MÉTODO PRINCIPAL (orquesta todo)
  async placeOrder(orderData) {
    this.validateOrder(orderData);
    this.checkInventory(orderData);

    const paymentResult = await this.processPayment(orderData);
    orderData.paymentId = paymentResult.id;

    this.updateInventory(orderData);
    orderData.total = this.calculateTotal(orderData);

    this.saveOrder(orderData);
    this.sendConfirmation(orderData);
    this.scheduleShipping(orderData);
    this.trackOrder(orderData);
    this.logOrder(orderData);

    return orderData;
  }
}

// Problema: ¡10 responsabilidades en una sola clase!
```

### Solución

```javascript
// ✅ SEPARACIÓN DE RESPONSABILIDADES

// 1. Validación
class OrderValidator {
  validate(order) {
    if (!order.customer) throw new Error('No customer');
    if (!order.items.length) throw new Error('No items');
    if (!order.payment) throw new Error('No payment');
  }
}

// 2. Cálculos
class OrderPriceCalculator {
  constructor(taxCalculator, shippingCalculator) {
    this.taxCalculator = taxCalculator;
    this.shippingCalculator = shippingCalculator;
  }

  calculate(order) {
    const subtotal = order.items.reduce((sum, item) =>
      sum + item.price * item.quantity, 0
    );
    const tax = this.taxCalculator.calculate(subtotal);
    const shipping = this.shippingCalculator.calculateCost(order.items);

    return { subtotal, tax, shipping, total: subtotal + tax + shipping };
  }
}

// 3. Inventario
class InventoryChecker {
  constructor(inventoryService) {
    this.inventoryService = inventoryService;
  }

  check(items) {
    for (const item of items) {
      if (!this.inventoryService.isAvailable(item.id, item.quantity)) {
        throw new Error(`Insufficient stock for ${item.name}`);
      }
    }
  }

  reserve(items) {
    for (const item of items) {
      this.inventoryService.reduce(item.id, item.quantity);
    }
  }
}

// 4. Procesamiento de pago
class PaymentProcessor {
  constructor(paymentGateway) {
    this.gateway = paymentGateway;
  }

  async process(payment, amount) {
    return await this.gateway.charge(payment, amount);
  }
}

// 5. Persistencia
class OrderRepository {
  constructor(database) {
    this.db = database;
  }

  save(order) {
    return this.db.insert('orders', order);
  }
}

// 6. Notificaciones
class OrderNotifier {
  constructor(emailService) {
    this.emailService = emailService;
  }

  sendConfirmation(order) {
    return this.emailService.send(
      order.customer.email,
      'Order Confirmation',
      `Your order #${order.id} has been confirmed`
    );
  }
}

// 7. Envío
class ShippingScheduler {
  constructor(shippingService) {
    this.shippingService = shippingService;
  }

  schedule(order) {
    return this.shippingService.schedule(order);
  }
}

// 8-9. Observabilidad
class OrderObserver {
  constructor(analyticsService, logger) {
    this.analytics = analyticsService;
    this.logger = logger;
  }

  track(order) {
    this.analytics.track('order_placed', {
      orderId: order.id,
      total: order.total
    });
    this.logger.info(`Order ${order.id} processed`);
  }
}

// ORQUESTADOR (ahora solo coordina)
class OrderService {
  constructor(
    validator,
    priceCalculator,
    inventoryChecker,
    paymentProcessor,
    repository,
    notifier,
    shippingScheduler,
    observer
  ) {
    this.validator = validator;
    this.priceCalculator = priceCalculator;
    this.inventoryChecker = inventoryChecker;
    this.paymentProcessor = paymentProcessor;
    this.repository = repository;
    this.notifier = notifier;
    this.shippingScheduler = shippingScheduler;
    this.observer = observer;
  }

  async placeOrder(orderData) {
    // Orquesta, no implementa
    this.validator.validate(orderData);
    this.inventoryChecker.check(orderData.items);

    const pricing = this.priceCalculator.calculate(orderData);
    const paymentResult = await this.paymentProcessor.process(
      orderData.payment,
      pricing.total
    );

    this.inventoryChecker.reserve(orderData.items);

    const order = {
      ...orderData,
      ...pricing,
      paymentId: paymentResult.id
    };

    await this.repository.save(order);
    await this.notifier.sendConfirmation(order);
    await this.shippingScheduler.schedule(order);
    this.observer.track(order);

    return order;
  }
}
```

## Violación 2: Feature Envy

### Definición

**Feature Envy**: Un método que usa más datos/métodos de otra clase que de su propia clase.

### Señales de detección

- ✅ Método accede repetidamente a `otherObject.property`
- ✅ Método tiene más llamadas a otra clase que a `this`
- ✅ Método sería más fácil de testear en la otra clase

### Ejemplo

```javascript
// ❌ FEATURE ENVY
class ShoppingCart {
  constructor() {
    this.items = [];
  }

  addItem(item) {
    this.items.push(item);
  }

  // Este método tiene "envidia" de Order
  calculateShippingCost(order) {
    let weight = 0;
    for (const item of order.items) {
      weight += item.weight * item.quantity;
    }

    const destination = order.customer.address;
    const distance = this.calculateDistance(
      order.warehouse.location,
      destination
    );

    let cost = 5; // Base cost
    if (weight > 10) cost += (weight - 10) * 0.5;
    if (distance > 100) cost += (distance - 100) * 0.1;

    return cost;
  }
}

// Problema: calculateShippingCost usa más datos de Order que de ShoppingCart
```

### Solución

```javascript
// ✅ MOVER MÉTODO A CLASE CORRECTA
class Order {
  constructor(items, customer, warehouse) {
    this.items = items;
    this.customer = customer;
    this.warehouse = warehouse;
  }

  calculateShippingCost() {
    const weight = this.getTotalWeight();
    const distance = this.getDistance();

    let cost = 5; // Base cost
    if (weight > 10) cost += (weight - 10) * 0.5;
    if (distance > 100) cost += (distance - 100) * 0.1;

    return cost;
  }

  getTotalWeight() {
    return this.items.reduce((sum, item) =>
      sum + (item.weight * item.quantity), 0
    );
  }

  getDistance() {
    return calculateDistance(
      this.warehouse.location,
      this.customer.address
    );
  }
}

class ShoppingCart {
  constructor() {
    this.items = [];
  }

  addItem(item) {
    this.items.push(item);
  }

  // Ya no necesita calcular shipping
}
```

## Violación 3: Divergent Change

### Definición

**Divergent Change**: Una clase cambia frecuentemente por diferentes razones (múltiples tipos de cambios).

### Señales de detección

- ✅ "Cuando cambian los requisitos de X, modifico esta clase"
- ✅ "Cuando cambian los requisitos de Y, también modifico esta clase"
- ✅ Clase modificada en múltiples historias de usuario no relacionadas

### Ejemplo

```javascript
// ❌ DIVERGENT CHANGE
class Employee {
  constructor(name, salary, department) {
    this.name = name;
    this.salary = salary;
    this.department = department;
  }

  // RAZÓN DE CAMBIO 1. Reglas de nómina cambian
  calculatePay() {
    let pay = this.salary;
    if (this.department === 'Sales') {
      pay += this.calculateCommission();
    }
    if (this.isManager()) {
      pay *= 1.2;
    }
    return pay;
  }

  // RAZÓN DE CAMBIO 2: Formato de reporte cambia
  generateReport() {
    return {
      name: this.name,
      department: this.department,
      salary: this.calculatePay(),
      performance: this.getPerformanceRating()
    };
  }

  // RAZÓN DE CAMBIO 3: Base de datos cambia
  save() {
    database.update('employees', this.id, {
      name: this.name,
      salary: this.salary,
      department: this.department
    });
  }
}

// Problema: 3 razones diferentes para cambiar esta clase
// (Nómina, Reportes, Persistencia)
```

### Solución

```javascript
// ✅ SEPARAR POR RAZÓN DE CAMBIO

// RAZÓN 1. Reglas de nómina
class PayrollCalculator {
  calculatePay(employee) {
    let pay = employee.salary;
    if (employee.department === 'Sales') {
      pay += this.calculateCommission(employee);
    }
    if (employee.isManager) {
      pay *= 1.2;
    }
    return pay;
  }

  calculateCommission(employee) {
    // Lógica de comisiones
    return employee.sales * 0.1;
  }
}

// RAZÓN 2: Formato de reporte
class EmployeeReportGenerator {
  constructor(payrollCalculator) {
    this.payrollCalculator = payrollCalculator;
  }

  generate(employee) {
    return {
      name: employee.name,
      department: employee.department,
      salary: this.payrollCalculator.calculatePay(employee),
      performance: employee.performanceRating
    };
  }
}

// RAZÓN 3: Persistencia
class EmployeeRepository {
  save(employee) {
    return database.update('employees', employee.id, {
      name: employee.name,
      salary: employee.salary,
      department: employee.department
    });
  }

  findById(id) {
    return database.findOne('employees', { id });
  }
}

// Modelo de dominio (sin razones de cambio externas)
class Employee {
  constructor(name, salary, department) {
    this.name = name;
    this.salary = salary;
    this.department = department;
    this.isManager = false;
    this.sales = 0;
    this.performanceRating = 0;
  }
}
```

## Violación 4: Shotgun Surgery

### Definición

**Shotgun Surgery**: Un cambio requiere modificar muchas clases diferentes. Opuesto a Divergent Change.

### Señales de detección

- ✅ "Para agregar esta feature, tuve que tocar 10 archivos diferentes"
- ✅ Cambios pequeños requieren muchas modificaciones dispersas
- ✅ Difícil rastrear todos los lugares que necesitan cambiar

### Ejemplo

```javascript
// ❌ SHOTGUN SURGERY
// Agregar soporte para nueva moneda requiere cambios en múltiples lugares

// Archivo 1. product.js
class Product {
  constructor(name, priceUSD) {
    this.name = name;
    this.priceUSD = priceUSD; // ❌ Hardcoded USD
  }

  getPrice() {
    return `$${this.priceUSD} USD`; // ❌ Formato hardcoded
  }
}

// Archivo 2: cart.js
class ShoppingCart {
  calculateTotal() {
    let totalUSD = 0; // ❌ Hardcoded USD
    for (const item of this.items) {
      totalUSD += item.product.priceUSD * item.quantity;
    }
    return totalUSD;
  }

  display() {
    return `Total: $${this.calculateTotal()} USD`; // ❌ Formato hardcoded
  }
}

// Archivo 3: invoice.js
class Invoice {
  generate(cart) {
    return {
      total: cart.calculateTotal(),
      currency: 'USD', // ❌ Hardcoded
      formattedTotal: `$${cart.calculateTotal()} USD` // ❌ Hardcoded
    };
  }
}

// Archivo 4: payment.js
class PaymentProcessor {
  process(amount) {
    // Solo procesa USD
    return this.gateway.chargeUSD(amount); // ❌ Hardcoded
  }
}

// Problema: Agregar EUR requiere cambiar 4+ archivos
```

### Solución

```javascript
// ✅ CENTRALIZAR LÓGICA DE MONEDA

// Toda la lógica de moneda en un solo lugar
class Money {
  constructor(amount, currency = 'USD') {
    this.amount = amount;
    this.currency = currency;
  }

  format() {
    const symbols = { USD: '$', EUR: '€', GBP: '£' };
    return `${symbols[this.currency]}${this.amount} ${this.currency}`;
  }

  add(other) {
    if (this.currency !== other.currency) {
      throw new Error('Cannot add different currencies');
    }
    return new Money(this.amount + other.amount, this.currency);
  }

  multiply(factor) {
    return new Money(this.amount * factor, this.currency);
  }

  convertTo(targetCurrency, exchangeRate) {
    return new Money(this.amount * exchangeRate, targetCurrency);
  }
}

// Ahora agregar nueva moneda es fácil
class Product {
  constructor(name, price) {
    this.name = name;
    this.price = price; // Money object
  }

  getPrice() {
    return this.price.format();
  }
}

class ShoppingCart {
  calculateTotal() {
    let total = new Money(0, this.items[0].product.price.currency);
    for (const item of this.items) {
      const itemTotal = item.product.price.multiply(item.quantity);
      total = total.add(itemTotal);
    }
    return total;
  }

  display() {
    return `Total: ${this.calculateTotal().format()}`;
  }
}

// Agregar soporte para JPY ahora solo requiere agregar '¥' al objeto symbols
```

## Herramientas de detección

### ESLint Rules

```javascript
// .eslintrc.js
module.exports = {
  rules: {
    'max-lines-per-function': ['error', 50],
    'max-lines': ['error', 300],
    'max-params': ['error', 4],
    'complexity': ['error', 10],
    'max-depth': ['error', 3],
    'max-nested-callbacks': ['error', 3]
  }
};
```

### SonarQube Detection

```javascript
// SonarQube detecta automáticamente:
// - God Classes (>200 líneas)
// - Feature Envy (alta dependencia externa)
// - Divergent Change (múltiples cambios no relacionados)
// - Shotgun Surgery (cambios dispersos)
```

## Resumen de Violaciones

| Violación | Síntoma | Solución |
|-----------|---------|----------|
| **God Object** | Clase hace demasiado (>10 responsabilidades) | Extract Class múltiple |
| **Feature Envy** | Método usa más de otra clase que la suya | Move Method |
| **Divergent Change** | Una clase cambia por muchas razones | Extract Class por razón |
| **Shotgun Surgery** | Un cambio toca muchas clases | Move Method/Field para centralizar |

## Resumen

**En una frase**: Las violaciones comunes de SRP tienen patrones reconocibles que pueden detectarse sistemáticamente.

**Señales de alerta**:
- Clases con nombres genéricos (Manager, Handler, Service)
- Métodos que usan más de otras clases
- Cambios que requieren tocar múltiples archivos
- Clases que cambian frecuentemente

**Prerequisito crítico**: Entender SRP, cohesión y refactoring

**Siguiente paso**: Técnicas de refactoring para corregir violaciones (Extract Class, Extract Method, Move Method)
