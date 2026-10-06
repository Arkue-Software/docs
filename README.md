# RedVital - Documentación

Repositorio central de la documentación técnica y de gestión del proyecto **RedVital**, una plataforma para la gestión del ciclo de vida de la donación de sangre en Colombia.

Proyecto académico desarrollado por **Arkhé Software S.A.S.** para el curso de Arquitectura de Software Empresarial de la Pontificia Universidad Javeriana.

## Propósito

Este repositorio conserva la documentación viva y versionada del proyecto. Aquí se registran los requisitos, decisiones arquitectónicas, procesos de trabajo, pruebas e infraestructura. El código fuente se mantiene en un repositorio separado.

## Estado de Fase 1

La [revisión de las entregas y validaciones de Fase 1](./deliverables/revision-fase1.md) resume el estado de los Pull Requests, los resultados de CI y las decisiones que requieren confirmación del equipo.

## Repositorios y herramientas relacionadas

| Recurso | Propósito | Enlace |
|---|---|---|
| Repositorio de integración | Contratos OpenAPI, documentación de entrada y configuración compartida. | [RedVital](https://github.com/Arkue-Software/RedVital) |
| Frontend | Prototipo web de RedVital. | [frontend](https://github.com/Arkue-Software/frontend) |
| Servicio de Identidad | API de sesiones, tokens y JWKS. | [identity-service](https://github.com/Arkue-Software/identity-service) |
| Servicio de Campañas | API de campañas y reservas. | [campaign-service](https://github.com/Arkue-Software/campaign-service) |
| Servicio de Donación | Servicio de ciclo de vida de donaciones. | [donation-service](https://github.com/Arkue-Software/donation-service) |
| Servicio Institucional | Servicio de instituciones y jerarquía territorial. | [Institutional-network-service](https://github.com/Arkue-Software/Institutional-network-service) |
| API Gateway | Enrutamiento y politicas de entrada. | [api-gateway-apisix](https://github.com/Arkue-Software/api-gateway-apisix) |
| Bases de datos | Instancias y migraciones SQL por servicio. | [databases](https://github.com/Arkue-Software/databases) |
| Tablero de Jira | Gestión de épicas, historias de usuario, tareas técnicas, defectos y Sprints. | [Backlog de RedVital](https://arkhe-software.atlassian.net/jira/software/projects/SCRUM/boards/1/backlog) |
| Repositorio de documentación | Documentación técnica y de gestión del proyecto. | Este repositorio |

## Estructura del repositorio

```text
red-vital-documentation/
├── architecture/       Documento de arquitectura, ADRs y diagramas
├── context/            Investigación y contexto del dominio
├── deliverables/       Versiones finales entregadas en PDF u otro formato
├── infrastructure/     Documentación de infraestructura y operación
├── processes/          Procesos de trabajo, actas y evaluación de Sprints
├── requirements/       Requisitos funcionales y no funcionales
├── templates/          Plantillas controladas para los documentos
└── testing/            Planes, casos, evidencias e informes de pruebas