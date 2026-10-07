## EC-08 — Aislamiento del fallo del servicio par

*Atributo de calidad:* Fiabilidad

*HU relacionadas:* RF-07, RF-08, RF-10 — M2, M4

*Subcaracterística:* Tolerancia a fallos

*Prioridad:* Alta

### Escenario estímulo–respuesta

| Elemento | Descripción |
|---|---|
| Fuente del estímulo | Fallo de infraestructura o despliegue en curso |
| Estímulo | El Servicio Institucional deja de responder mientras el Servicio de Donación necesita datos de campaña o de jerarquía territorial |
| Contexto | Sistema en operación, un solo servicio caído |
| Artefacto afectado | Cliente HTTP del Servicio de Donación, API Gateway, aplicación web |
| Respuesta | La llamada expira en un plazo acotado en lugar de quedar esperando. Las funciones que no dependen del servicio caído siguen operando con normalidad. La interfaz indica con precisión qué quedó no disponible, sin exponer detalle técnico ni dejar la pantalla en blanco |
| Medida de respuesta | Tiempo de espera máximo de 3 segundos por llamada entre servicios. Cero hilos o conexiones bloqueados más allá de ese plazo. Las operaciones de M3 y M4 que no requieren datos institucionales conservan el 100% de su funcionalidad. Cero errores no controlados en la interfaz durante la indisponibilidad |

### Verificación

- Este escenario es la contrapartida de la arquitectura distribuida: separar los servicios solo aporta si el fallo de uno no arrastra al otro. Sin él, la distribución añade puntos de fallo sin beneficio.
- La prueba se ejecuta deteniendo el contenedor del Servicio Institucional y recorriendo el inventario y el ciclo de vida de la unidad.
- Un plazo de espera sin límite es el fallo más común y el más caro: agota el pool de conexiones y convierte la caída de un servicio en la caída de los dos.
- La respuesta degradada nunca inventa datos. Si no se puede confirmar la campaña asociada, se dice que no está disponible; no se muestra un valor por defecto.
