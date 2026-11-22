# Architecture Decision Records (ADRs)

Los **Architecture Decision Records (ADRs)** registran las decisiones arquitectónicas relevantes tomadas durante el desarrollo de **Red Vital**.

Cada ADR conserva el contexto de una decisión, las alternativas consideradas, la decisión adoptada y sus consecuencias. De esta manera, el equipo puede mantener trazabilidad sobre la evolución de la arquitectura y comprender por qué se adoptaron determinadas soluciones técnicas.

---

## Propósito

Los ADRs permiten:

- Mantener trazabilidad de las decisiones arquitectónicas.
- Registrar el contexto en el que se tomó cada decisión.
- Documentar las alternativas evaluadas.
- Justificar técnicamente las decisiones adoptadas.
- Identificar las consecuencias de cada decisión.
- Relacionar decisiones con atributos de calidad, estudios técnicos, diagramas y otros artefactos del proyecto.
- Mantener un historial de la evolución de la arquitectura de Red Vital.

---

## Estructura de los ADRs

Cada ADR debe contener, como mínimo:

1. **Estado**
2. **Contexto**
3. **Opciones consideradas**
4. **Decisión**
5. **Consecuencias**

Cuando corresponda, también puede incluir:

- drivers arquitectónicos relacionados;
- atributos de calidad involucrados;
- riesgos y restricciones;
- estudios o benchmarks asociados;
- diagramas relacionados;
- contratos o especificaciones relacionadas;
- referencias a otros ADRs.

---

## Estados

Los ADRs pueden encontrarse en alguno de los siguientes estados:

| Estado | Descripción |
|---|---|
| `Propuesto` | La decisión se encuentra en evaluación. |
| `Aceptado` | La decisión fue aprobada y se encuentra vigente. |
| `Rechazado` | La alternativa fue evaluada, pero no fue adoptada. |
| `Reemplazado` | La decisión fue sustituida por un ADR posterior. |
| `Obsoleto` | La decisión ya no aplica a la arquitectura actual. |

Los ADRs que hayan sido reemplazados u obsoletos no deben eliminarse, ya que forman parte del historial arquitectónico del proyecto.

---

## Índice de ADRs

La columna «Estado en el documento» copia lo que declara cada ADR. La columna «En `develop`» dice si la decisión está implementada en la rama de integración de los repositorios de código.

| ADR | Decisión | Estado en el documento | En `develop` |
|---|---|---|---|
| [ADR-001](./ADR-001-seleccion-del-stack-tecnologico.md) | Selección del stack tecnológico | Aceptada | Implementada |
| [ADR-007](./ADR-007-api-gateway-yarp.md) | API Gateway como punto único de entrada mediante YARP | Propuesta | No implementada: el gateway vigente es Apache APISIX (ADR-019) |
| [ADR-008](./ADR-008-token-sesion-firma-asimetrica.md) | Token de acceso firmado asimétricamente y sesión de renovación rotatoria | Aceptado | Implementada |
| [ADR-009](./ADR-009-linea-plataforma-java-25.md) | Línea de plataforma Java 25 para los componentes Java | Propuesta | Implementada en Donación y Notificaciones |
| [ADR-010](./ADR-010-bitacora-auditoria-revisado.md) | Bitácora de auditoría distribuida con consulta compuesta | Aceptada | Implementada (EV-10 y consulta del auditor en Institucional) |
| [ADR-011](./ADR-011-matriz-autorizacion.md) | Matriz de autorización por grupo de operaciones | Aceptada | Implementada en APISIX y revalidada en cada servicio |
| [ADR-012](./ADR-012-clasificacion-extensibilidad.md) | Clasificación de la extensibilidad del dominio | Aceptada | No aplica: decisión de clasificación |
| [ADR-013](./ADR-013-Caddy%20como%20herramienta%20de%20proxy%20de%20borde.md) | Caddy como proxy de borde | Aceptada | Implementada |
| [ADR-017](./ADR-017-arquitectura-de-microservicios.md) | Adopción de una arquitectura de microservicios | Propuesta | Implementada: cinco servicios con base propia |
| [ADR-018](./ADR-018-estrategia-de-comunicacion-asincrona.md) | Comunicación entre microservicios con Apache Kafka | Propuesta | Implementada: bandeja de salida y Kafka 4.3.1 |
| [ADR-019](./ADR-API-GATEWAY.md) | Elección de API Gateway (Apache APISIX) | Sin definir: conserva las opciones de la plantilla | Implementada |
| [Sin número](./ADR-Gatling-seleccion-pruebas-carga.md) | Gatling para pruebas de carga y estrés | Propuesta para revisión | Usada en las pruebas LT-01 a LT-04 |

### Pendientes de este índice

Estas diferencias entre los documentos y lo implementado requieren decisión de la Mesa de Arquitectura; este índice solo las registra.

- ADR-009, ADR-017, ADR-018 y ADR-019 están implementadas pero no figuran como aceptadas. El acta `MIN-2026-ARCH-ADR-017-018` sigue con las decisiones pendientes de aprobación.
- ADR-007 no declara que fue reemplazada por ADR-019.
- El archivo de ADR-019 no sigue la convención de nombres y el de ADR-013 conserva campos de la plantilla sin diligenciar.
- El ADR de Gatling no tiene número.
- Faltan en el repositorio los ADR-002 a ADR-006 y ADR-014 a ADR-016, que otros documentos citan. El SRS cita la descomposición en cinco servicios como ADR-014 y el SAD como ADR-017.

---

## Convención de nombres

Los ADRs deben seguir la siguiente convención:

`ADR-XXX-nombre-de-la-decision.md`

Donde:

- `ADR` identifica el tipo de documento.
- `XXX` corresponde al número consecutivo de la decisión.
- El nombre describe de manera breve la decisión arquitectónica.
- Las palabras del nombre se separan mediante guiones.

La numeración asignada a un ADR no debe reutilizarse, incluso si posteriormente la decisión es reemplazada u obsoleta.

---

## Relación con otros artefactos

Los ADRs forman parte de la documentación de arquitectura de Red Vital y pueden estar relacionados con:

- el SAT;
- el SAD;
- el SDD;
- los atributos y escenarios de calidad;
- los estudios técnicos;
- los diagramas de arquitectura;
- el diseño de datos;
- los contratos de integración;
- la infraestructura;
- los requisitos y restricciones del sistema.

Los estudios técnicos y benchmarks proporcionan evidencia para evaluar alternativas, mientras que el ADR registra formalmente la decisión arquitectónica adoptada.

Por esta razón, la información detallada de un estudio no debe duplicarse dentro del ADR. Cuando exista un estudio relacionado, el ADR debe incluir una referencia al documento correspondiente.

---

## Mantenimiento

Este índice debe actualizarse cuando:

- se cree un nuevo ADR;
- cambie el estado de un ADR;
- un ADR sea reemplazado;
- se agregue una relación con otro artefacto arquitectónico;
- se incorpore al repositorio un ADR previamente faltante.

Las decisiones arquitectónicas aceptadas deben mantenerse en el repositorio aunque posteriormente cambien, con el fin de conservar la trazabilidad histórica de la arquitectura.

---

## Navegación

- [Volver a Arquitectura](../README.md)
- [Diagramas de arquitectura](../diagrams/README.md)
- [Estudios arquitectónicos](../studies/README.md)
- [Atributos y escenarios de calidad](../quality-attributes/README.md)