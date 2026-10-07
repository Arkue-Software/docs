# Matriz de Trazabilidad — Red Vital

## 1. Propósito

Este documento consolida la **trazabilidad de requerimientos de Red Vital**.

Su objetivo es permitir seguir cada necesidad desde su origen hasta:

- requerimiento;
- módulo;
- historia de usuario;
- implementación;
- prueba;
- evidencia.

La trazabilidad debe mantenerse en ambos sentidos.

---

# 2. Principio de trazabilidad

La relación general es:

    Preocupación / Necesidad
             ↓
        Requerimiento
             ↓
       Historia de usuario
             ↓
         Tarea / Issue
             ↓
        Implementación
             ↓
         Caso de prueba
             ↓
           Evidencia

La trazabilidad también debe permitir recorrer esta cadena en sentido inverso.

---

# 3. Reglas

La matriz debe cumplir las siguientes reglas:

1. todo requerimiento debe tener una fuente;
2. todo requerimiento funcional debe relacionarse con al menos una historia de usuario;
3. toda historia debe derivar de un requerimiento o restricción;
4. toda implementación debe estar vinculada con trabajo registrado;
5. todo requerimiento verificable debe tener método de prueba;
6. toda ejecución debe producir evidencia;
7. no deben existir elementos huérfanos.

---

# 4. Matriz principal de requerimientos funcionales

| Requerimiento | Nombre | Historia(s) | Módulo | Preocupación |
|---|---|---|---|---|
| RF-01 | Registro voluntario de donante | HU-01 | M1 | P1, P18 |
| RF-02 | Cálculo de elegibilidad | HU-02, HU-03 | M1 | P2, P3 |
| RF-03 | Registro de donación | HU-05 | M3 | P7 |
| RF-04 | Trazabilidad de origen | HU-06 | M3 | P15 |
| RF-05 | Marcado de unidad no apta | HU-07 | M3 | P5 |
| RF-06 | Protocolo de disposición final | HU-08 | M3 | P15 |
| RF-07 | Consulta de existencias | HU-09 | M4 | P10 |
| RF-08 | Publicación de campañas | HU-15, HU-16 | M2 | P9 |
| RF-09 | Administración de la jerarquía territorial | HU-13, HU-14 | M6 | P11, P13 |
| RF-10 | Transferencia entre bancos | HU-17, HU-18 | M5 | P9 |
| RF-11 | Reconocimiento no monetario | HU-19, HU-20 | M1 | P4 |
| RF-12 | Notificación al donante | HU-21, HU-22 | ST1 | P3, P8 |
| RF-13 | Tablero de indicadores | HU-23 | M7 | P12, P13 |
| RF-14 | Movilización de donantes | HU-24 | M5 | P9 |
| RF-15 | Reserva de cupo con expiración | HU-25 | M2 | P1 |
| RF-17 | Consulta de historial propio | HU-04 | M1 | P3 |
| RF-18 | Exclusión de unidades vencidas | HU-12 | M4 | P6 |
| RF-19 | Alerta de escasez | HU-10 | M4 | P8 |
| RF-20 | Alerta de vencimiento próximo | HU-11 | M4 | P6 |
| RF-21 | Consulta de bitácora | HU-27 | ST2 | P17 |
| RF-22 | Asignación de jurisdicción de usuario | HU-28 | M6 | P11, P16 |
| RF-23 | Inicio de sesión | HU-29 | ST3 | P2, P11 |
| RF-24 | Término de sesión | HU-30 | ST3 | P2 |

---

# 5. Requerimiento no funcional con historia técnica

El requerimiento RNF-05 genera trabajo directo de modelado.

| Requerimiento | Nombre | Historia | Área | Preocupación |
|---|---|---|---|---|
| RNF-05 | Adaptabilidad del modelo de dominio | HU-26 | Transversal | P14 |

HU-26 se considera una historia técnica.

No deriva de un requerimiento funcional, sino de:

- RNF-05;
- RI-01.

---

# 6. Estado de cobertura de historias

La línea base vigente mantiene cobertura de:

- 23 requerimientos funcionales;
- 1 requerimiento no funcional con trabajo explícito de modelado.

Toda historia asociada debe contar con una fuente válida.

