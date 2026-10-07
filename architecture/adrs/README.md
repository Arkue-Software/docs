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

Actualmente, la documentación arquitectónica de Red Vital cuenta con los siguientes ADRs:

| ADR |
|---|
| ADR-001 |
| ADR-007 |
| ADR-008 |
| ADR-009 |
| ADR-010 |
| ADR-011 |
| ADR-012 |
| ADR-013 |
| ADR-017 |
| ADR-018 |
| ADR-API-GATEWAY |


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