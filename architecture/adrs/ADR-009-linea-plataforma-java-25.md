# ADR-009 — Línea de plataforma Java 25 para los componentes Java

| Campo | Contenido |
|---|---|
| **Identificador y título** | ADR-009. Línea de plataforma Java 25 para los componentes Java. |
| **Estado** | Propuesta para deliberación y aprobación en Mesa de Arquitectura. |
| **Restricciones aplicables** | RI-01 y lineamientos tecnológicos vigentes del proyecto. |
| **Fecha** | 22 de septiembre de 2026. |

---

## Contexto

RedVital utiliza componentes implementados sobre la plataforma Java y necesita fijar una línea de versión común antes de que avance la construcción del backend.

Mantener una versión sin verificar su horizonte de soporte podría obligar al equipo a migrar durante los sprints de implementación, con impacto sobre dependencias, imágenes de contenedor, compilación, pruebas y documentación.

El Backlog V3 propone Java 25 como línea de plataforma para los componentes Java, pero la decisión no debe cerrarse únicamente por preferencia técnica. La versión seleccionada debe contar con una ventana de soporte que cubra el horizonte del proyecto y debe ser coherente con la distribución JDK finalmente adoptada.

El proyecto es académico. Por ello, además del soporte, se debe considerar el costo operativo de la decisión: disponibilidad de imágenes, compatibilidad con librerías, facilidad de instalación en los ambientes del equipo y ausencia de costos de licenciamiento innecesarios.

---

## Opciones consideradas

### Opción A — Mantener la línea Java anterior

Mantener la versión utilizada previamente reduciría el cambio inmediato y podría evitar ajustes en dependencias existentes.

Sin embargo, conservar una versión sin verificar su ventana de soporte puede trasladar el costo de migración a una etapa posterior, cuando ya existan más código, imágenes, pruebas y documentación acoplados a ella.

**Descartada como decisión definitiva mientras no se demuestre que cubre el horizonte del proyecto.**

### Opción B — Adoptar Java 25 como línea de plataforma

Java 25 se adopta como línea propuesta para los componentes Java del Sprint 2 y siguientes.

La elección busca evitar una migración de versión durante el proyecto y mantener una línea única entre código, imágenes de contenedor, pipeline y documentación.

La distribución concreta y la fecha oficial de fin de soporte no se fijan en este ADR hasta completar la verificación correspondiente.

**Seleccionada, sujeta a verificación de soporte.**

### Opción C — Elegir otra versión después de iniciar la implementación

Posponer la decisión permitiría evaluar más alternativas, pero trasladaría el riesgo a una etapa en la que ya existirían componentes construidos sobre una versión no consolidada.

Esto aumentaría el costo de cambio y contradice el criterio del backlog de tomar ahora las decisiones estructurales que después serían más caras de revertir.

**Descartada.**

---

## Compromiso evaluado

**Se gana.** Una línea de plataforma única para los componentes Java, menor riesgo de migración durante la implementación y coherencia entre desarrollo, contenedores, pipeline y documentación.

También se evita que distintos desarrolladores trabajen con versiones diferentes de Java, reduciendo fallos de compatibilidad y diferencias entre ambientes.

**Se sacrifica.** Adoptar una versión reciente puede exponer incompatibilidades con librerías, plugins o imágenes base que todavía no estén completamente alineadas con ella.

La decisión además depende de una verificación externa: no basta con declarar Java 25, es necesario confirmar que la distribución seleccionada y su ventana de soporte cubran el horizonte del proyecto.

En el contexto académico, el principal costo no es monetario sino el tiempo de ajuste de dependencias, configuración y pruebas si alguna herramienta todavía no es compatible.

---

## Decisión

1. **Java 25 se adopta como línea propuesta de plataforma para los componentes Java de RedVital.**

2. **Todos los componentes Java deben compilar, ejecutarse y documentarse sobre la misma línea de versión**, evitando diferencias entre ambientes de desarrollo, QA y demostración.

3. **La distribución concreta del JDK queda pendiente de verificación en T-103.**

