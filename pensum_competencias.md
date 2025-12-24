# PENSUM DE COMPETENCIAS: SOLID APLICADO EN JAVASCRIPT

## PERFIL DE EGRESO

Al finalizar este curso, el estudiante será capaz de:

1. **Diseñar arquitecturas de software mantenibles y escalables** aplicando los cinco principios SOLID de manera integrada, justificando decisiones de diseño mediante análisis de trade-offs entre acoplamiento, cohesión y extensibilidad.

2. **Refactorizar código legacy hacia arquitecturas limpias** identificando violaciones de SOLID mediante análisis de métricas (LCOM, acoplamiento eferente/aferente, complejidad ciclomática) y ejecutando transformaciones sistemáticas priorizadas por impacto técnico.

3. **Implementar patrones de diseño GoF en JavaScript/TypeScript** que materializan principios SOLID (Strategy para OCP, Adapter para ISP, Factory para DIP), seleccionando el patrón apropiado según contexto arquitectónico.

4. **Validar calidad de diseño mediante Test-Driven Development** escribiendo tests que guíen hacia arquitecturas SOLID-compliant, demostrando que el cumplimiento de principios reduce complejidad de tests y aumenta testabilidad.

5. **Aplicar inyección de dependencias e inversión de control** en arquitecturas JavaScript modernas (Node.js, React, NestJS), configurando contenedores IoC y diseñando abstracciones estables que desacoplen módulos de alto nivel.

6. **Evaluar arquitecturas de software existentes** diagnosticando anti-patrones (God Objects, Tight Coupling, Leaky Abstractions), cuantificando deuda técnica mediante métricas automatizadas y proponiendo roadmaps de mejora priorizados.

7. **Diseñar sistemas extensibles sin modificación** creando arquitecturas plug-in, middlewares y sistemas configurables que respetan el principio Open/Closed, aplicando polimorfismo y composición sobre herencia.

8. **Integrar principios SOLID en metodologías ágiles** aplicando refactoring continuo en ciclos Red-Green-Refactor de TDD, emergiendo diseño evolutivo y gestionando deuda técnica en sprints.

---

## MATRIZ DE COMPETENCIAS POR MÓDULO

