## EC-24 — Extensión a otro tipo de donación

*Atributo de calidad:* Flexibilidad

*HU relacionadas:* RF-16 — decisión arquitectónica, ADR-005

*Subcaracterística:* Adaptabilidad

*Prioridad:* Baja

### Escenario estímulo–respuesta

| Elemento | Descripción |
|---|---|
| Fuente del estímulo | El cliente, en una fase posterior del producto |
| Estímulo | Pide administrar un tipo de donación distinto de la sangre, con su propio ciclo de vida y su propia caducidad |
| Contexto | Sistema en operación con el dominio de sangre ya construido |
| Artefacto afectado | Modelo de dominio del Servicio de Donación, esquema de base de datos, inventario y trazabilidad |
| Respuesta | El tipo nuevo se incorpora como dato del modelo. La trazabilidad, el inventario y la disposición final siguen aplicando sin duplicar código ni crear una estructura paralela |
| Medida de respuesta | Cero cambios estructurales en las tablas de trazabilidad y de inventario. El tipo nuevo se agrega por registro en catálogo o por configuración, no creando tablas propias. Ninguna funcionalidad existente deja de operar |

### Verificación

- RF-16 está clasificado como *Won't* para la primera versión. El escenario no compromete la funcionalidad: verifica que el modelo de datos no la impida, que es lo que se decide en el Sprint 1 y ya no se puede revertir barato después.
- La comprobación se hace sobre el diseño del modelo, en la revisión de arquitectura, y no requiere construir nada.
- La señal de que el escenario falla es un modelo donde «sangre» aparece en el nombre de las tablas de trazabilidad e inventario, en lugar de como valor de un atributo de tipo.
- Extensibilidad no es un módulo funcional sino una propiedad del modelo. Vive en el ADR, no en el catálogo de módulos.
