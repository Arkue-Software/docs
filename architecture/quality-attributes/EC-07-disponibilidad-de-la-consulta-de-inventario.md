## EC-07 — Disponibilidad de la consulta de inventario

*Atributo de calidad:* Fiabilidad

*HU relacionadas:* RF-07, RF-18 — M4

*Subcaracterística:* Disponibilidad

*Prioridad:* Alta

### Escenario estímulo–respuesta

| Elemento | Descripción |
|---|---|
| Fuente del estímulo | Administrador de banco (U4) u operador (U3) durante la jornada operativa |
| Estímulo | Consulta el inventario de su institución para decidir si atiende una solicitud o si escala a una transferencia |
| Contexto | Ambiente de QA en operación, con un contenedor reiniciándose por un despliegue o por una caída |
| Artefacto afectado | Servicio de Donación, API Gateway, base de datos, orquestación de contenedores |
| Respuesta | La consulta responde. Si el contenedor del servicio cae, la política de reinicio lo levanta y el sondeo de salud lo devuelve a servicio sin que nadie intervenga |
| Medida de respuesta | Disponibilidad igual o superior al 99% sobre el endpoint de salud, medida en la ventana de evaluación del sprint. Un contenedor caído vuelve a estado saludable en 60 segundos o menos. Cero reinicios que requieran intervención manual |

### Verificación

- La disponibilidad se mide, no se estima: la serie proviene de Prometheus y se publica en el tablero de Grafana.
- La prueba consiste en detener el contenedor del Servicio de Donación durante la consulta y comprobar que vuelve solo.
- Cada contenedor expone un sondeo de salud que verifica también su dependencia de base de datos, no solo que el proceso esté vivo. Un servicio que responde pero no alcanza su base no está disponible.
- El objetivo del 99% corresponde a un proyecto académico con infraestructura de nivel gratuito. No es un compromiso de servicio de producción y así debe presentarse al cliente.