| Módulo | Competencia Específica (Saber Hacer) | Resultado de Aprendizaje (Evidencia) |
| :--- | :--- | :--- |
| **Módulo 0: Diagnóstico y Fundamentos OOP** | Validar dominio de OOP en JavaScript ES6+ utilizando clases, composición y testing unitario con Jest/Vitest. | Evaluación diagnóstica aprobada que demuestra identificación de code smells y cálculo de métricas de cohesión/acoplamiento. |
| **Módulo 1. Single Responsibility Principle** | Refactorizar clases con múltiples responsabilidades aplicando Extract Class/Method, diseñando componentes con responsabilidad única verificable mediante tests aislados. | Proyecto de sistema de facturación refactorizado con clases SRP-compliant, tests unitarios que requieren mínimo mocking, y métricas LCOM mejoradas. |
| **Módulo 2: Open/Closed Principle** | Diseñar sistemas extensibles sin modificar código existente, implementando Strategy, Template Method y Decorator en JavaScript. | Sistema de reportes con capacidad de añadir nuevos formatos (JSON, XML, CSV) sin modificar generador base, validado con tests de no-regresión. |
| **Módulo 3: Liskov Substitution Principle** | Diseñar jerarquías de herencia que respetan contratos comportamentales, aplicando Design by Contract y validando sustitución mediante property-based testing. | Sistema de procesadores de pago polimórfico con jerarquía LSP-compliant validada mediante abstract test suites que pasan para todas las subclases. |
| **Módulo 4: Interface Segregation Principle** | Segregar interfaces gordas en role interfaces específicas, aplicando duck typing en JavaScript y tipos estructurales en TypeScript. | API de e-commerce con interfaces segregadas por cliente (Customer, Admin, Warehouse) usando mixins y adapters, evitando dependencias innecesarias. |
| **Módulo 5: Dependency Inversion Principle** | Invertir dependencias hacia abstracciones estables aplicando Constructor/Setter Injection, configurando contenedores IoC (InversifyJS, NestJS). | Sistema de notificaciones multi-canal con arquitectura hexagonal (Ports & Adapters), dependencias inyectadas mediante DI container, tests unitarios con mocks de abstracciones. |
| **Módulo 6: Integración SOLID y Patrones** | Integrar múltiples principios SOLID aplicando patrones creacionales (Factory, Builder), estructurales (Adapter, Composite) y comportamentales (Observer, Command). | Framework de validación extensible combinando Composite, Chain of Responsibility y Strategy, respetando OCP/ISP/DIP simultáneamente. |
| **Módulo 7: TDD y SOLID** | Aplicar Test-Driven Development guiando diseño hacia arquitecturas SOLID mediante ciclo Red-Green-Refactor, identificando test smells causados por violaciones de principios. | API REST desarrollada completamente con TDD, diseño SOLID emergente documentado en commits, cobertura >85%, mutation score >70%. |
| **Módulo 8: Refactoring Sistemático** | Diagnosticar violaciones SOLID en código legacy usando análisis estático (ESLint, SonarQube), ejecutar refactorizaciones seguras con Strangler Fig y Branch by Abstraction. | Aplicación legacy refactorizada con roadmap priorizado por hotspot analysis, métricas de calidad mejoradas (reducción 40% complejidad ciclomática), tests de regresión pasando. |
| **Módulo 9: Arquitecturas Limpias** | Diseñar arquitecturas enterprise aplicando Clean Architecture y DDD, separando lógica de negocio de frameworks mediante Dependency Rule y abstracciones estables. | Aplicación con arquitectura en capas (Entities, Use Cases, Adapters, Frameworks) respetando flujo de dependencias hacia dentro, independiente de Express/Koa. |
| **Módulo 10: Proyecto Integrador** | Sintetizar todos los principios SOLID diseñando e implementando sistema CMS modular desde cero, con plugins extensibles, exportación multi-formato y tests completos. | Sistema CMS funcional con: arquitectura documentada en ADRs, diagrama de clases anotado con principios aplicados, cobertura >80%, métricas de calidad en green zone. |

---

## COMPETENCIAS TRANSVERSALES

Además de las competencias técnicas específicas, el curso desarrolla:

### Pensamiento Analítico y Resolución de Problemas

- **Diagnóstico de problemas de diseño**: Identificar root causes de acoplamiento alto, baja cohesión y rigidez arquitectónica mediante análisis sistemático.
- **Toma de decisiones basada en trade-offs**: Evaluar opciones de diseño considerando mantenibilidad, performance, complejidad y time-to-market.
- **Refactoring incremental**: Descomponer problemas complejos de arquitectura en transformaciones pequeñas y seguras.

### Comunicación Técnica

- **Documentación de decisiones arquitectónicas**: Escribir ADRs (Architecture Decision Records) justificando elección de patrones y principios.
- **Code reviews orientados a diseño**: Evaluar pull requests identificando violaciones de SOLID y sugiriendo mejoras concretas.
- **Diagramas técnicos**: Comunicar arquitectura mediante diagramas de clases UML, grafos de dependencias y mapas conceptuales.

### Aprendizaje Continuo

- **Análisis de código abierto**: Estudiar implementaciones de principios SOLID en proyectos JavaScript enterprise (NestJS, Angular, Vue).
- **Evaluación de nuevas tecnologías**: Analizar frameworks y librerías desde perspectiva de principios de diseño (extensibilidad, testabilidad, acoplamiento).
- **Auto-crítica técnica**: Revisar código propio identificando mejoras de diseño aplicables.

### Trabajo en Equipo Técnico

- **Estándares de código compartidos**: Configurar linters y herramientas de análisis estático para enforcing de principios SOLID en equipos.
- **Mentoría técnica**: Explicar principios SOLID a desarrolladores junior mediante ejemplos concretos y refactorings guiados.
- **Code ownership colectivo**: Refactorizar código de otros desarrolladores respetando estilos y manteniendo compatibilidad.

