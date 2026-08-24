# Marco de KPIs y evaluación de Sprint

> **Documento de proceso**  
> **Proyecto:** RedVital  
> **Versión:** 2.0  
> **Responsable:** Scrum Master / Project Lead  
> **Documento relacionado:** [`sprint-evaluation-template.md`](../templates/processes/sprint-evaluation-template.md)

## Propósito

Este documento define qué se evalúa en cada Sprint de RedVital, cómo se mide y cuáles son los criterios de interpretación. Incluye indicadores de código y funcionalidad, arquitectura, proceso Scrum y flujo de trabajo del equipo.

El propósito no es acumular métricas, sino contar con una base consistente para comparar Sprints, identificar tendencias a tiempo y sustentar decisiones con datos.

> Para elaborar el informe de cada Sprint debe utilizarse la [plantilla de evaluación de Sprint](../templates/processes/sprint-evaluation-template.md). Este documento no se duplica por Sprint: se mantiene y actualiza como marco de referencia.

## 1. Política de entrega interna

La entrega oficial de la materia es el jueves. Para permitir una revisión previa por parte del líder del equipo, se establece el siguiente corte interno:

- **Martes, 6:00 p. m.:** versión borrador completa, incluyendo código, documentación y KPIs preliminares, lista para revisión interna.
- **Miércoles:** ventana de ajustes según la retroalimentación recibida.
- **Jueves, antes de la hora límite:** entrega oficial final.

## 2. Escala de desempeño

Cada KPI individual se interpreta con una escala de 1 a 5.

| Nivel | Significado |
|---:|---|
| 1 | Muy por debajo de lo esperado. |
| 2 | Por debajo de lo esperado. |
| 3 | Cumple lo esperado: realizó lo que se le pidió. |
| 4 | Por encima de lo esperado: tomó iniciativa para mejorar el trabajo o resolvió algo adicional a lo asignado. |
| 5 | Muy por encima de lo esperado: aporte con impacto claro más allá de su alcance asignado. |

El nivel 3 representa el cumplimiento esperado. Los niveles 1 y 2 reflejan distintos grados de incumplimiento; los niveles 4 y 5 representan iniciativa o aporte adicional verificable.

## 3. KPIs de código y funcionalidad

| KPI | Definición | Frecuencia | Fuente de datos |
|---|---|---|---|
| Cumplimiento del Sprint | Grado de cumplimiento de las historias comprometidas en el Sprint. | Semanal | Tablero de Jira |
| Cobertura de pruebas | Porcentaje del código cubierto por pruebas automatizadas. | Semanal | Reporte de CI/CD |
| Densidad de defectos | Bugs reportados por historia entregada tras el despliegue. | Por Sprint | Backlog de defectos |
| Calidad de code review | Pull Requests aprobados sin retrabajo mayor después de la revisión. | Semanal | Repositorio Git |

### 3.1 Cumplimiento del Sprint

**Fórmula:** historias completadas / historias comprometidas.

| Nivel | Criterio |
|---:|---|
| 1 | Completó menos del 60 % de lo comprometido. |
| 2 | Completó entre el 60 % y el 84 % de lo comprometido. |
| 3 | Completó entre el 85 % y el 100 % de lo comprometido. |
| 4 | Completó el 100 % y absorbió trabajo adicional no planeado. |
| 5 | Completó el 100 % y tomó una iniciativa relevante fuera de su alcance. |

### 3.2 Cobertura de pruebas

**Fórmula:** líneas o ramas cubiertas / total de líneas o ramas.

| Nivel | Criterio |
|---:|---|
| 1 | Menos del 40 % de cobertura. |
| 2 | Entre el 40 % y el 59 % de cobertura. |
| 3 | Entre el 60 % y el 80 % de cobertura. |
| 4 | Entre el 80 % y el 90 % de cobertura, con casos adicionales no exigidos. |
| 5 | Más del 90 % de cobertura y mejora verificable de la estrategia de pruebas. |

### 3.3 Densidad de defectos

**Fórmula:** bugs reportados / historias entregadas.

| Nivel | Criterio |
|---:|---|
| 1 | Más de 0,6 bugs por historia. |
| 2 | Entre 0,4 y 0,6 bugs por historia. |
| 3 | Entre 0,2 y 0,4 bugs por historia. |
| 4 | Menos de 0,2 bugs por historia y evidencia de revisión proactiva. |
| 5 | Cero defectos y mejora verificable del proceso de calidad. |

