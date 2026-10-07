## EC-12 — Bloqueo del despacho de una unidad no apta o vencida

*Atributo de calidad:* Seguridad física

*HU relacionadas:* RF-05, RF-06, RF-10, RF-18 — M3, M4

*Subcaracterística:* A prueba de fallos

*Prioridad:* Alta

### Escenario estímulo–respuesta

| Elemento | Descripción |
|---|---|
| Fuente del estímulo | Operador de banco (U3) o coordinador que gestiona una transferencia entre bancos |
| Estímulo | Intenta despachar, transferir o reservar una unidad que está marcada como no apta o cuya fecha de vencimiento ya se cumplió |
| Contexto | Operación normal, o degradada porque el estado de la unidad no se puede confirmar en ese momento |
| Artefacto afectado | Servicio de Donación: inventario, transferencias y disposición final |
| Respuesta | El sistema rechaza la operación. Ante estado indeterminado o dato faltante, opta siempre por la alternativa más restrictiva y trata la unidad como no disponible. La unidad no aparece en ningún listado de disponibles, tampoco en el que alimenta las sugerencias de transferencia |
| Medida de respuesta | Cero unidades no aptas o vencidas presentes en cualquier respuesta de unidades disponibles, incluida la ruta de transferencia entre bancos. Ante estado indeterminado, el 100% de las unidades se trata como no disponible. El mensaje de rechazo no revela causa clínica |

### Verificación

- Este es el único escenario del catálogo cuyo incumplimiento afecta a la integridad física de una persona. Una unidad no apta que llega a transfusión es el fallo que el sistema existe para impedir.
- El comportamiento por defecto ante la duda es el rechazo. Un sistema que asume «disponible» cuando no puede confirmar el estado falla del lado peligroso.
- La regla se aplica en el servicio y en toda ruta que consulte disponibilidad, no solo en la pantalla de despacho: la sugerencia automática de transferencia es una ruta distinta y suele olvidarse.
- Se cruza con EC-13, que retira la unidad vencida sin intervención, y con EC-02, que impide que el rechazo explique el motivo clínico.