Actualmente, según el SRS V3, no existen elementos huérfanos en la matriz base.

---

# 7. RF-16

El identificador `RF-16` no pertenece al catálogo funcional vigente.

Su contenido anterior relacionado con la extensión hacia otros tipos de donación fue trasladado a:

- RNF-05;
- RI-01.

Por esta razón la numeración continúa desde RF-15 hacia RF-17.

---

# 8. Matriz de verificación

Los requerimientos se verifican mediante diferentes métodos.

| Método | Requerimientos principales |
|---|---|
| Prueba funcional | RF-01 a RF-24 |
| Prueba de seguridad | RNF-01, RNF-02, RNF-09, RF-23, RF-24 |
| Medición | RNF-04, RNF-06 |
| Inspección | RNF-01, RNF-05, RNF-11, RI-01 a RI-04 |
| Auditoría de conformidad | RNF-10 |
| Demostración | RNF-03, RNF-07, RNF-08 |

---

# 9. Trazabilidad hacia Testing

Los requerimientos deben relacionarse con casos de prueba.

Ejemplo:

| Requerimiento | Caso de prueba |
|---|---|
| RF-23 | TC-ID-001 |
| RF-24 | TC-ID-003 |
| RNF-02 | TC-INS-002 / TC-SEC-003 |
| RNF-06 | TC-NOT-001 |
| RNF-09 | TC-OBS-001 |
| RNF-11 | TC-DEP-001 |

La relación definitiva debe mantenerse sincronizada con:

[Test Design](../testing/test-design.md)

---

# 10. Trazabilidad de seguridad

Los requerimientos de seguridad deben poder rastrearse hacia controles y pruebas.

Ejemplo:

| Fuente | Security Requirement | Prueba |
|---|---|---|
| RNF-01 | SR-01 | TC-OBS-002 |
| RNF-02 | SR-02 | TC-INS-002 |
| RF-23 | SR-05 | TC-ID-001 |
| RF-24 | SR-08 | TC-ID-003 |
| RNF-09 | SR-16 | TC-OBS-001 |
| RF-21 | SR-18 | Caso de auditoría correspondiente |

---

# 11. Trazabilidad hacia arquitectura

Algunos requerimientos producen o condicionan decisiones arquitectónicas.

La relación general es:

    Requerimiento
         ↓
    Driver arquitectónico
         ↓
        ADR
         ↓
       Táctica
         ↓
    Implementación
         ↓
       Prueba

Ejemplos:

- autenticación y sesiones → ADR de identidad;
- control de acceso → ADR de autorización;
- persistencia distribuida → ADR de servicios y datos;
- auditoría → ADR de bitácora;
- tolerancia a fallos de Notificaciones → estrategia de entrega asíncrona.

---

# 12. Trazabilidad hacia datos

Los requerimientos que afectan persistencia deben reflejarse en:

- DD;
- modelo lógico;
- modelo físico;
- diccionario de datos.

Ejemplos:

- RF-03 → donación;
- RF-04 → historial de unidad;
- RF-09 → jerarquía territorial;
- RF-22 → jurisdicción;
- RNF-09 → auditoría.

Consultar:

[Data](../data/README.md)

---

# 13. Trazabilidad hacia integración

Los requerimientos que atraviesan servicios deben reflejarse en contratos.

Ejemplos:

- RF-08;
- RF-10;
- RF-12;
- RNF-06;
- RNF-07.

Consultar:

[Integration](../integration/)

---

# 14. Trazabilidad hacia infraestructura

Los RNF asociados con:

- rendimiento;
- seguridad;
- despliegue;
- tolerancia a fallos;
- instalabilidad;

deben rastrearse hacia controles de infraestructura.

Ejemplos:

| Requerimiento | Infraestructura relacionada |
|---|---|
| RNF-04 | Métricas y pruebas de carga |
| RNF-06 | Recuperación de Notificaciones |
| RNF-11 | Contenedores y variables de ambiente |
| RNF-02 | Redes, gateway y autorización |
| RNF-09 | Logs y auditoría |

---

# 15. Requerimientos fuera de la primera línea base

Algunos requerimientos están especificados pero no comprometidos completamente para la primera versión.

