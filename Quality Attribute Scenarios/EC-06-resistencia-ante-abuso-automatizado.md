## EC-06 — Resistencia ante abuso automatizado

*Atributo de calidad:* Seguridad

*HU relacionadas:* RF-01, RF-07 — M1, M4

*Subcaracterística:* Resistencia

*Prioridad:* Media

### Escenario estímulo–respuesta

| Elemento | Descripción |
|---|---|
| Fuente del estímulo | Agente externo no autenticado |
| Estímulo | Envía peticiones repetidas contra el registro anónimo, o recorre identificadores de unidad de forma sistemática para enumerar el inventario |
| Contexto | Sistema desplegado en el ambiente de QA, expuesto en red, sin sesión válida |
| Artefacto afectado | API Gateway, endpoints públicos del Servicio de Donación |
| Respuesta | El gateway limita la tasa de peticiones por origen y responde con rechazo por exceso de solicitudes. El tráfico legítimo sigue siendo atendido. Los identificadores de unidad no son adivinables, de modo que recorrerlos no produce información |
| Medida de respuesta | Superado el umbral definido de peticiones por minuto y por origen, el 100% de las peticiones excedentes se rechaza sin consumir recursos del servicio. Los identificadores de unidad son valores no secuenciales. El tiempo de respuesta del tráfico legítimo no se degrada más de un 20% durante la prueba |

### Verificación

- El escenario es de resistencia, no de disponibilidad ante ataque masivo: el objetivo es que el abuso simple no degrade el servicio ni permita inferir el tamaño ni el contenido del inventario.
- Los identificadores no secuenciales protegen además a EC-01: un identificador adivinable permitiría deducir la existencia de unidades de otra jurisdicción aunque la consulta se rechace.
- El registro anónimo está entre los objetivos protegidos porque es el único endpoint que crea datos sin sesión previa.
- El umbral concreto se fija cuando exista medición real de tráfico en QA, y queda registrado en la configuración del gateway, no en el código.