4. **La fecha oficial de fin de soporte de la distribución seleccionada queda pendiente de verificación en T-103.**

5. **La decisión solo se considera cerrada si la ventana de soporte cubre, como mínimo, el horizonte del proyecto hasta la Semana 16.**

6. **Si la distribución o su ventana de soporte no cubren el horizonte requerido, se abre un nuevo ADR para seleccionar una alternativa.**

7. **Las imágenes de contenedor, el pipeline de compilación y la documentación técnica deben utilizar la misma versión y distribución una vez confirmadas.**

8. **No se adopta una distribución con costo de licenciamiento si existe una alternativa compatible y sin costo que satisfaga los requisitos del proyecto académico.**

9. **La compatibilidad de librerías y dependencias debe comprobarse antes de considerar la plataforma completamente consolidada.**

10. **T-604 actualizará Herramientas V3 con la versión, distribución y ventana de soporte finalmente confirmadas.**

---

## Justificación

La decisión busca evitar que una elección aparentemente menor de versión se convierta después en una migración costosa.

La versión de Java afecta directamente la compilación, las dependencias, las imágenes de contenedor, el pipeline y el ambiente de ejecución. Por eso conviene fijarla antes de que el backend avance.

Java 25 se adopta como línea propuesta porque es la versión declarada por el Backlog V3 para los componentes Java del Sprint 2. Sin embargo, este ADR no sustituye la verificación de soporte asignada a T-103.

La Arquitecta de Software documenta la decisión estructural, mientras que la comprobación oficial de la ventana de soporte corresponde al responsable asignado en el backlog.

En el contexto académico, la decisión también busca minimizar costos de adopción. Se prioriza una distribución compatible, sin costo de licenciamiento innecesario y con imágenes y herramientas disponibles para los ambientes utilizados por el equipo.

---

## Vigencia de lo elegido

La decisión permanece vigente mientras Java 25 y la distribución seleccionada mantengan soporte suficiente para el horizonte del proyecto y compatibilidad con las dependencias utilizadas.

La versión concreta puede revisarse si una incompatibilidad crítica, una limitación de soporte o un cambio de distribución impiden cumplir los requisitos del proyecto.

La distribución y su fecha de soporte no se consideran cerradas hasta completar T-103.

---

## Consecuencias

**Sobre el desarrollo.** Todos los componentes Java deben utilizar la misma línea de versión, reduciendo diferencias entre máquinas y ambientes.

**Sobre la compatibilidad.** Las librerías, plugins y herramientas deben comprobar su funcionamiento sobre Java 25 antes de consolidar la plataforma.

**Sobre los contenedores.** Las imágenes base deben corresponder a la versión y distribución finalmente aprobadas.

**Sobre el soporte.** La ventana oficial de soporte debe cubrir el horizonte del proyecto. Esta comprobación corresponde a T-103 y no se da por resuelta en este ADR.

**Sobre el costo.** Se evita introducir costos de licenciamiento innecesarios para el proyecto académico. El costo principal asumido es de configuración, compatibilidad y mantenimiento.

**Sobre Herramientas V3.** T-604 debe registrar la versión, distribución y ventana de soporte finalmente confirmadas.

**Sobre la verificación.** Si T-103 determina que la alternativa seleccionada no cubre la Semana 16, la decisión debe reabrirse mediante un nuevo ADR.

**Sobre el Tech Radar.** La entrada correspondiente a Java debe reflejar la versión y distribución finalmente aprobadas, con su justificación y ventana de soporte.

---

## Responsable y fecha de revisión

| Rol | Responsabilidad |
|---|---|
| Arquitecta de Software | Coherencia de la decisión con la arquitectura y los documentos controlados. |
| Ingeniero DevOps | Verificación oficial de distribución y ventana de soporte mediante T-103. |
| Desarrollador | Validación de compatibilidad de dependencias e imágenes sobre la línea seleccionada. |

**Próxima revisión:** cierre del Sprint 2, con T-103 resuelta y Herramientas V3 actualizada mediante T-604.
