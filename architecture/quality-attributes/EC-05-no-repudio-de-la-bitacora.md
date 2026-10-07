## EC-05 — No repudio de la bitácora de auditoría

*Atributo de calidad:* Seguridad

*HU relacionadas:* RF-06, RF-09, RF-10 — servicio transversal de auditoría

*Subcaracterística:* No repudio y responsabilidad

*Prioridad:* Alta

### Escenario estímulo–respuesta

| Elemento | Descripción |
|---|---|
| Fuente del estímulo | Auditor (U7), o una investigación posterior a un incidente |
| Estímulo | Se requiere establecer quién ejecutó una acción sensible: marcar una unidad como no apta, ejecutar su disposición final, aprobar una transferencia entre bancos, o intentar un acceso fuera de jurisdicción |
| Contexto | Sistema en operación, consulta posterior al hecho |
| Artefacto afectado | Servicio transversal de auditoría, base de datos, pantalla del auditor |
| Respuesta | Cada acción sensible quedó registrada con actor, rol, jurisdicción, marca de tiempo, recurso afectado y resultado. Ningún usuario de la aplicación, incluido el administrador nacional, puede borrar ni alterar un registro de la bitácora desde el sistema |
| Medida de respuesta | El 100% de las acciones del catálogo de acciones sensibles queda registrado. Cero operaciones de actualización o borrado sobre la bitácora expuestas por la aplicación. El auditor reconstruye la cadena completa de eventos de una unidad, de captación a disposición final, en menos de cinco minutos y sin apoyo del equipo técnico |

### Verificación

- El catálogo de acciones sensibles se define de forma explícita en el diseño y se revisa cada sprint: una acción sensible que no esté en el catálogo no se registra, y el hueco no se nota hasta que se necesita.
- El auditor (U7) no escribe en ningún módulo. Un auditor que puede modificar lo que audita invalida la auditoría.
- La bitácora registra el intento fallido de EC-01 con el mismo detalle que una acción exitosa.
- Los registros de auditoría no contienen causa clínica (EC-02) ni datos personales innecesarios para identificar la acción.
