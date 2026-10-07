## EC-13 — Exclusión automática de unidades vencidas

*Atributo de calidad:* Adecuación funcional

*HU relacionadas:* RF-18, RF-07 — M4

*Subcaracterística:* Corrección funcional

*Prioridad:* Alta

### Escenario estímulo–respuesta

| Elemento | Descripción |
|---|---|
| Fuente del estímulo | El paso del tiempo. No hay usuario involucrado |
| Estímulo | Una unidad alcanza su fecha de vencimiento mientras figura como disponible en el inventario |
| Contexto | Sistema en operación normal, sin que ningún usuario esté conectado |
| Artefacto afectado | Servicio de Donación: inventario, proceso programado de vencimiento, bitácora |
| Respuesta | La unidad sale del inventario disponible y entra al flujo de disposición final, sin que nadie ejecute una acción ni confirme nada |
| Medida de respuesta | En el 100% de los casos la unidad deja de figurar como disponible dentro de los 15 minutos siguientes al vencimiento. Cero unidades vencidas visibles como disponibles en cualquier consulta, listado o sugerencia de transferencia. El cambio queda en la bitácora con el sistema como actor |

### Verificación

- El requisito es explícito en que la exclusión ocurre **sin intervención manual**. Una pantalla que pida al operador «revisar vencimientos» no cumple el escenario aunque produzca el mismo resultado.
- La prueba se hace adelantando el reloj sobre datos sintéticos y comprobando el inventario antes y después, sin tocar la interfaz.
- El actor del registro de auditoría es el sistema, no el último usuario conectado. Atribuir a una persona una acción automática rompe EC-05.
- Complementa a EC-12: este escenario retira la unidad, aquel impide despacharla si por alguna razón siguiera visible.
