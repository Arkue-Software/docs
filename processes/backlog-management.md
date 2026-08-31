# Gestión del Backlog

| Campo | Información |
|---|---|
| Proyecto | Red Vital |
| Código | PROC-BM-001 |
| Versión | 1.0 |
| Estado | Borrador |
| Responsable | Product Owner |
| Revisado por | Scrum Master y equipo de desarrollo |
| Fecha de creación | AAAA-MM-DD |
| Próxima revisión | AAAA-MM-DD |

## 1. Objetivo

Establecer el proceso para crear, priorizar, refinar, estimar, ejecutar y dar seguimiento al backlog del producto Red Vital, asegurando que el trabajo del equipo esté alineado con las necesidades de los usuarios, los objetivos del proyecto y los criterios de calidad definidos.

## 2. Alcance

Este proceso aplica a todos los elementos del backlog del producto: épicas, características, historias de usuario, tareas técnicas, bugs y *spikes* de investigación.

Incluye desde la identificación de una necesidad hasta el cierre del elemento una vez se cumplan sus criterios de aceptación y sea validado en el sprint correspondiente.

## 3. Herramientas y repositorios

| Elemento | Herramienta o ubicación |
|---|---|
| Backlog y tablero de trabajo | GitHub Projects |
| Repositorio de código | GitHub |
| Documentación del proyecto | Repositorio de documentación en Markdown |
| Diseño y prototipos | [Enlace o herramienta] |
| Evidencias de pruebas | [Ruta o enlace] |
| Comunicación del equipo | [Herramienta definida por el equipo] |

## 4. Tipos de elementos del backlog

| Tipo | Descripción |
|---|---|
| Épica | Gran funcionalidad o línea de trabajo que agrupa varias historias de usuario. |
| Característica | Capacidad específica del producto que aporta valor y puede dividirse en historias. |
| Historia de usuario | Necesidad funcional expresada desde la perspectiva de un usuario o actor. |
| Tarea técnica | Trabajo necesario para soportar una historia, arquitectura, infraestructura o documentación. |
| Bug | Error identificado en una funcionalidad, servicio o componente existente. |


## 5. Estructura de una historia de usuario

Cada historia de usuario debe contener, como mínimo:

| Campo | Descripción |
|---|---|
| ID | Identificador único. Ejemplo: HU-001. |
| Título | Nombre breve y descriptivo. |
| Épica | Épica a la cual pertenece. |
| Clase | Historia de usuario, tarea, bug o spike. |
| Descripción | Formato: “Como [rol], quiero [acción], para [beneficio]”. |
| Criterios de aceptación | Condiciones verificables que determinan cuándo la historia está terminada. |
| Prioridad | Valor de negocio y urgencia. |
| Estimación | Puntos de historia o esfuerzo acordado por el equipo. |
| Sprint | Sprint asignado, cuando corresponda. |
| Responsable | Integrante asignado para liderar su desarrollo. |
| Dependencias | Elementos, decisiones o recursos necesarios para iniciar o terminarla. |
| Evidencias | Enlaces a pull requests, pruebas, documentación o prototipos relacionados. |

### Plantilla de historia de usuario

```text
ID: HU-XXX
Título: [Nombre de la necesidad]
Épica: [EP-XXX – Nombre]
Clase: Historia de usuario
Prioridad: [Alta / Media / Baja]
Valor de negocio: [Alto / Medio / Bajo]
Estimación: [X puntos]
Sprint: [Sprint X]

Como [rol],
quiero [acción o necesidad],
para [beneficio esperado].

Criterios de aceptación:
- Dado que [contexto], cuando [acción], entonces [resultado verificable].
- Dado que [contexto], cuando [acción], entonces [resultado verificable].

Dependencias:
- [Dependencia o “No aplica”]

Evidencias:
- [Enlace a PR, prueba, prototipo o documento]