---

## NIVELES DE DOMINIO POR COMPETENCIA

| Competencia | Nivel Básico | Nivel Intermedio | Nivel Avanzado |
| :--- | :--- | :--- | :--- |
| **Aplicación de SRP** | Identifica clases con múltiples responsabilidades en código simple. Aplica Extract Class con guía. | Refactoriza módulos complejos hacia SRP sin guía. Justifica particiones mediante análisis de cambios. | Diseña arquitecturas modulares desde cero aplicando SRP a nivel de paquetes/servicios. Define límites de responsabilidad en sistemas distribuidos. |
| **Aplicación de OCP** | Implementa Strategy y Template Method siguiendo ejemplos. Extiende sistemas con puntos de extensión predefinidos. | Diseña puntos de extensión nuevos identificando variabilidad futura. Aplica múltiples patrones OCP combinados. | Arquitectura sistemas plug-in completos con registros dinámicos, configuración y lifecycle management. |
| **Aplicación de LSP** | Identifica violaciones clásicas (Square/Rectangle). Aplica principio de sustitución en jerarquías simples. | Diseña jerarquías complejas respetando contratos. Implementa Design by Contract con aserciones. | Valida LSP mediante property-based testing. Refactoriza jerarquías violadoras usando composición sobre herencia. |
| **Aplicación de ISP** | Segrega interfaces gordas obvias en JavaScript. Aplica duck typing para interfaces implícitas. | Diseña role interfaces específicas por cliente. Usa TypeScript utility types (Pick, Omit) para segregación. | Arquitectura sistemas con múltiples vistas de interfaces (cliente/admin/system). Aplica ISP a nivel de API contracts en microservicios. |
| **Aplicación de DIP** | Aplica Constructor Injection manual. Usa mocks básicos para testing. | Configura contenedores IoC (InversifyJS). Diseña abstracciones estables y adaptadores concretos. | Arquitectura sistemas con capas hexagonales completas. Implementa DI custom con lifecycle management y scopes. |
| **Testing y TDD** | Escribe tests unitarios básicos. Aplica ciclo Red-Green en katas simples. | Desarrolla features completas con TDD. Identifica test smells y refactoriza tests. | Diseña test suites arquitectónicas (abstract tests, contract tests). Mide calidad con mutation testing. |
| **Refactoring** | Aplica refactorings básicos (Rename, Extract). Usa IDE para transformaciones seguras. | Ejecuta refactorings complejos (Replace Inheritance with Delegation). Usa Strangler Fig en legacy. | Prioriza refactorings mediante hotspot analysis. Calcula ROI técnico de mejoras. Lidera iniciativas de reducción de deuda técnica. |
| **Patrones de Diseño** | Implementa patrones básicos (Factory, Observer) siguiendo blueprints. | Selecciona patrón apropiado según contexto. Combina múltiples patrones coherentemente. | Adapta patrones GoF a paradigmas funcionales (Higher-Order Functions como Strategy). Crea patrones custom documentados. |

---

## RÚBRICA DE EVALUACIÓN FINAL

### Proyecto Integrador (Sistema CMS)