### 3.4 Calidad de code review

**Fórmula:** Pull Requests aprobados sin cambios mayores / total de Pull Requests.

| Nivel | Criterio |
|---:|---|
| 1 | Menos del 60 % aprobados sin retrabajo mayor. |
| 2 | Entre el 60 % y el 74 % aprobados sin retrabajo mayor. |
| 3 | Entre el 75 % y el 90 % aprobados sin retrabajo mayor. |
| 4 | Más del 90 % y comentarios que mejoraron el diseño de otros integrantes. |
| 5 | Más del 90 % e identificación proactiva de un riesgo que evitó retrabajo mayor. |

## 4. KPIs de arquitectura

| KPI | Definición | Frecuencia | Fuente de datos |
|---|---|---|---|
| Cobertura de C4 | Componentes o contenedores nuevos o modificados con diagramas C4 actualizados. | Por Sprint | Repositorio de documentación |
| Cumplimiento de ADRs | Decisiones arquitectónicas relevantes registradas como ADR. | Por Sprint | Carpeta de ADRs |
| Gestión de deuda técnica | Crecimiento neto del backlog de deuda técnica. | Por Sprint | Backlog técnico en Jira |
| Seguridad por diseño | Historias con checklist de seguridad aplicado antes de iniciar desarrollo. | Por Sprint | Checklist de Definition of Ready |

### 4.1 Cobertura de C4

**Fórmula:** elementos documentados / elementos nuevos o modificados.

| Nivel | Criterio |
|---:|---|
| 1 | Menos del 50 % documentado. |
| 2 | Entre el 50 % y el 79 % documentado. |
| 3 | Entre el 80 % y el 100 % documentado. |
| 4 | 100 % documentado y actualización de diagramas atrasados de Sprints anteriores. |
| 5 | 100 % documentado y mejora de la convención de diagramación del equipo. |

### 4.2 Cumplimiento de ADRs

**Fórmula:** decisiones documentadas / decisiones relevantes tomadas.

| Nivel | Criterio |
|---:|---|
| 1 | Menos del 50 % documentado. |
| 2 | Entre el 50 % y el 79 % documentado. |
| 3 | Entre el 80 % y el 100 % documentado. |
| 4 | 100 % documentado, con alternativas no obvias evaluadas rigurosamente. |
| 5 | 100 % documentado y liderazgo de una discusión de decisión compleja en beneficio del equipo. |

### 4.3 Gestión de deuda técnica

**Fórmula:** ítems nuevos de deuda técnica - ítems resueltos o priorizados.

| Nivel | Criterio |
|---:|---|
| 1 | Crecimiento neto mayor a 4 ítems. |
| 2 | Crecimiento neto entre 3 y 4 ítems. |
| 3 | Crecimiento neto entre 0 y 2 ítems. |
| 4 | Reducción neta del backlog de deuda técnica. |
| 5 | Reducción neta lograda por iniciativa propia, resolviendo ítems no asignados. |

### 4.4 Seguridad por diseño

**Fórmula:** historias con checklist aplicado / total de historias iniciadas.

| Nivel | Criterio |
|---:|---|
| 1 | Menos del 50 % de historias con checklist aplicado. |
| 2 | Entre el 50 % y el 79 % con checklist aplicado. |
| 3 | Entre el 80 % y el 100 % con checklist aplicado. |
| 4 | 100 % y detección de un riesgo de seguridad no cubierto por el checklist. |
| 5 | 100 % y propuesta de mejora al checklist de seguridad. |

## 5. KPIs de proceso y equipo

| KPI | Definición | Frecuencia | Fuente de datos |
|---|---|---|---|
| Cumplimiento de ceremonias | Ceremonias Scrum realizadas frente a las planificadas. | Semanal | Calendario y actas |
| Puntualidad de entrega interna | Entregas completas antes del corte interno definido. | Por Sprint | Registro de entregas |
| Participación en Mesa de Arquitectura | Intervención sustantiva registrada en el acta. | Por Sprint | Acta de Mesa de Arquitectura |
| Cierre de acciones de retrospectiva | Acciones de mejora de la retrospectiva anterior cerradas en el Sprint actual. | Por Sprint | Tablero de acciones |

