## EC-15 — Tiempo de respuesta de la consulta de inventario

*Atributo de calidad:* Eficiencia de desempeño

*HU relacionadas:* RF-07, RF-13 — M4, M6

*Subcaracterística:* Comportamiento temporal

*Prioridad:* Alta

### Escenario estímulo–respuesta

| Elemento | Descripción |
|---|---|
| Fuente del estímulo | Administrador de banco (U4), operador (U3) o coordinador territorial (U5) |
| Estímulo | Solicita el inventario de su institución o de su jurisdicción, con filtro por componente sanguíneo y tipo de sangre |
| Contexto | Operación normal en el ambiente de QA, con el volumen sintético de referencia cargado |
| Artefacto afectado | Aplicación web, API Gateway, Servicio de Donación, base de datos |
| Respuesta | El sistema entrega el resultado completo, con las alertas de umbral y de vencimiento incluidas |
| Medida de respuesta | Percentil 95 igual o menor a 3 segundos, medido en el gateway sobre el conjunto sintético de referencia. Percentil 99 igual o menor a 5 segundos. La medición se toma de Prometheus y se publica en Grafana |

### Verificación

- El requisito original habla de «menos de 3 segundos en condiciones normales». El escenario lo traduce a percentiles porque un promedio esconde justamente los casos lentos que el usuario recuerda.
- Se mide en el gateway y no dentro del servicio: lo que importa es el tiempo que percibe quien consulta, incluida la red y el enrutamiento.
- El volumen sintético de referencia se declara y se versiona. Una medida de desempeño sin volumen declarado no es comparable entre sprints.
- La medición es un dato observado, no una estimación del equipo. Este escenario es una de las razones por las que Prometheus y Grafana entran en el Sprint 4.
