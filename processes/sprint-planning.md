# Planificación del Sprint 1

| Campo | Información |
|---|---|
| Proyecto | Red Vital |
| Sprint | Sprint 1 |
| Versión | 1.0 |
| Estado | Planificado |
| Product Owner | Alexander Aponte |
| Scrum Master | Carlos Santiago Pinzón Caicedo |
| Fecha de planificación | [AAAA-MM-DD] |
| Fecha de inicio | [AAAA-MM-DD] |
| Fecha de cierre | [AAAA-MM-DD] |

## 1. Objetivo del Sprint

Establecer las bases arquitectónicas, funcionales, documentales y operativas de Red Vital, definiendo las decisiones técnicas iniciales, el backlog estructurado, la gestión documental, las herramientas DevOps y los prototipos necesarios para iniciar el desarrollo del producto de forma trazable.

## 2. Alcance

El Sprint 1 reúne los habilitadores iniciales del proyecto. Incluye decisiones de arquitectura, ajuste de requerimientos y backlog, configuración de herramientas y prácticas DevOps, gestión documental y elaboración de prototipos.

## 3. Sprint Backlog

### Épica: Arquitectura y decisiones

| ID | Issue | Estimación | Responsable |
|---|---|---:|---|
| C-01 | ADR-003 — Estilo arquitectónico | 5 | Carlos Pinzón |
| C-02 | ADR-004 — Componentes Java y .NET | 5 | Carlos Pinzón |
| C-03 | ADR-005 — Tipo de base de datos | 3 | Carlos Pinzón |
| C-04 | ADR-006 — Contenedores y despliegue | 3 | Carlos Pinzón |
| C-05 | Arquitectura de alto nivel (HLD) | 8 | Damián |
| C-06 | Drivers y killers arquitectónicos | 5 | Alexander Aponte |
| C-07 | Escenarios de calidad ISO 25010 | 5 | Tomás Silva |

### Épica: Requerimientos y backlog

| ID | Issue | Estimación | Responsable |
|---|---|---:|---|
| C-08 | Reestructurar módulos y épicas | 5 | Alexander Aponte |
| C-09 | Crear Épica 0 de gestión del proyecto | 3 | Tomás Silva |
| C-10 | Reescribir backlog y HUs técnicas | 13 | Alexander Aponte |
| C-11 | Actualizar RNF a ISO 25010 | 8 | Por asignar |
| C-12 | Completar requisitos de seguridad | 5 | Por asignar |
| C-13 | Formalizar restricciones de diseño | 2 | Samuel Velandia |
| C-14 | Modelo de datos y contratos | 8 | Sara Muñoz |

### Épica: Infraestructura y DevOps

| ID | Issue | Estimación | Responsable |
|---|---|---:|---|
| C-15 | Organización GitHub y multirepositorio | 5 | Carlos Pinzón |
| C-16 | Política de ramas GitFlow | 3 | Damián |
| C-17 | Evaluar Jira vs. GitHub Projects | 5 | Sara Muñoz |
| C-17.1 | Configurar GitHub Projects y migrar el backlog | Incluida en C-17 | Sara Muñoz |
| C-18 | Añadir Prometheus y Grafana | 3 | Samuel Velandia |
| C-19 | Revisar ciclo DevOps completo | 5 | Samuel Velandia |
| C-20 | Herramientas .NET en Tech Radar | 3 | Por asignar |
| C-21 | Configurar entornos Dev, QA y Producción | 5 | Por asignar |

### Épica: Gestión documental

| ID | Issue | Estimación | Responsable |
|---|---|---:|---|
| C-22 | Plantilla base de documentación | 5 | Carlos Pinzón y equipo |
| C-23 | Plantilla de infraestructura | 3 | Damián |
| C-24 | Plantilla de pruebas | 3 | Tomás Silva |
| C-25 | Plantilla de arquitectura SAD | 3 | Carlos Pinzón |
| C-26 | Sistema de gestión documental ISO 9001 | 5 | Samuel Velandia |
| C-27 | Migrar documentación a Markdown y Mermaid | 8 | Sara Muñoz |

### Épica: Prototipos

| ID | Issue | Estimación | Responsable |
|---|---|---:|---|
| C-28 | Mockups de pantallas principales | 8 | Carlos Pinzón |

## 4. Resumen de esfuerzo

| Épica | Puntos estimados |
|---|---:|
| Arquitectura y decisiones | 34 |
| Requerimientos y backlog | 44 |
| Infraestructura y DevOps | 29 |
| Gestión documental | 27 |
| Prototipos | 8 |
| **Total del Sprint** | **142** |

## 5. Dependencias principales

| Elemento bloqueador | Elementos dependientes |
|---|---|
| C-01, C-02 y C-03: ADRs iniciales | C-05 Arquitectura de alto nivel, C-14 Modelo de datos y contratos, C-28 Mockups. |
| C-15: Organización GitHub y multirepositorio | C-16 GitFlow, C-17 GitHub Projects y el trabajo colaborativo en repositorios. |
| C-22: Plantilla base de documentación | C-23 Plantilla de infraestructura, C-24 Plantilla de pruebas y C-25 Plantilla SAD. |
| C-08: Reestructurar módulos y épicas | C-10 Reescritura del backlog y de las historias de usuario. |

## 6. Entregables esperados

- ADR-003, ADR-004, ADR-005 y ADR-006 documentados.
- Diagrama de arquitectura de alto nivel en Mermaid.
- Drivers, killers y escenarios de calidad definidos.
- Backlog reorganizado por módulos funcionales y épicas.
- Historias de usuario de negocio y técnicas actualizadas.
- Requisitos no funcionales, de seguridad y restricciones de diseño formalizados.
- Modelo de datos y contratos iniciales.
- Organización, repositorios, GitFlow y GitHub Projects configurados.
- Evaluación Jira vs. GitHub Projects documentada.
- Tech Radar y ciclo de vida DevOps actualizados con Prometheus, Grafana y herramientas .NET.
- Ambientes de desarrollo, QA y producción definidos.
- Plantillas documentales y sistema de gestión documental implementados.
- Documentación migrada a Markdown y diagramas Mermaid.
- Mockups de las pantallas principales disponibles.

## 7. Ceremonias y seguimiento

| Actividad | Frecuencia | Responsable |
|---|---|---|
| Sprint Planning | Inicio del sprint | Scrum Master y equipo |
| Daily Standup | Tres veces por semana | Scrum Master |
| Mesa de arquitectura | Semanal | Equipo de arquitectura |
| Actualización de GitHub Projects | Continua | Todo el equipo |
| Sprint Review | Cierre del sprint | Product Owner y equipo |
| Retrospectiva | Después de la Review | Scrum Master |

## 8. Criterio de cierre

El Sprint 1 se considera finalizado cuando las issues estén en estado `Done`, sus criterios de aceptación hayan sido validados, las evidencias estén enlazadas en GitHub Projects y los documentos resultantes estén actualizados en `Red-Vital-Documentacion`.

## 9. Control de cambios

| Versión | Fecha | Descripción del cambio | Responsable |
|---|---|---|---|
| 1.0 | [AAAA-MM-DD] | Creación de la planificación del Sprint 1. | Equipo Red Vital |
