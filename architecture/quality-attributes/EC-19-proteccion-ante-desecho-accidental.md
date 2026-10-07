## EC-19 — Protección ante el desecho accidental de una unidad

*Atributo de calidad:* Capacidad de interacción

*HU relacionadas:* RF-06, RF-10 — M3, M4

*Subcaracterística:* Protección contra errores de usuario

*Prioridad:* Alta

### Escenario estímulo–respuesta

| Elemento | Descripción |
|---|---|
| Fuente del estímulo | Operador de banco (U3) en jornada operativa, con prisa o fatiga |
| Estímulo | Ejecuta la disposición final de una unidad, o aprueba una transferencia. Ambas son acciones irreversibles |
| Contexto | Operación normal, varias unidades en pantalla y una lista larga donde es fácil equivocarse de fila |
| Artefacto afectado | Aplicación web, Servicio de Donación |
| Respuesta | La acción exige una confirmación explícita que identifica la unidad concreta sobre la que se va a actuar. No se dispara con una sola pulsación ni queda contigua a una acción frecuente e inofensiva |
| Medida de respuesta | El 100% de las acciones irreversibles pide confirmación mostrando el identificador de la unidad. Cero acciones irreversibles ejecutables con un solo clic. El 100% queda registrado en la bitácora con su responsable |

### Verificación

- La confirmación debe mostrar **qué** unidad, no preguntar «¿está seguro?». Un mensaje genérico no evita el error de fila, que es el error real que se quiere prevenir.
- La acción destructiva no se coloca junto a la más usada de la pantalla. La separación física es tan importante como la confirmación.
- Este escenario protege contra el error del usuario legítimo. EC-12 protege contra la operación inválida aunque el usuario la confirme.
- Que la acción sea irreversible en el dominio no significa que no deje rastro: la unidad desechada conserva su historial completo, según EC-04.
