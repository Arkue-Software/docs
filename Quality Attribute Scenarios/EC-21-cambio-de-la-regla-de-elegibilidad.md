## EC-21 — Cambio de la regla de elegibilidad

*Atributo de calidad:* Mantenibilidad

*HU relacionadas:* RF-02, RF-17 — M1

*Subcaracterística:* Modificabilidad

*Prioridad:* Media

### Escenario estímulo–respuesta

| Elemento | Descripción |
|---|---|
| Fuente del estímulo | El cliente, o un cambio en la normativa de captación |
| Estímulo | Cambia el periodo mínimo entre donaciones, o el criterio que determina si un donante es elegible |
| Contexto | Sistema en operación, con donantes ya registrados y con historial |
| Artefacto afectado | Servicio de Donación — dominio de donantes y elegibilidad |
| Respuesta | El cambio se aplica en un único punto del modelo de dominio. No obliga a modificar la aplicación web, el Servicio Institucional ni el contrato entre servicios |
| Medida de respuesta | El cambio afecta a dos archivos o menos del Servicio de Donación. Cero cambios en el Servicio Institucional y cero en el contrato. Las pruebas existentes siguen pasando y la regla nueva queda cubierta por prueba propia. Esfuerzo estimado de un punto de historia o menos |

### Verificación

- La regla de elegibilidad es la que más probabilidades tiene de cambiar durante la vida del sistema, porque depende de normativa externa. Por eso es la que se elige para medir modificabilidad.
- La medida en número de archivos es un indicador de acoplamiento, no un objetivo de estilo. Si el cambio se dispersa, la regla está duplicada en varios sitios y eso es el defecto que el escenario detecta.
- La interfaz muestra el resultado del cálculo, nunca lo replica. Una copia de la regla en el frontend haría que el sistema diera dos respuestas distintas a la misma pregunta.
- El periodo mínimo es configuración del dominio, no una constante repartida por el código.
