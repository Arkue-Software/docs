# Planificación del Sprint 1

| Campo | Información |
|---|---|
| Proyecto | Red Vital |
| Sprint | Sprint 1 — Habilitadores técnicos |
| Versión | 1.0 |
| Estado | Planificado |
| Product Owner | Alexander Aponte |
| Scrum Master | Carlos Santiago Pinzón Caicedo |
| Fecha de planificación | 2026-08-31 |
| Duración | Dos semanas |

## 1. Objetivo del sprint

Establecer los habilitadores de gestión y control de versiones necesarios para que el equipo pueda desarrollar Red Vital de forma colaborativa, trazable y con documentación controlada.

## 2. Capacidad

La capacidad estimada del equipo para el sprint es de **14,5 puntos**. Para este sprint se comprometen **13 puntos**, dejando un margen de 1,5 puntos para actividades de coordinación, revisión y resolución de impedimentos.

## 3. Sprint Backlog

| ID | Elemento | Épica | Tipo | Estimación | Responsable | Criterio de finalización |
|---|---|---|---|---:|---|---|
| C-15 | Crear y configurar la organización de GitHub y la estructura de repositorios | Infraestructura y DevOps | Tarea técnica | 5 | Samuel Velandia | Organización creada; repositorios `Red-Vital` y `Red-Vital-Documentacion` configurados con accesos definidos. |
| C-16 | Definir política de ramas GitFlow y aprobadores de Pull Request | Infraestructura y DevOps | Tarea técnica | 3 | Samuel Velandia | Guía GitFlow publicada; ramas protegidas y reglas de revisión documentadas. |
| C-17 | Evaluar Jira frente a GitHub Projects para la gestión del backlog | Gestión del proyecto | Spike | 5 | Samuel Velandia | Comparativo documentado y decisión registrada sobre la herramienta que gestionará el backlog. |

## 4. Entregables

- Organización de GitHub de Arkhe Software S.A.S. operativa.
- Repositorio `Red-Vital` para código y configuraciones ejecutables.
- Repositorio `Red-Vital-Documentacion` para documentación Markdown, procesos, ADRs, pruebas y plantillas.
- Guía de GitFlow en `processes/gitflow-guide.md`.
- Configuración inicial de GitHub Projects para el seguimiento del backlog.
- Documento de evaluación Jira vs. GitHub Projects y la decisión correspondiente.

## 5. Dependencias y riesgos

| Elemento | Dependencia o riesgo | Acción |
|---|---|---|
| C-15 | Es requisito para iniciar el trabajo colaborativo en los repositorios. | Configurar organización y permisos al inicio del sprint. |
| C-16 | Depende de que los repositorios estén creados. | Aplicar reglas de protección después de C-15. |
| C-17 | Requiere conocer las necesidades de seguimiento del equipo. | Validar la decisión con Product Owner y Scrum Master. |

## 6. Ceremonias y seguimiento

| Actividad | Frecuencia | Responsable |
|---|---|---|
| Daily Standup | Tres veces por semana | Scrum Master |
| Seguimiento del tablero | Continuo en GitHub Projects | Todo el equipo |
| Mesa de Arquitectura | Martes semanal | Equipo de arquitectura |
| Revisión del sprint | Al cierre del sprint | Product Owner y equipo |
| Retrospectiva | Después de la revisión | Scrum Master |

## 7. Definition of Done del sprint

El Sprint 1 se considera terminado cuando los tres elementos comprometidos estén en estado `Done`, cuenten con sus evidencias enlazadas en GitHub Projects, hayan sido revisados por al menos un integrante distinto al autor y la documentación resultante esté almacenada en el repositorio documental.

## 8. Control de cambios

| Versión | Fecha | Descripción del cambio | Responsable |
|---|---|---|---|
| 1.0 | 2026-08-31 | Creación de la planificación del Sprint 1. | Equipo Red Vital |
