# CRONOGRAMA DETALLADO: SOLID APLICADO EN JAVASCRIPT

## RESUMEN TEMPORAL

- **Duración Total**: 12 Semanas (3 meses)
- **Carga Semanal Promedio**: 8-9 horas
- **Modalidad**: Auto-dirigido con checkpoints semanales
- **Dedicación Recomendada**: 2 horas diarias de lunes a viernes, o sesiones intensivas de fin de semana

---

## DISTRIBUCIÓN TEMPORAL POR MÓDULO

| Módulo | Título | Duración (horas) | Semanas |
| :--- | :--- | :--- | :--- |
| 0 | Diagnóstico y Fundamentos OOP | 6h | 0.75 |
| 1 | Single Responsibility Principle | 12h | 1.5 |
| 2 | Open/Closed Principle | 14h | 1.75 |
| 3 | Liskov Substitution Principle | 13h | 1.6 |
| 4 | Interface Segregation Principle | 11h | 1.4 |
| 5 | Dependency Inversion Principle | 15h | 1.9 |
| 6 | Integración SOLID y Patrones | 14h | 1.75 |
| 7 | TDD y SOLID | 12h | 1.5 |
| 8 | Refactoring Sistemático | 10h | 1.25 |
| 9 | Arquitecturas Limpias | 8h | 1.0 |
| 10 | Proyecto Integrador Final | 10h | 1.25 |
| **TOTAL** | | **105h** | **~13 semanas** |

---

## CALENDARIO SEMANAL DETALLADO

### SEMANA 1: FUNDAMENTOS Y SINGLE RESPONSIBILITY (PARTE 1)

**Objetivos de la semana**: Completar diagnóstico, validar prerrequisitos e iniciar SRP

**Lunes (2h)**: Módulo 0 - Diagnóstico
- Clases y Prototipos en JavaScript (45 min)
- Composición vs Herencia (45 min)
- Testing Unitario con Jest/Vitest (30 min)

**Martes (2h)**: Módulo 0 - Finalización
- Testing Unitario con Jest/Vitest (continuación, 30 min)
- Code Smells Básicos (45 min)
- Métricas de Calidad (45 min)

**Miércoles (2h)**: Módulo 0 - Evaluación
- Evaluación Diagnóstica (90 min)
- Revisión y correcciones (30 min)

**Jueves (2h)**: Módulo 1 - Fundamentos SRP
- Definición y Fundamentos del SRP (30 min)
- Cohesión y SRP (45 min)
- Violaciones Comunes del SRP (45 min)

**Viernes (2h)**: Módulo 1 - Refactoring hacia SRP
- Extract Class (90 min)
- Extract Method (30 min)

**Carga total**: 10 horas

**Entregable**: Evaluación diagnóstica aprobada, primer ejercicio de Extract Class

---

### SEMANA 2: SINGLE RESPONSIBILITY PRINCIPLE (PARTE 2)

**Objetivos de la semana**: Completar SRP con aplicaciones prácticas en arquitecturas JavaScript

**Lunes (2h)**: Módulo 1 - Refactoring (continuación)
- Extract Method (continuación, 30 min)
- Move Method/Field (60 min)
- Ejercicios prácticos (30 min)

**Martes (2h)**: Módulo 1 - SRP en Arquitecturas
- SRP en Controladores y Servicios (90 min)
- Inicio SRP en Componentes React/Vue (30 min)

**Miércoles (2h)**: Módulo 1 - SRP en Frontend
- SRP en Componentes React/Vue (continuación, 60 min)
- Testing de Clases SRP-Compliant (60 min)

**Jueves (2h)**: Módulo 1 - Testing SRP
- Testing de Clases SRP-Compliant (continuación, 30 min)
- Inicio Proyecto: Sistema de Facturación (90 min)

**Viernes (2h)**: Módulo 1 - Proyecto Integrador
- Proyecto: Sistema de Facturación (continuación y finalización, 120 min)

**Carga total**: 10 horas

