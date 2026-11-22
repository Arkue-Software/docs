# RedVital - Documentación

Repositorio central de la documentación técnica y de gestión del proyecto **RedVital**, una plataforma para la gestión del ciclo de vida de la donación de sangre en Colombia.

Proyecto académico desarrollado por **Arkhé Software S.A.S.** para el curso de Arquitectura de Software Empresarial de la Pontificia Universidad Javeriana.

## Propósito

Este repositorio conserva la documentación viva y versionada del proyecto. Aquí se registran los requisitos, decisiones arquitectónicas, procesos de trabajo, pruebas e infraestructura. El código fuente se mantiene en un repositorio separado.

## Estado de Fase 1

La [revisión de las entregas y validaciones de Fase 1](./deliverables/revision-fase1.md) resume el estado de los Pull Requests, los resultados de CI y las decisiones que requieren confirmación del equipo.

## Repositorios y herramientas relacionadas

El código se reparte en un repositorio por componente. La rama de integración de todos es `develop`.

| Recurso | Propósito | Enlace |
|---|---|---|
| Servicio de Identidad | Sesiones, tokens de acceso, llaves públicas y auditoría de accesos (.NET 10). | [identity](https://github.com/Arkue-Software/identity) |
| Servicio Institucional | Territorios, instituciones, indicadores y bitácora del auditor (.NET 10). | [institutional](https://github.com/Arkue-Software/institutional) |
| Servicio de Campañas | Campañas y jornadas de donación (.NET 10). | [campaign](https://github.com/Arkue-Software/campaign) |
| Servicio de Donación | Donantes, donaciones, unidades, inventario y alertas (Java 25, Spring Boot). | [donation](https://github.com/Arkue-Software/donation) |
| Trabajador de Notificaciones | Entrega de avisos por correo a partir de eventos (Java 25, Spring Boot). | [notifications](https://github.com/Arkue-Software/notifications) |
| Frontend | Aplicación web de RedVital (React y Vite). | [frontend](https://github.com/Arkue-Software/frontend) |
| API Gateway y herramientas | Configuración de Apache APISIX y su plugin de autorización. | [tools](https://github.com/Arkue-Software/tools) |
| Bases de datos | Roles y migraciones SQL de cada servicio. | [databases](https://github.com/Arkue-Software/databases) |
| Despliegue | Composición por VM, Kafka, secretos, firewall y pruebas de sistema. | [Despliegue_RedVital](https://github.com/Arkue-Software/Despliegue_RedVital) |
| Repositorio inicial | Monorepositorio de la primera fase, archivado. Se conserva como historial. | [RedVital](https://github.com/Arkue-Software/RedVital) |
| Tablero de Jira | Gestión de épicas, historias de usuario, tareas técnicas, defectos y Sprints. | [Backlog de RedVital](https://arkhe-software.atlassian.net/jira/software/projects/SCRUM/boards/1/backlog) |
| Repositorio de documentación | Documentación técnica y de gestión del proyecto. | Este repositorio |

## Estructura del repositorio

```text
docs/
├── QA/testing/         Documento de pruebas (TD), plantillas e informes por Sprint
├── SDD/                Documento de diseño de software
├── SRS/                Especificación de requisitos
├── api/                Fuente LaTeX del DD V2.0, histórica
├── architecture/       SAD, ADRs, diagramas C4, atributos de calidad y estudios
├── context/            Investigación y contexto del dominio
├── data/               Documento de datos (DD) y modelos lógico y físico
├── deliverables/       Versiones entregadas en PDF y revisiones de fase
├── governance/         Lineamientos de documentación, políticas y herramientas
├── infrastructure/     Documento de infraestructura y operación
├── integration/api/    Resumen de contratos de API y eventos
├── processes/          GitFlow, backlog, KPI y actas
├── product/            Reservada para la visión de producto, sin contenido
├── requirements/       Requisitos funcionales, no funcionales y de seguridad
└── templates/          Plantillas controladas para los documentos
```

## Versiones vigentes

| Documento | Versión vigente |
|---|---|
| Especificación de requisitos | [SRS V5](./SRS/SRS_V5.md) |
| Documento de arquitectura | [SAD V5](./architecture/SAD/SAD_V5.md) |
| Documento de diseño | [SDD V4](./SDD/SDD_V4.md) |
| Documento de datos | [DD V5](./data/DD/DD_V5.md) |
| Infraestructura | [Infraestructura V4](./infrastructure/Infraestructura_V4_RedVital.md) |
| Documento de pruebas | [TD V2, revisión 2.2](./QA/testing/TD/RedVital_TD_V2.md). El SAD V5, el SDD V4 y el DD V5 citan el TD V3.1, que todavía no está en este repositorio. |
| Contratos de API y eventos | [Resumen vigente](./integration/api/redvital-contratos.md) |
