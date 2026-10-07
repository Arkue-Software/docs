## EC-02 — Confidencialidad de la causa clínica

*Atributo de calidad:* Seguridad

*HU relacionadas:* RF-05, RF-04, RF-06 — M3

*Subcaracterística:* Confidencialidad

*Prioridad:* Alta

### Escenario estímulo–respuesta

| Elemento | Descripción |
|---|---|
| Fuente del estímulo | Cualquier usuario con acceso al ciclo de vida de la unidad —operador (U3), administrador de banco (U4), auditor (U7)— o un cliente que consulte la API directamente |
| Estímulo | Consulta una unidad marcada como no apta, o su historial de trazabilidad, por la interfaz web o por el contrato de la API |
| Contexto | Sistema en operación normal, usuario autenticado y dentro de su jurisdicción |
| Artefacto afectado | Modelo de dominio del Servicio de Donación, base de datos, contrato de la API, aplicación web, bitácora de auditoría |
| Respuesta | El sistema devuelve el estado de aptitud y la trazabilidad de la unidad. No devuelve la causa clínica porque el dato no existe en el modelo, ni en el esquema de la base, ni en el contrato |
| Medida de respuesta | Cero campos de causa clínica, diagnóstico o resultado de tamizaje en el esquema, en el contrato OpenAPI y en cualquier respuesta. Revisión del 100% de los endpoints de M3 y M4. Ningún registro de bitácora, mensaje de error o traza contiene información diagnóstica |

### Verificación

- La confidencialidad se garantiza en el **modelo**, no por filtrado en la capa de presentación. Un dato que no se almacena no puede filtrarse por un descuido posterior.
- Se revisan también los mensajes de error y las trazas de diagnóstico: un texto como «rechazada por reactividad» viola el escenario igual que un campo.
- El rechazo de despacho de una unidad no apta (EC-12) debe redactarse sin motivo clínico.
- Fundamento: Ley 1581 de 2012, que clasifica los datos de salud como sensibles, y la restricción de diseño RD-08.
