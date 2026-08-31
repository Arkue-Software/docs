## EC-11 — Reanudación de notificaciones

*Atributo de calidad:* Fiabilidad

*HU relacionadas:* RF-12, RF-14 — servicio transversal de notificaciones

*Subcaracterística:* Recuperabilidad

*Prioridad:* Media

### Escenario estímulo–respuesta

| Elemento | Descripción |
|---|---|
| Fuente del estímulo | El propio sistema, al producirse un evento notificable: elegibilidad recuperada, campaña cercana, reconocimiento obtenido o convocatoria por escasez |
| Estímulo | El evento se genera mientras el canal de notificación no está disponible |
| Contexto | Operación degradada: el sistema funciona, el canal de salida no |
| Artefacto afectado | Servicio transversal de notificaciones, almacenamiento de eventos pendientes |
| Respuesta | El evento se conserva y se reintenta con espera creciente. Al restablecerse el canal, la notificación se entrega. Ningún aviso se pierde en silencio ni llega dos veces al mismo donante |
| Medida de respuesta | El 100% de los eventos generados durante la indisponibilidad se entrega al restablecerse el canal. Cero notificaciones duplicadas al mismo donante por el mismo evento. Agotados los reintentos, el evento queda marcado para revisión manual y visible en el tablero, nunca descartado sin registro |

### Verificación

- El caso que importa es la convocatoria por escasez: un aviso que se pierde cuando falta un componente sanguíneo es precisamente el que no puede perderse.
- La notificación es asíncrona por diseño. Que el canal falle no debe bloquear el registro de la donación ni la consulta de inventario que originó el evento.
- El descarte silencioso es el peor resultado posible, peor que el reintento fallido visible: nadie se entera de que el sistema dejó de avisar.
- Ninguna notificación al donante incluye causa clínica ni ofrece contraprestación económica (EC-02 y EC-14).
