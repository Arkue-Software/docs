## EC-22 — Evolución del contrato entre servicios

*Atributo de calidad:* Compatibilidad

*HU relacionadas:* HT-15 — contratos entre componentes

*Subcaracterística:* Interoperabilidad

*Prioridad:* Alta

### Escenario estímulo–respuesta

| Elemento | Descripción |
|---|---|
| Fuente del estímulo | El equipo de uno de los dos servicios |
| Estímulo | Publica una versión nueva del contrato que añade un campo a una respuesta existente |
| Contexto | El otro servicio y la aplicación web están en ejecución con la versión anterior del contrato |
| Artefacto afectado | Contrato OpenAPI versionado, Servicio de Donación, Servicio Institucional, aplicación web |
| Respuesta | El consumidor sigue operando sin cambio alguno. Un cambio que sí rompa la compatibilidad obliga a versión mayor y a un periodo en el que ambas versiones conviven |
| Medida de respuesta | Cero fallos del consumidor ante un cambio compatible. El 100% de los cambios incompatibles se publica con incremento de versión mayor, conforme a versionado semántico. El contrato vive versionado en el repositorio y la verificación de integración continua lo comprueba en cada Pull Request. Cero llamadas entre servicios fuera del contrato |

### Verificación

- La restricción de fondo es RD-01: los dos servicios no comparten base de datos ni se llaman por fuera del contrato. Este escenario es la forma de comprobarlo de manera continua y no solo en la revisión de arquitectura.
- El contrato es independiente del lenguaje porque un extremo es Java y el otro .NET. Un contrato expresado en tipos de un solo lenguaje no sirve aquí.
- El consumidor ignora los campos que no conoce. Un cliente que falla ante un campo desconocido convierte cualquier adición en un cambio incompatible.
- Se prueba con el consumidor de la versión anterior ya desplegado, no recompilándolo contra el contrato nuevo, que es donde el problema se esconde.
