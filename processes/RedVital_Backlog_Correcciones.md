# Backlog de Correcciones — RedVital

## Épica A — Arquitectura y decisiones

| ID | Tarea | Est. | Responsable | Entregable |
|---|---|---|---|---|
| C-01 | ADR-003: arquitectura distribuida en reemplazo del monolito modular. Debe justificar el estilo concreto, sin asumir microservicios | 5 | Arquitecto + Sebastián | SAD V1 |
| C-02 | ADR-004: reparto de componentes entre Java y .NET, con criterio explícito de qué va en cada uno | 5 | Arquitecto + Sebastián | SAD V1 |
| C-03 | ADR-005: base de datos relacional frente a no relacional | 3 | Arquitecto + Sebastián | SAD V1 |
| C-04 | ADR-006: contenerización y estrategia de despliegue | 3 | Arquitecto + Sebastián | Infraestructura |
| C-05 | Arquitectura de Alto Nivel (HLD) con diagramas en Mermaid | 8 | Arquitecto + Sebastián | SAD V1 |
| C-06 | Drivers y killerss arquitectónicos del sistema | 5 | Arquitecto + Sebastián | SAD V1 |
| C-07 | Escenarios de calidad estímulo–respuesta alineados a ISO 25010:2023 | 5 | Arquitecto + Sebastián | SAD V1 |

## Épica B — Requerimientos y backlog

| ID | Tareea | Est. | Responsable | Entregable |
|---|---|---|---|---|
| C-08 | Reestructurar módulos funcionales para que coincidan uno a uno con las épicas. Gamificación pasa a característica de Gestión de Donantes | 5 | Alex + Tomás | SRS V2 / Backlog V2 |
| C-09 | Crear la Épica 0 de Gestión del Proyecto y trasladar allí el trabajo de infraestructura | 3 | Alex | Backlog V2 |
| C-10 | Reescribir el backlog desde cero: HU de negocio y HU técnicas, con prioridad de dos valores y clase | 13 | Alex + Tomás + Sara | Backlog V2 |
| C-11 | Actualizar los RNF a ISO 25010:2023: nueve características, priorizadas, con métrica por cada una | 8 | Alex | SRS V2 |
| C-12 | Añadir los requerimientos de seguridad faltantes: datos personales y sensibles bajo Ley 1581 | 5 | Alex | SRS V2 |
| C-13 | Incorporar las ocho restricciones de diseño como restricciones formales del SRS | 2 | Alex | SRS V2 |
| C-14 | Modelo de datos y contratos entre componentes | 8 | Alex + Arquitecto | DD V1 |

## Épica C — Herramientas, infraestructura y DevOps

| ID | Tarea | Est. | Responsable | Entregable |
|---|---|---|---|---|
| C-15 | Crear la organización de GitHub y definir la estructura multi-repositorio | 5 | Veli | Herramientas V2 |
| C-16 | Política de ramas con GitFlow, definiendo quién aprueba los Pull Requests en cada repositorio | 3 | Veli | Herramientas V2 |
| C-17 | Evaluar Jira frente a GitHub Projects: verificar si GitHub entrega velocity y burndown nativos. Si no, definir la integración | 5 | Veli | Herramientas V2 |
| C-18 | Incorporar Prometheus y Grafana al Tech Radar con su justificación | 3 | Veli | Herramientas V2 |
| C-19 | Revisar el ciclo completo de DevOps y verificar que hay herramienta por fase: plan, código, build, test, release, deploy, operate, monitor | 5 | Veli | Herramientas V2 |
| C-20 | Añadir herramientas de desarrollo del stack .NET al Tech Radar | 3 | Veli | Herramientas V2 |
| C-21 | Definir la configuración de los entornos Dev, QA y Producción | 5 | Veli | Infraestructura |

## Épica D — Gestión documental

| ID | Tarea | Est. | Responsable | Entregable |
|---|---|---|---|---|
| C-22 | Crear la plantilla base de documentación: logo, versionamiento, responsable, versión, tabla de contenido, control de cambios | 5 | Carlos | Herramientas V2 |
| C-23 | Plantilla de documento de Infraestructura | 3 | Carlos | Herramientas V2 |
| C-24 | Plantilla de documento de Pruebas | 3 | Carlos | Herramientas V2 |
| C-25 | Plantilla de documento de Arquitectura (SAD) | 3 | Carlos | Herramientas V2 |
| C-26 | Definir el sistema de gestión documental conforme a ISO 9001: repositorio único, control de acceso, versiones, responsables | 5 | Carlos | Herramientas V2 |
| C-27 | Migrar la documentación técnica de LaTeX a .MD con Mermaid | 8 | Todo el equipo | Todos |

## Épica E — Prototipos

| ID | Tarea | Est. | Responsable | Entregable |
|---|---|---|---|---|
| C-28 | Mockups de las pantallass principales del front | 8 | Damián | Prototipos |

---

**Total: 142 puntos** · A: 34 · B: 44 · C: 29 · D: 27 · E: 8

**Dependencias críticas:** C-01, C-02 y C-03 bloquean C-05, C-14 y C-28. C-15 y C-16 bloquean todo trabajo en repositorio. C-22 bloquea C-23, C-24 y C-25. ojito con esto


