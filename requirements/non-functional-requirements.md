# Requerimientos No Funcionales — Red Vital

## 1. Propósito

Este documento consolida los **requerimientos no funcionales vigentes de Red Vital**.

Su objetivo es mantener una vista especializada de los atributos de calidad que debe satisfacer la solución y de las medidas utilizadas para verificar su cumplimiento.

Los requerimientos no funcionales complementan los requerimientos funcionales y deben mantenerse sincronizados con:

- SRS;
- escenarios de calidad;
- SAD;
- ADRs;
- infraestructura;
- testing;
- backlog.

---

# 2. Marco de calidad

Los atributos de calidad se clasifican de acuerdo con **ISO/IEC 25010:2023**.

Cada requerimiento se expresa como un escenario verificable que incluye:

- fuente del estímulo;
- estímulo;
- artefacto afectado;
- entorno;
- respuesta esperada;
- medida de respuesta.

La existencia de una medida permite verificar objetivamente el cumplimiento.

---

# 3. Catálogo de requerimientos no funcionales

## RNF-01 — Confidencialidad de información clínica

**Característica:** Seguridad — Confidencialidad

### Escenario

Cuando un operador o administrador de banco consulte una unidad marcada como no apta durante operación normal, el sistema deberá presentar únicamente el veredicto de aptitud.

El sistema no deberá revelar:

- causa clínica;
- información diagnóstica del donante;
- información clínica no requerida para la operación.

### Medida de respuesta

Ningún campo de:

- modelo de datos;
- interfaz;
- registros de aplicación;
- métricas;

debe admitir o exponer la causa clínica.

La verificación debe realizarse mediante:

- inspección del modelo;
- revisión de respuestas de API;
- revisión de logs;
- revisión de métricas.

---

## RNF-02 — Control de acceso por jurisdicción

**Característica:** Seguridad — Control de acceso

### Escenario

Cuando un usuario territorial solicite información perteneciente a una jurisdicción fuera de su alcance, el sistema deberá denegar el acceso.

### Respuesta esperada

El sistema deberá:

- rechazar la operación;
- registrar el intento en la bitácora de auditoría;
- evitar revelar si el recurso existe;
- evitar revelar información de la jurisdicción ajena.

### Medida de respuesta

El **100 %** de las solicitudes fuera de jurisdicción debe:

- ser denegado;
- quedar registrado.

---

## RNF-03 — Operabilidad del registro anónimo

**Característica:** Capacidad de interacción — Operabilidad

### Escenario

Cuando un donante sin experiencia previa inicie el registro anónimo desde un dispositivo móvil y sin asistencia, el sistema deberá permitir completar el proceso.

### Medida de respuesta

El registro debe completarse:

- en tres pasos o menos;
- sin campos obligatorios de identificación personal.

---

## RNF-04 — Tiempo de respuesta de inventario

**Característica:** Eficiencia de desempeño — Comportamiento temporal

### Escenario

Cuando un administrador de banco consulte el inventario consolidado de su institución en condiciones normales, el sistema deberá devolver el resultado dentro del tiempo establecido.

### Medida de respuesta

El tiempo de respuesta debe ser:

**inferior a 3 segundos en el percentil 95 de las consultas.**

---

## RNF-05 — Adaptabilidad del dominio de donación

**Característica:** Flexibilidad — Adaptabilidad

### Escenario

Cuando un desarrollador incorpore un tipo de donación diferente de sangre al modelo de dominio del Servicio de Donación, el sistema deberá permitir la extensión sin rehacer la lógica existente.

### Medida de respuesta

La incorporación no debe exigir modificar:

- las entidades existentes de trazabilidad;
- la lógica existente de trazabilidad;
- las entidades existentes de inventario;
- la lógica existente de inventario.

La verificación se realiza mediante revisión del cambio.

---

## RNF-06 — Tolerancia a fallos de Notificaciones

**Característica:** Fiabilidad — Tolerancia a fallos