**Entregable**: Proyecto Sistema de Facturación refactorizado con SRP

---

### SEMANA 3: OPEN/CLOSED PRINCIPLE

**Objetivos de la semana**: Dominar extensibilidad sin modificación mediante OCP

**Lunes (2h)**: Módulo 2 - Fundamentos OCP
- Definición y Motivación del OCP (30 min)
- Polimorfismo como Mecanismo OCP (90 min)

**Martes (2h)**: Módulo 2 - Abstracciones y Strategy
- Abstracciones Estables (60 min)
- Strategy Pattern (60 min)

**Miércoles (2h)**: Módulo 2 - Patrones OCP
- Strategy Pattern (continuación, 30 min)
- Template Method Pattern (90 min)

**Jueves (2h)**: Módulo 2 - Decorator y JavaScript Moderno
- Decorator Pattern (90 min)
- Plugins y Middleware (inicio, 30 min)

**Viernes (2h)**: Módulo 2 - JavaScript Moderno
- Plugins y Middleware (continuación, 60 min)
- Higher-Order Functions y OCP (60 min)

**Fin de Semana (4h)**: Módulo 2 - Finalización
- Configuration Objects (60 min)
- Proyecto: Sistema de Reportes Extensible (180 min)

**Carga total**: 12 horas

**Entregable**: Sistema de Reportes con capacidad de añadir formatos sin modificar core

---

### SEMANA 4: LISKOV SUBSTITUTION PRINCIPLE

**Objetivos de la semana**: Diseñar jerarquías correctas respetando contratos

**Lunes (2h)**: Módulo 3 - Fundamentos LSP
- Definición y Contratos (45 min)
- Violaciones Clásicas de LSP (75 min)

**Martes (2h)**: Módulo 3 - Design by Contract
- Violaciones Clásicas de LSP (continuación, 15 min)
- Design by Contract en JavaScript (90 min)
- Ejercicios prácticos (15 min)

**Miércoles (2h)**: Módulo 3 - Jerarquías LSP
- Principio de Sustitución en Herencia (120 min)

**Jueves (2h)**: Módulo 3 - Covarianza y TypeScript
- Covarianza y Contravarianza (90 min)
- TypeScript y LSP (30 min)

**Viernes (2h)**: Módulo 3 - Testing Contratos
- TypeScript y LSP (continuación, 30 min)
- Property-Based Testing (90 min)

**Fin de Semana (3h)**: Módulo 3 - Finalización
- Abstract Test Cases (90 min)
- Proyecto: Sistema de Pagos Polimórfico (90 min)

**Carga total**: 11 horas

**Entregable**: Sistema de Pagos con jerarquía LSP validada por tests

---

### SEMANA 5: INTERFACE SEGREGATION PRINCIPLE

**Objetivos de la semana**: Segregar interfaces gordas en role interfaces cohesivas

**Lunes (2h)**: Módulo 4 - Fundamentos ISP
- Definición y Motivación del ISP (30 min)
- Fat Interfaces y Code Smells (60 min)
- Interfaces en JavaScript (Duck Typing) (30 min)

**Martes (2h)**: Módulo 4 - Segregación
- Interfaces en JavaScript (continuación, 30 min)
- Role Interfaces (90 min)

**Miércoles (2h)**: Módulo 4 - Adapters y Mixins
- Interface Adapters (90 min)
- Mixin Composition (30 min)

**Jueves (2h)**: Módulo 4 - TypeScript ISP
- Mixin Composition (continuación, 60 min)
- TypeScript Interfaces y Types (60 min)

**Viernes (2h)**: Módulo 4 - Utility Types y Proyecto
- TypeScript Interfaces y Types (continuación, 30 min)
- Utility Types para ISP (60 min)
- Inicio Proyecto E-commerce (30 min)

**Fin de Semana (3h)**: Módulo 4 - Finalización
- Proyecto: API de E-commerce con ISP (180 min)

**Carga total**: 11 horas

**Entregable**: API E-commerce con interfaces segregadas por rol de cliente

---

