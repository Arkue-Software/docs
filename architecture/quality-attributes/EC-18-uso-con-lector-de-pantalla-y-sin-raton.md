## EC-18 — Uso con lector de pantalla y sin ratón

*Atributo de calidad:* Capacidad de interacción

*HU relacionadas:* RF-01, RF-07, RF-17 — M1, M4

*Subcaracterística:* Inclusividad

*Prioridad:* Media

### Escenario estímulo–respuesta

| Elemento | Descripción |
|---|---|
| Fuente del estímulo | Donante o funcionario que usa lector de pantalla, que navega únicamente con teclado, o que tiene baja visión o daltonismo |
| Estímulo | Recorre el registro anónimo y la consulta de inventario de principio a fin |
| Contexto | Operación normal, navegador con lector de pantalla activo o sin ratón conectado |
| Artefacto afectado | Aplicación web |
| Respuesta | Todo control es alcanzable con teclado y muestra el foco de forma visible. Toda información transmitida por color va acompañada de texto o de forma. Los mensajes de error se anuncian al lector de pantalla y no solo cambian de color |
| Medida de respuesta | Conformidad con WCAG 2.2 nivel AA en las pantallas críticas. Cero incidencias de contraste por debajo de 4.5:1 en texto normal y 3:1 en texto grande. Objetivos interactivos de al menos 24 por 24 píxeles. Recorrido completo con teclado sin trampas de foco |

### Verificación

- La accesibilidad es parte de la característica de calidad, no un añadido opcional: un sistema público de salud que excluye a una parte de sus usuarios no cumple su función.
- Las pantallas críticas para este escenario son el registro anónimo, la consulta de inventario y la disposición final de una unidad.
- Los indicadores de estado del inventario no pueden distinguirse solo por color: la celda bajo umbral lleva además marca visual y texto.
- La revisión combina herramienta automática y recorrido manual con teclado. La herramienta sola detecta contraste y etiquetas, pero no trampas de foco ni orden de lectura incoherente.
