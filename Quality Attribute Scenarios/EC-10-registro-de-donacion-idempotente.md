## EC-10 — Registro de donación idempotente

*Atributo de calidad:* Fiabilidad

*HU relacionadas:* RF-03, RF-07 — M3, M4

*Subcaracterística:* Ausencia de fallos

*Prioridad:* Alta

### Escenario estímulo–respuesta

| Elemento | Descripción |
|---|---|
| Fuente del estímulo | Operador de banco (U3) |
| Estímulo | Envía dos veces el mismo registro de donación, por doble pulsación, por reintento del navegador o porque la red cortó la respuesta y el cliente reenvió |
| Contexto | Jornada operativa con conectividad intermitente |
| Artefacto afectado | Servicio de Donación, base de datos, inventario |
| Respuesta | El sistema crea una sola unidad. La segunda petición devuelve el mismo resultado que la primera en lugar de crear un duplicado o de fallar con un error que confunda al operador |
| Medida de respuesta | Cero unidades duplicadas ante 100 reenvíos del mismo identificador de operación. El conteo del inventario no varía por efecto del reintento. La segunda respuesta es idéntica a la primera |

### Verificación

- Una unidad duplicada es peor que una donación perdida: infla el inventario disponible y puede llevar a no escalar una transferencia que sí hacía falta.
- La idempotencia se resuelve con un identificador de operación que genera el cliente y que el servicio usa como clave, no con una comprobación de «ya existe algo parecido».
- La misma regla aplica a la aprobación de transferencias, que también es una operación que el usuario puede reenviar.
- Se prueba en el nivel de la API, no en la interfaz: deshabilitar el botón tras el primer clic ayuda al usuario pero no protege al servicio.