### SEMANA 6: DEPENDENCY INVERSION PRINCIPLE (PARTE 1)

**Objetivos de la semana**: Iniciar DIP con fundamentos e inyección de dependencias

**Lunes (2h)**: Módulo 5 - Fundamentos DIP
- Definición y Arquitectura en Capas (45 min)
- Abstracciones Estables vs Concretas (75 min)

**Martes (2h)**: Módulo 5 - Métricas y Constructor Injection
- Abstracciones Estables (continuación, 15 min)
- Acoplamiento y Métricas (45 min)
- Constructor Injection (60 min)

**Miércoles (2h)**: Módulo 5 - Dependency Injection
- Constructor Injection (continuación, 30 min)
- Setter/Method Injection (60 min)
- Property Injection (30 min)

**Jueves (2h)**: Módulo 5 - DI Containers
- Property Injection (continuación, 15 min)
- Manual DI Container (105 min)

**Viernes (2h)**: Módulo 5 - IoC Frameworks
- Manual DI Container (continuación, 15 min)
- InversifyJS (90 min)
- Ejercicios prácticos (15 min)

**Carga total**: 10 horas

---

### SEMANA 7: DEPENDENCY INVERSION PRINCIPLE (PARTE 2)

**Objetivos de la semana**: Completar DIP con patrones avanzados y proyecto

**Lunes (2h)**: Módulo 5 - NestJS y Factory
- NestJS Dependency Injection (90 min)
- Factory Pattern (30 min)

**Martes (2h)**: Módulo 5 - Patrones DIP
- Factory Pattern (continuación, 60 min)
- Service Locator (Anti-pattern) (60 min)

**Miércoles (2h)**: Módulo 5 - Hexagonal Architecture
- Ports and Adapters (Hexagonal) (120 min)

**Jueves (2h)**: Módulo 5 - Testing con DIP
- Mocking de Dependencias (90 min)
- Test Doubles y DIP (30 min)

**Viernes (2h)**: Módulo 5 - Proyecto Integrador
- Test Doubles (continuación, 30 min)
- Proyecto: Sistema de Notificaciones (inicio, 90 min)

**Fin de Semana (3h)**: Módulo 5 - Finalización
- Proyecto: Sistema de Notificaciones (continuación y finalización, 180 min)

**Carga total**: 11 horas

**Entregable**: Sistema de Notificaciones con arquitectura hexagonal y DI

---

### SEMANA 8: INTEGRACIÓN SOLID Y PATRONES DE DISEÑO

**Objetivos de la semana**: Aplicar múltiples principios mediante patrones GoF

**Lunes (2h)**: Módulo 6 - Patrones Creacionales
- Factory Method y SOLID (90 min)
- Abstract Factory (30 min)

**Martes (2h)**: Módulo 6 - Creacionales (continuación)
- Abstract Factory (continuación, 60 min)
- Builder Pattern (60 min)

**Miércoles (2h)**: Módulo 6 - Singleton y Estructurales
- Builder Pattern (continuación, 30 min)
- Singleton (Anti-pattern) (60 min)
- Adapter Pattern y ISP (30 min)

**Jueves (2h)**: Módulo 6 - Patrones Estructurales
- Adapter Pattern (continuación, 60 min)
- Composite Pattern (60 min)

**Viernes (2h)**: Módulo 6 - Proxy y Observer
- Composite Pattern (continuación, 30 min)
- Proxy Pattern (60 min)
- Observer Pattern (30 min)

**Fin de Semana (6h)**: Módulo 6 - Comportamiento y Proyecto
- Observer Pattern (continuación, 60 min)
- Command Pattern (90 min)
- Chain of Responsibility (90 min)
- Proyecto: Framework de Validación (180 min)

**Carga total**: 14 horas

**Entregable**: Framework de validación usando Composite, Chain y Strategy

---

### SEMANA 9: TEST-DRIVEN DEVELOPMENT Y SOLID

**Objetivos de la semana**: Validar SOLID mediante TDD y testing avanzado

