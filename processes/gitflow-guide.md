# Guía de GitFlow

| Campo | Información |
|---|---|
| Proyecto | Red Vital |
| Código | PROC-GF-001 |
| Versión | 1.0 |
| Estado | Borrador |
| Responsable | DevOps |
| Revisado por | Arquitectura y Scrum Master |
| Fecha de creación | AAAA-MM-DD |
| Próxima revisión | AAAA-MM-DD |

## 1. Objetivo

Definir la estrategia de ramas, commits, revisiones y entregas para los repositorios de Red Vital. Esta guía busca mantener la trazabilidad entre los elementos del backlog, el código, la documentación, las pruebas y los despliegues.

## 2. Alcance

Aplica a los repositorios de la organización Arkhe Software S.A.S.:

| Repositorio | Contenido |
|---|---|
| `Red-Vital` | Código fuente, frontend, backend, configuraciones ejecutables, infraestructura y automatización. |
| `Red-Vital-Documentacion` | Documentación en Markdown, ADRs, procesos, pruebas, plantillas, actas y entregables. |

Ningún integrante debe realizar cambios directos sobre las ramas protegidas.

## 3. Modelo de ramas

```mermaid
gitGraph
   commit id: "Base"
   branch development
   checkout development
   commit id: "Integración"
   branch feature/HU-001
   checkout feature/HU-001
   commit id: "Desarrollo HU"
   checkout development
   merge feature/HU-001 id: "PR aprobado"
   branch release/sprint-1
   checkout release/sprint-1
   commit id: "Validación"
   checkout main
   merge release/sprint-1 id: "Versión entregable"
