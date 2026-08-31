## EC-04 — Integridad del ciclo de vida de la unidad

*Atributo de calidad:* Seguridad

*HU relacionadas:* RF-03, RF-04, RF-05, RF-06 — M3

*Subcaracterística:* Integridad

*Prioridad:* Alta

### Escenario estímulo–respuesta

| Elemento | Descripción |
|---|---|
| Fuente del estímulo | Operador de banco (U3), o un cliente que invoca la API directamente sin pasar por la interfaz |
| Estímulo | Intenta llevar una unidad a un estado que no corresponde a su estado actual: devolver a disponible una unidad ya desechada, transferir una unidad no apta, o alterar su origen ya registrado |
| Contexto | Sistema en operación normal |
| Artefacto afectado | Máquina de estados de la unidad en el Servicio de Donación, base de datos, historial de trazabilidad |
| Respuesta | El sistema rechaza la transición, conserva el estado anterior sin modificarlo, y registra el intento. El historial de trazabilidad solo admite anexar eventos: nunca se edita ni se borra lo ya escrito |
| Medida de respuesta | Cero transiciones inválidas aceptadas sobre la matriz completa de pares estado origen–estado destino. Ninguna unidad queda en un estado no previsto. Cero operaciones de modificación o borrado expuestas sobre el historial. El origen y la fecha de captación de una unidad no son modificables después de su registro |

### Verificación

- La matriz de transiciones válidas se documenta en el diseño del dominio y se prueba de forma exhaustiva, no por muestreo: el número de pares es pequeño y comprobarlos todos es viable.
- La restricción se aplica en el servicio, no solo en la interfaz, porque el estímulo contempla la llamada directa a la API.
- El historial de solo anexado es la base de la trazabilidad exigida por RF-04 y de la reconstrucción que hace el auditor en EC-05.