**Lunes (2h)**: Módulo 7 - TDD como Driver
- Red-Green-Refactor y SOLID (90 min)
- Test Smells y Violaciones SOLID (30 min)

**Martes (2h)**: Módulo 7 - Testabilidad y Testing SRP/OCP
- Test Smells (continuación, 30 min)
- Testabilidad como Métrica de Diseño (60 min)
- Testing SRP (30 min)

**Miércoles (2h)**: Módulo 7 - Testing Principios
- Testing SRP (continuación, 30 min)
- Testing OCP (60 min)
- Testing LSP (30 min)

**Jueves (2h)**: Módulo 7 - Testing LSP/ISP/DIP
- Testing LSP (continuación, 60 min)
- Testing ISP (60 min)

**Viernes (2h)**: Módulo 7 - Testing Avanzado
- Testing DIP (90 min)
- Contract Testing (30 min)

**Fin de Semana (4h)**: Módulo 7 - Mutation Testing y Proyecto
- Contract Testing (continuación, 60 min)
- Mutation Testing (60 min)
- Proyecto: API REST con TDD (120 min)

**Carga total**: 12 horas

**Entregable**: API REST desarrollada con TDD mostrando diseño SOLID emergente

---

### SEMANA 10: REFACTORING SISTEMÁTICO

**Objetivos de la semana**: Refactorizar código legacy hacia SOLID

**Lunes (2h)**: Módulo 8 - Análisis Legacy
- Code Smells y SOLID (90 min)
- Métricas de Calidad Automatizadas (30 min)

**Martes (2h)**: Módulo 8 - Dependency Analysis y Strangler
- Métricas Automatizadas (continuación, 30 min)
- Dependency Analysis (60 min)
- Strangler Fig Pattern (30 min)

**Miércoles (2h)**: Módulo 8 - Estrategias de Refactoring
- Strangler Fig Pattern (continuación, 60 min)
- Branch by Abstraction (60 min)

**Jueves (2h)**: Módulo 8 - Parallel Change y Priorización
- Branch by Abstraction (continuación, 30 min)
- Parallel Change (Expand-Contract) (60 min)
- Hotspot Analysis (30 min)

**Viernes (2h)**: Módulo 8 - ROI y Proyecto
- Hotspot Analysis (continuación, 30 min)
- ROI de Refactoring (45 min)
- Proyecto: Refactoring Legacy (inicio, 45 min)

**Fin de Semana (2h)**: Módulo 8 - Finalización
- Proyecto: Refactoring de Aplicación Legacy (120 min)

**Carga total**: 10 horas

**Entregable**: Aplicación legacy refactorizada con roadmap documentado

---

### SEMANA 11: ARQUITECTURAS LIMPIAS

**Objetivos de la semana**: Aplicar SOLID a nivel arquitectónico

**Lunes (2h)**: Módulo 9 - Clean Architecture
- Capas y Dependency Rule (90 min)
- Entities, Use Cases, Adapters (inicio, 30 min)

**Martes (2h)**: Módulo 9 - Clean Architecture (continuación)
- Entities, Use Cases, Adapters (continuación, 90 min)
- Framework Independence (30 min)

**Miércoles (2h)**: Módulo 9 - Framework Independence y DDD
- Framework Independence (continuación, 60 min)
- Aggregates y SRP (60 min)

**Jueves (2h)**: Módulo 9 - DDD y Microservicios
- Aggregates y SRP (continuación, 30 min)
- Repositories y DIP (60 min)
- SRP a Nivel de Servicio (30 min)

**Viernes (2h)**: Módulo 9 - Microservicios
- SRP a Nivel de Servicio (continuación, 30 min)
- API Contracts y ISP (60 min)
- Revisión y consolidación (30 min)

**Carga total**: 10 horas

**Entregable**: Diseño de arquitectura limpia documentada para caso de estudio

---

### SEMANA 12: PROYECTO INTEGRADOR FINAL (PARTE 1)

**Objetivos de la semana**: Iniciar desarrollo del CMS modular