## RF-14 — Movilización de donantes

**Prioridad:** Could

Depende de que el mecanismo de escalamiento y transferencias esté disponible.

---

## RF-15 — Reserva de cupo con expiración

**Prioridad:** Could

Introduce manejo adicional de concurrencia.

---

## RNF-07 — Interoperabilidad con SIHEVI-INS

El sistema debe dejar preparado y documentado el punto de integración.

La ejecución real depende de:

- credenciales institucionales;
- especificación técnica externa.

---

## RNF-05 — Extensión hacia otros tipos de donación

La primera versión no implementa administración completa de nuevos tipos de donación.

Sí debe verificarse que el modelo pueda extenderse sin rehacer:

- trazabilidad;
- inventario.

---

# 16. Matriz extendida de implementación

A medida que avance el proyecto debe completarse esta tabla.

| Req. | HU | Issue / Task | Servicio | Contrato | Caso de prueba | Evidencia | Estado |
|---|---|---|---|---|---|---|---|
| RF-01 | HU-01 | Pendiente | Pendiente | Pendiente | Pendiente | Pendiente | Pendiente |
| RF-02 | HU-02 / HU-03 | Pendiente | Pendiente | Pendiente | Pendiente | Pendiente | Pendiente |
| RF-03 | HU-05 | Pendiente | Donación | Pendiente | TC-DON-001 | Pendiente | Pendiente |
| RF-08 | HU-15 / HU-16 | Pendiente | Campañas | Pendiente | TC-CAM-001 | Pendiente | Pendiente |
| RF-23 | HU-29 | Pendiente | Identidad | Pendiente | TC-ID-001 | Pendiente | Pendiente |
| RF-24 | HU-30 | Pendiente | Identidad | Pendiente | TC-ID-003 | Pendiente | Pendiente |
| RNF-05 | HU-26 | Pendiente | Transversal | — | Pendiente | Pendiente | Pendiente |

Esta sección debe completarse progresivamente con los identificadores reales del backlog.

---

# 17. Elementos huérfanos

Un elemento se considera huérfano cuando:

### Requerimiento huérfano

No tiene:

- historia;
- implementación prevista;
- método de verificación.

### Historia huérfana

No tiene origen en:

- requerimiento;
- restricción;
- decisión técnica justificada.

### Prueba huérfana

No verifica ningún:

- requerimiento;
- escenario;
- riesgo;
- control.

La meta es:

**0 elementos huérfanos.**

---

# 18. Control de cambios

Cuando cambie un requerimiento se debe revisar:

- historia relacionada;
- tareas;
- arquitectura;
- contratos;
- modelo de datos;
- casos de prueba;
- documentación;
- evidencia.

Cuando cambie una historia también debe comprobarse que su requerimiento de origen continúe siendo válido.

---

# 19. Documentos relacionados

- [Functional Requirements](./functional-requirements.md)
- [Non-Functional Requirements](./non-functional-requirements.md)
- [Security Requirements](./security-requirements.md)
- [SRS](./)
- [Architecture](../architecture/README.md)
- [Data](../data/README.md)
- [Integration](../integration/)
- [Testing](../testing/README.md)

---

# 20. Pendientes

- [ ] Vincular los Issues reales del backlog.
- [ ] Vincular tareas técnicas.
- [ ] Vincular cada RF con sus casos de prueba definitivos.
- [ ] Vincular RNF con escenarios de calidad.
- [ ] Vincular requerimientos con ADRs correspondientes.
- [ ] Vincular contratos OpenAPI.
- [ ] Añadir evidencias de ejecución.
- [ ] Mantener estado de implementación actualizado.

---

# 21. Regla de mantenimiento

Esta matriz debe actualizarse cuando:

- se cree un requerimiento;
- cambie un requerimiento;
- se cree una historia;
- se elimine una historia;
- cambie un contrato;
- cambie arquitectura;
- se cree un caso de prueba;
- se ejecute una prueba;
- se genere evidencia.

La trazabilidad debe mantenerse como una relación viva entre:

    Necesidad
       ↓
    Requerimiento
       ↓
    Backlog
       ↓
    Implementación
       ↓
    Verificación
       ↓
    Evidencia