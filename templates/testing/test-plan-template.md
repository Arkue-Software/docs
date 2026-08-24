# Plan de pruebas - [Sprint o entrega]

> **Instrucciones de uso - eliminar al crear un plan real:**  
> Esta plantilla se utiliza antes de ejecutar las pruebas de un Sprint o entrega. Define qué se probará, cómo se probará, quién será responsable y cuáles son los criterios para iniciar y finalizar las pruebas.  
>
> Copia este archivo en `testing/test-plans/` y nómbralo con el formato `test-plan-sprint-[numero].md`.  
> Los casos de prueba detallados deben crearse en `testing/test-cases/` y los resultados finales deben registrarse en un informe de pruebas.

## Control del documento

| Campo | Información |
|---|---|
| Código del documento | `TST-PLAN-XX` |
| Versión | `0.1` |
| Estado | `Borrador / En revisión / Aprobado` |
| Sprint o entrega | `[Sprint X]` |
| Responsable de pruebas | `[Nombre / rol QA]` |
| Revisado por | `[Arquitecto / Equipo de desarrollo]` |
| Aprobado por | `[Product Owner]` |
| Fecha de creación | `AAAA-MM-DD` |
| Última actualización | `AAAA-MM-DD` |
| Ítems relacionados de Jira | `[Enlaces]` |

## Historial de cambios

| Versión | Fecha | Descripción del cambio | Autor |
|---|---|---|---|
| 0.1 | AAAA-MM-DD | Creación inicial del plan | `[Nombre]` |

---

## 1. Objetivo

[Explicar qué se busca validar durante este Sprint o entrega.]

## 2. Alcance

### 2.1 Elementos incluidos

| Historia, requisito o ítem de Jira | Descripción | Prioridad |
|---|---|---|
| `[RV-000]` | `[Descripción]` | `Alta / Media / Baja` |

### 2.2 Elementos excluidos

- [Funcionalidades o requisitos que no se probarán en este ciclo.]

## 3. Estrategia de pruebas

| Tipo de prueba | Objetivo | Responsable | Evidencia esperada |
|---|---|---|---|
| Funcional | Verificar que la funcionalidad cumpla los criterios de aceptación | `[Nombre]` | Casos de prueba ejecutados |
| Integración | Verificar la comunicación entre componentes | `[Nombre]` | Resultado de prueba |
| Seguridad | Verificar controles de acceso o protección de datos | `[Nombre]` | Resultado de prueba |
| Rendimiento | Verificar métricas de tiempo o capacidad, si aplica | `[Nombre]` | Reporte o evidencia |

## 4. Ambiente y datos de prueba

| Elemento | Información | Estado |
|---|---|---|
| Ambiente de pruebas | `[Desarrollo / Pruebas]` | `Pendiente / Disponible` |
| Versión o commit | `[Identificador]` | `Pendiente / Disponible` |
| Datos de prueba | `Datos sintéticos` | `Pendiente / Disponible` |
| Herramientas | `[Herramientas utilizadas]` | `Pendiente / Disponible` |

## 5. Casos de prueba

| Caso de prueba | Historia o requisito relacionado | Tipo | Responsable | Estado |
|---|---|---|---|---|
| `TC-001` | `[HU-XXX / RV-000]` | `Funcional` | `[Nombre]` | `No ejecutado` |

## 6. Criterios de entrada

Las pruebas pueden iniciar cuando:

- [ ] Los requisitos o criterios de aceptación están definidos.
- [ ] El ambiente de pruebas está disponible.
- [ ] Los datos sintéticos requeridos están preparados.
- [ ] La versión o funcionalidad está disponible para probar.

## 7. Criterios de salida

Las pruebas se consideran finalizadas cuando:

- [ ] Se ejecutaron todos los casos de prueba de prioridad alta.
- [ ] Los defectos críticos fueron resueltos o aceptados formalmente.
- [ ] Las evidencias están enlazadas.
- [ ] Se elaboró el informe de pruebas.

## 8. Riesgos y dependencias

| Elemento | Impacto | Acción de mitigación | Responsable |
|---|---|---|---|
| `[Riesgo o dependencia]` | `Alto / Medio / Bajo` | `[Acción]` | `[Nombre]` |

## 9. Revisión y aprobación

| Actividad | Persona | Fecha | Resultado |
|---|---|---|---|
| Revisión del plan | `[Nombre]` | AAAA-MM-DD | `Pendiente / Aprobado` |
| Aprobación final | `[Nombre]` | AAAA-MM-DD | `Pendiente / Aprobado` |