**Lunes (3h)**: Módulo 10 - Análisis y Diseño
- Análisis de Requisitos (60 min)
- Diseño Arquitectónico (120 min)

**Martes (3h)**: Módulo 10 - Implementación Core
- Implementación del Core (180 min)

**Miércoles (3h)**: Módulo 10 - Core (continuación)
- Implementación del Core (continuación, 180 min)

**Jueves (3h)**: Módulo 10 - Sistema de Plugins
- Sistema de Plugins Extensible (120 min)
- Testing del sistema de plugins (60 min)

**Viernes (3h)**: Módulo 10 - Testing y Documentación
- Testing y Documentación (120 min)
- Revisión de métricas de calidad (60 min)

**Carga total**: 15 horas (semana intensiva)

---

### SEMANA 13: PROYECTO INTEGRADOR FINAL (PARTE 2) Y CIERRE

**Objetivos de la semana**: Completar CMS, documentar decisiones y presentar

**Lunes (2h)**: Módulo 10 - Finalización
- Ajustes finales de implementación (120 min)

**Martes (2h)**: Módulo 10 - Testing
- Completar suite de tests (120 min)

**Miércoles (2h)**: Módulo 10 - Documentación
- Architecture Decision Records (ADRs) (90 min)
- Diagrama de clases anotado (30 min)

**Jueves (2h)**: Módulo 10 - Métricas y Presentación
- Cálculo de métricas finales (cohesión, acoplamiento) (60 min)
- Preparación de presentación/demo (60 min)

**Viernes (2h)**: Módulo 10 - Entrega Final
- Revisión completa del proyecto (60 min)
- Entrega y auto-evaluación (60 min)

**Carga total**: 10 horas

**Entregable Final**: Sistema CMS completo con documentación, tests y métricas

---

## CHECKPOINTS Y EVALUACIONES

### Evaluaciones Formativas (10)

| Semana | Módulo | Evaluación | Peso |
| :--- | :--- | :--- | :--- |
| 1 | 0 | Evaluación Diagnóstica | Pasa/No Pasa |
| 2 | 1 | Proyecto: Sistema de Facturación SRP | 8% |
| 3 | 2 | Proyecto: Sistema de Reportes OCP | 8% |
| 4 | 3 | Proyecto: Sistema de Pagos LSP | 8% |
| 5 | 4 | Proyecto: API E-commerce ISP | 8% |
| 7 | 5 | Proyecto: Sistema de Notificaciones DIP | 10% |
| 8 | 6 | Proyecto: Framework de Validación | 8% |
| 9 | 7 | Proyecto: API REST con TDD | 10% |
| 10 | 8 | Proyecto: Refactoring Legacy | 8% |
| 11 | 9 | Diseño de Arquitectura Limpia | 7% |

### Evaluación Sumativa (1)

| Semana | Módulo | Evaluación | Peso |
| :--- | :--- | :--- | :--- |
| 12-13 | 10 | Proyecto Integrador Final (CMS) | 25% |

**Total**: 100%

---

## RECOMENDACIONES DE ESTUDIO

### Organización Semanal Sugerida

**Opción 1: Estudio Diario Distribuido**
- Lunes a Viernes: 2 horas diarias (10h/semana)
- Fines de semana: Proyectos integradores (3-4h cuando aplique)
- Ideal para: Profesionales trabajando tiempo completo

**Opción 2: Sesiones Intensivas**
- Martes y Jueves: 3 horas (6h)
- Sábado o Domingo: 4-6 horas
- Total: 10-12h/semana
- Ideal para: Estudiantes con horarios flexibles

**Opción 3: Modo Acelerado (10 semanas)**
- Lunes a Viernes: 3 horas diarias (15h/semana)
- Permite completar en 7-8 semanas
- Ideal para: Bootcamp o dedicación exclusiva

### Técnicas de Estudio Recomendadas