### Escenario

Cuando el trabajador de Notificaciones no esté disponible mientras un operador registra una donación, el sistema deberá completar la operación principal.

### Respuesta esperada

El sistema deberá:

- registrar la donación;
- conservar el evento notificable;
- entregar posteriormente el evento cuando Notificaciones vuelva a estar disponible.

### Medida de respuesta

- La donación debe quedar registrada en el **100 % de los casos**.
- El evento debe registrarse en la misma transacción que la operación principal.
- La entrega posterior debe producirse sin pérdida.
- La entrega no debe generar duplicados.

---

## RNF-07 — Interoperabilidad con SIHEVI-INS

**Característica:** Compatibilidad — Interoperabilidad

### Escenario

Cuando el Instituto Nacional de Salud requiera el reporte de donaciones y eventos adversos hacia SIHEVI-INS, el sistema deberá exponer la información requerida mediante un punto de integración documentado.

### Medida de respuesta

El reporte debe poder producirse sin modificar:

- el modelo de dominio;
- la lógica de los módulos que originan la información.

---

## RNF-08 — Corrección funcional ante vencimiento

**Característica:** Adecuación funcional — Corrección funcional

### Escenario

Cuando el reloj del sistema alcance la fecha de vencimiento de una unidad disponible, el sistema deberá excluirla automáticamente del inventario disponible.

### Medida de respuesta

Después de su fecha de vencimiento:

**ninguna unidad vencida debe aparecer como disponible en una consulta posterior.**

---

## RNF-09 — Responsabilidad y auditoría

**Característica:** Seguridad — Responsabilidad

### Escenario

Cuando un usuario:

- cambie el estado de una unidad; o
- intente acceder a información fuera de su jurisdicción;

el sistema deberá registrar el evento en la bitácora de auditoría.

### Medida de respuesta

El registro deberá contener:

- autor;
- operación;
- jurisdicción solicitada;
- marca temporal.

Además:

- deberá ser inmutable;
- no deberá incluir el dato al que se intentó acceder.

---

## RNF-10 — Inclusividad y accesibilidad

**Característica:** Capacidad de interacción — Inclusividad

### Escenario

Cuando una persona con limitación visual o motriz utilice la aplicación web mediante:

- lector de pantalla; o
- navegación únicamente por teclado;

el sistema deberá permitir completar las tareas correspondientes a su perfil.

### Medida de respuesta

La aplicación debe cumplir **WCAG 2.2 nivel AA** en, como mínimo:

- contraste;
- foco visible;
- navegación por teclado;
- tamaño de objetivo táctil.

Ninguna información debe transmitirse únicamente mediante color.

---

## RNF-11 — Instalabilidad y configuración por ambiente

**Característica:** Flexibilidad — Instalabilidad

### Escenario

Cuando un integrante despliegue el sistema completo en un entorno diferente de aquel donde fue construido, utilizando las imágenes publicadas, el sistema deberá iniciar y operar correctamente.

### Medida de respuesta

El despliegue no debe exigir modificar las imágenes.

Toda diferencia entre ambientes debe expresarse mediante:

- variables de entorno;
- configuración externa;
- secretos externos.

---

# 4. Resumen de requerimientos

| ID | Característica | Tema principal |
|---|---|---|
| RNF-01 | Seguridad | Confidencialidad |
| RNF-02 | Seguridad | Control de acceso |
| RNF-03 | Interacción | Operabilidad |
| RNF-04 | Desempeño | Tiempo de respuesta |
| RNF-05 | Flexibilidad | Adaptabilidad |
| RNF-06 | Fiabilidad | Tolerancia a fallos |
| RNF-07 | Compatibilidad | Interoperabilidad |
| RNF-08 | Adecuación funcional | Corrección funcional |
| RNF-09 | Seguridad | Auditoría y responsabilidad |
| RNF-10 | Interacción | Accesibilidad |
| RNF-11 | Flexibilidad | Instalabilidad |