| Criterio | Insuficiente (0-59%) | Suficiente (60-74%) | Bueno (75-89%) | Excelente (90-100%) |
| :--- | :--- | :--- | :--- | :--- |
| **SRP (20%)** | Clases con múltiples responsabilidades. Cohesión baja (LCOM >0.8). | Mayoría de clases con responsabilidad única. Algunos God Objects. | Todas las clases core con SRP. Justificación documentada. | SRP aplicado a todos los niveles (clases, módulos, servicios). Arquitectura modular ejemplar. |
| **OCP (20%)** | Sistema de plugins no funcional o requiere modificación de core. | Plugins funcionales pero con puntos de extensión limitados. | Sistema extensible con múltiples puntos de configuración. | Arquitectura plug-in completa con registro dinámico, eventos y lifecycle. Zero modificaciones en core. |
| **LSP (15%)** | Violaciones LSP evidentes. Tests fallan al sustituir subclases. | Jerarquías básicas respetan LSP. Algunos problemas de contrato. | Todas las jerarquías LSP-compliant validadas con tests. | Abstract test suites completas. Property-based tests validan invariantes. |
| **ISP (15%)** | Interfaces gordas. Clientes dependen de métodos no usados. | Interfaces segregadas en casos obvios. | Role interfaces bien definidas por tipo de cliente. | Interfaces cohesivas a todos los niveles. TypeScript types precisos. |
| **DIP (20%)** | Dependencias directas a implementaciones concretas. | DI manual en algunos módulos. Abstracciones parciales. | DI completa con contenedor IoC. Arquitectura hexagonal. | Dependency Rule estricta. Capas bien separadas. Framework independence demostrado. |
| **Testing (10%)** | Cobertura <50%. Tests frágiles o acoplados. | Cobertura 50-79%. Tests unitarios básicos. | Cobertura >80%. Tests bien estructurados. | Cobertura >90%. Tests arquitectónicos. Mutation score >70%. |

**Nota mínima aprobatoria**: 70%

---

## CERTIFICACIÓN Y ACREDITACIÓN

### Certificado de Aprobación

Al completar el curso con calificación ≥70%, el estudiante recibe:

**Certificado de Competencia en "SOLID Aplicado en JavaScript"**

Que acredita:

- Dominio de los cinco principios SOLID con aplicación práctica en JavaScript/TypeScript
- Capacidad de refactorizar código legacy hacia arquitecturas limpias
- Implementación de patrones de diseño GoF respetando principios SOLID
- Desarrollo guiado por tests (TDD) con diseño emergente
- Diseño de arquitecturas enterprise (Clean Architecture, Hexagonal)

### Badge Digital

El certificado incluye badge verificable con metadata:

- Duración: 105 horas
- Proyectos completados: 11 (1 por módulo + proyecto final)
- Nivel: Intermedio-Avanzado
- Tecnologías: JavaScript ES6+, TypeScript, Node.js, Jest, InversifyJS

---

## PLAN DE DESARROLLO PROFESIONAL POST-CURSO

### Rutas de Especialización Sugeridas

1. **Arquitecturas Enterprise**:
   - Domain-Driven Design táctico y estratégico
   - Event-Driven Architecture y CQRS
   - Microservicios con principios SOLID a nivel de servicio

2. **Calidad de Software**:
   - Static Analysis y herramientas de linting avanzadas
   - Mutation Testing y Property-Based Testing profundo
   - Software Metrics y predicción de defectos

3. **Frameworks y Ecosistema JavaScript**:
   - NestJS (arquitectura modular con DI nativa)
   - Angular (RxJS y programación reactiva con SOLID)
   - TypeScript avanzado (tipos genéricos, conditional types para ISP)

4. **DevOps y Automatización**:
   - CI/CD con análisis de calidad automatizado
   - Infrastructure as Code aplicando principios de diseño
   - Feature Flags y deployment strategies para OCP

### Recursos de Aprendizaje Continuo

**Libros recomendados**:

- "Clean Code" - Robert C. Martin
- "Refactoring: Improving the Design of Existing Code" - Martin Fowler
- "Design Patterns: Elements of Reusable Object-Oriented Software" - Gang of Four
- "Clean Architecture" - Robert C. Martin

**Proyectos de código abierto para analizar**:

- NestJS (ejemplar aplicación de DIP e ISP)
- RxJS (patrones Observer y Functional Reactive)
- TypeORM (Repository pattern y DIP)
- Vue.js 3 (Composition API y SRP)

**Comunidades y conferencias**:

- JSConf (conferencias JavaScript internacionales)
- Node.js Foundation
- TypeScript community (GitHub Discussions)
- Software Craftsmanship meetups

---

## ACTUALIZACIÓN DE COMPETENCIAS

Este pensum de competencias se revisará:

- **Semestralmente**: Actualización de herramientas y versiones de librerías
- **Anualmente**: Revisión de competencias según demanda del mercado
- **Bianualmente**: Rediseño de rúbricas según feedback de empleadores y egresados