1. **Práctica Deliberada**: No solo leer código, escribirlo desde cero
2. **Refactoring Incremental**: Hacer commits pequeños en proyectos
3. **Code Reviews Auto-dirigidos**: Revisar propio código 24h después
4. **Spaced Repetition**: Revisar conceptos de módulos anteriores semanalmente
5. **Teaching to Learn**: Explicar conceptos a compañeros o en blog posts

### Recursos de Soporte

**Durante el curso**:
- Office hours virtuales: Miércoles 7-8pm (opcional)
- Foro de discusión: Respuestas en <24h
- Repositorio de ejemplos: Código de referencia de todos los patrones
- Pair programming virtual: Matchmaking con otros estudiantes

**Indicadores de que vas bien**:
- Proyectos completados con ≥75% de calidad
- Tests pasando sin modificar código de producción
- Métricas de calidad mejorando en refactorings
- Capacidad de identificar violaciones SOLID en código ajeno

**Señales de alerta** (buscar ayuda):
- Proyectos tomando >200% del tiempo estimado
- Tests frágiles que rompen constantemente
- Dificultad para identificar responsabilidades en clases
- Confusión entre patrones (cuándo usar Strategy vs Template Method)

---

## AJUSTES POR NIVEL

### Ruta Básica (14-15 semanas)

- Añadir 1-2 semanas adicionales en Módulos 3 (LSP) y 5 (DIP)
- Más tiempo en proyectos con guías paso a paso
- Tutorías grupales semanales recomendadas

### Ruta Intermedia (12-13 semanas)

- Cronograma estándar presentado
- Balance entre teoría y práctica
- Office hours opcionales

### Ruta Avanzada (9-10 semanas)

- Reducir 50% tiempo en Módulos 0-1
- Omitir ejercicios básicos, foco en proyectos complejos
- Añadir desafíos opcionales (CQRS, Event Sourcing en proyecto final)
- Revisión de papers académicos sobre métricas de diseño

---

## PLANIFICACIÓN DE CONTINGENCIA

### Si te atrasas

**1 semana de retraso**:
- Reducir proyectos optativos
- Enfocarse en ejercicios core de cada módulo
- Recuperable sin ajustes mayores

**2+ semanas de retraso**:
- Considerar ruta básica en lugar de intermedia
- Fusionar Módulos 6 y 8 (reducir refactoring legacy)
- Proyecto final reducido (omitir sistema de plugins)

### Si vas adelantado

**Contenido adicional disponible**:
- TypeScript avanzado (Generics, Conditional Types para SOLID)
- Functional Programming y SOLID
- Performance optimization sin romper SOLID
- Design metrics automation con ESLint plugins custom

---

## CALENDARIO DE INICIO FLEXIBLE

Este cronograma puede iniciarse en cualquier momento. Fechas sugeridas de inicio:

- **1 de Enero**: Aprovechar inicio de año para nuevas habilidades
- **1 de Abril**: Ciclo de Q2 en empresas
- **1 de Septiembre**: Inicio de semestre académico
- **Cualquier Lunes**: El mejor momento es cuando estés listo

**Duración garantizada**: 90 días desde tu fecha de inicio con dedicación constante.

---

## DESPUÉS DEL CURSO

### Semana 14-16: Consolidación (Opcional)

- Revisar todos los proyectos completados
- Refactorizar proyecto final con aprendizajes finales
- Preparar portfolio público en GitHub
- Escribir artículo técnico sobre SOLID en JavaScript

### Plan de Mantenimiento

**Primer mes post-curso**:
- Aplicar SOLID en proyecto laboral/personal
- Code review de 2-3 repositorios open source identificando violaciones

**Trimestral**:
- Revisar notas y conceptos clave
- Actualizar conocimientos con nuevas versiones de frameworks

**Anual**:
- Re-tomar proyecto final con nuevas tecnologías
- Mentor a otro desarrollador enseñando SOLID

---

**Nota Final**: Este cronograma es una guía flexible. Ajústalo según tu ritmo de aprendizaje, pero mantén la secuencia de módulos (las dependencias conceptuales son críticas).