---

# 5. Relación con atributos de calidad

Los requerimientos no funcionales deben mantenerse alineados con los escenarios de calidad definidos en arquitectura.

La relación es:

    RNF
     ↓
    Atributo de calidad
     ↓
    Escenario de calidad
     ↓
    Táctica arquitectónica
     ↓
    Diseño
     ↓
    Prueba
     ↓
    Evidencia

Consultar:

[Quality Attributes](../architecture/quality-attributes/README.md)

---

# 6. Relación con Testing

Cada RNF debe tener una prueba verificable.

Ejemplos:

| RNF | Verificación principal |
|---|---|
| RNF-01 | Inspección de modelo, API, logs y métricas |
| RNF-02 | Pruebas de autorización fuera de jurisdicción |
| RNF-03 | Prueba de usabilidad del registro |
| RNF-04 | Prueba de carga y medición p95 |
| RNF-05 | Revisión de cambio |
| RNF-06 | Prueba de falla de Notificaciones |
| RNF-07 | Prueba de integración |
| RNF-08 | Prueba automática de vencimiento |
| RNF-09 | Inspección de bitácora |
| RNF-10 | Evaluación de accesibilidad |
| RNF-11 | Despliegue en ambiente alterno |

Consultar:

[Test Design](../testing/test-design.md)

---

# 7. Relación con infraestructura

Varios RNF dependen directamente de decisiones de infraestructura.

Especialmente:

- RNF-04 — rendimiento;
- RNF-06 — tolerancia a fallos;
- RNF-09 — observabilidad y auditoría;
- RNF-11 — despliegue y configuración.

Consultar:

[Infrastructure](../infrastructure/README.md)

---

# 8. Relación con seguridad

Los RNF relacionados específicamente con seguridad son:

- RNF-01;
- RNF-02;
- RNF-09.

Estos se complementan con los requerimientos especializados definidos en:

[Security Requirements](./security-requirements.md)

---

# 9. Relación con requerimientos funcionales

Los RNF no reemplazan las capacidades funcionales.

Ejemplos:

- RF-05 define que una unidad no apta debe marcarse como tal.
- RNF-01 define qué información no puede exponerse al hacerlo.

- RF-18 define que una unidad vencida debe retirarse.
- RNF-08 exige que ninguna unidad vencida permanezca disponible.

- RF-23 define el inicio de sesión.
- RNF-02 define cómo debe restringirse el acceso territorial posterior.

Consultar:

[Functional Requirements](./functional-requirements.md)

---

# 10. Verificabilidad

Todo RNF debe tener:

- estímulo claramente identificable;
- condición de ejecución;
- respuesta esperada;
- medida objetiva;
- mecanismo de prueba.

Un requisito no funcional que no pueda medirse o verificarse debe revisarse.

---

# 11. Trazabilidad

Cada RNF debe permitir la relación:

    RNF
     ↓
    Escenario de calidad
     ↓
    Decisión arquitectónica
     ↓
    Implementación
     ↓
    Caso de prueba
     ↓
    Evidencia

La trazabilidad debe conservar el identificador `RNF-*`.

---

# 12. Fuente de verdad

Este documento funciona como vista especializada de los requerimientos no funcionales dentro de la wiki.

Debe permanecer sincronizado con el SRS vigente.

Ante cambios:

1. actualizar el SRS;
2. actualizar este documento;
3. revisar escenarios de calidad;
4. revisar arquitectura;
5. revisar diseño;
6. revisar testing;
7. revisar infraestructura cuando aplique.

---

# 13. Regla de mantenimiento

Un RNF solo debe modificarse cuando cambie:

- una necesidad de calidad;
- una restricción;
- una medida;
- un escenario;
- un criterio de aceptación.

Las medidas no deben relajarse únicamente para acomodar una implementación que actualmente no las cumple.

Los identificadores deben conservarse para mantener trazabilidad histórica.