### 5.1 Cumplimiento de ceremonias

**Fórmula:** ceremonias realizadas / ceremonias planificadas.

| Nivel | Criterio |
|---:|---|
| 1 | Menos del 60 % de asistencia o realización. |
| 2 | Entre el 60 % y el 79 %. |
| 3 | Entre el 80 % y el 100 %. |
| 4 | 100 % con preparación previa visible, por ejemplo, backlog refinado antes del Planning. |
| 5 | 100 % y mejora de la dinámica de una ceremonia por iniciativa propia. |

### 5.2 Puntualidad de entrega interna

**Fórmula:** Sprints con entrega antes del corte / total de Sprints.

| Nivel | Criterio |
|---:|---|
| 1 | Menos del 50 % de Sprints entregados a tiempo. |
| 2 | Entre el 50 % y el 74 % de Sprints entregados a tiempo. |
| 3 | Entre el 75 % y el 100 % de Sprints entregados a tiempo. |
| 4 | 100 % con margen consistente antes del corte interno. |
| 5 | 100 % y apoyo verificable a otro integrante para cumplir su corte. |

### 5.3 Participación en Mesa de Arquitectura

**Fórmula:** intervención registrada en el acta de la sesión.

| Nivel | Criterio |
|---:|---|
| 1 | Sin intervención registrada. |
| 2 | Intervención mínima, sin aporte sustantivo. |
| 3 | Al menos una intervención sustantiva. |
| 4 | Más de una intervención relevante durante la sesión. |
| 5 | Lideró o preparó una propuesta de decisión llevada a la mesa. |

### 5.4 Cierre de acciones de retrospectiva

**Fórmula:** acciones cerradas / acciones abiertas.

| Nivel | Criterio |
|---:|---|
| 1 | Menos del 30 % cerradas. |
| 2 | Entre el 30 % y el 49 % cerradas. |
| 3 | Entre el 50 % y el 70 % cerradas. |
| 4 | Más del 70 % cerradas. |
| 5 | 100 % cerradas y propuesta de una acción adicional de mejora. |

## 6. Métricas ágiles del equipo

Estas métricas miden el flujo del equipo completo, no el desempeño individual. Se interpretan frente al comportamiento histórico del mismo equipo, no contra un umbral absoluto.

| Métrica | Qué mide | Fuente de datos |
|---|---|---|
| Burndown | Trabajo restante frente al tiempo transcurrido en el Sprint. Permite identificar riesgo de no completar lo comprometido. | Tablero Scrum |
| Velocity | Promedio histórico de puntos o historias completadas por Sprint. Permite planear con datos reales de capacidad. | Sprints cerrados en Jira |
| CFD | Cantidad de tareas en cada estado del flujo a lo largo del tiempo. Permite identificar cuellos de botella. | Historial de estados en Jira |
| Lifecycle o tiempo de ciclo | Tiempo que tarda una tarea desde que se inicia hasta que termina. Mide previsibilidad del flujo. | Fechas de cambio de estado en Jira |

| Métrica | Por debajo de lo esperado | Esperado | Por encima de lo esperado |
|---|---|---|---|
| Burndown | Trabajo restante estancado o creciente frente a la línea ideal. | Sigue razonablemente la línea ideal y llega cerca de cero al final del Sprint. | Termina consistentemente antes de tiempo, sin subestimación sistemática. |
| Velocity | Decreciente o con alta variabilidad entre Sprints. | Estable dentro del rango histórico del equipo. | Crecimiento sostenido sin sobrecompromiso. |
| CFD | Una etapa concentra trabajo de forma creciente. | Franjas de ancho estable, sin acumulación relevante. | Flujo que mejora de forma medible Sprint a Sprint. |
| Lifecycle | Tareas tardan notablemente más que el histórico. | Dentro del rango histórico esperado. | Reducción sostenida del tiempo de ciclo sin sacrificar calidad. |

## 7. Uso de la plantilla de evaluación de Sprint

Al cierre de cada Sprint, el Scrum Master o Project Manager debe crear un informe a partir de:

[`templates/processes/sprint-evaluation-template.md`](../templates/processes/sprint-evaluation-template.md)

El informe real debe guardarse en una carpeta como:

```text
processes/sprint-evaluations/sprint-1-evaluation.md