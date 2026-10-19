# ADR — Selección de Gatling para pruebas de carga y estrés

- **Estado:** Propuesta para revisión del equipo
- **Versión:** 1.0
- **Fecha:** 2026-10-18
- **Responsable de decisión:** Equipo de Arquitectura y QA
- **Responsable de implementación:** QA
- **Referencias:** Documento de Herramientas y Políticas V4, entrada 80 (Gatling); escenarios de pruebas LT-01, LT-02, LT-03 y LT-04.
- **Identificador:** Pendiente de asignar según el consecutivo vigente del repositorio.

## Historial de cambios

| Versión | Fecha | Cambio |
|---|---|---|
| 1.0 | 2026-10-18 | Registro de la selección y de las consecuencias observadas en QA. |

## Contexto

RedVital requiere verificar atributos de rendimiento del sistema expuesto mediante su API Gateway: tiempos de respuesta, errores, comportamiento con usuarios virtuales concurrentes y respuesta bajo estrés. Estas pruebas deben producir resultados reproducibles y evidencias que puedan incorporarse al Documento de Pruebas (TD).

El Documento de Herramientas V4 ya clasifica **Gatling** como **Adoptar** en su **entrada 80**, y especifica edición comunitaria gratuita, simulaciones Java y ejecución con Maven sin un servicio externo. Este ADR registra y justifica esa elección para las pruebas de carga del entorno QA.

## Opciones consideradas

| Opción | Ventaja relevante | Limitación o costo |
|---|---|---|
| **Gatling (Java, Maven)** | Escenarios de carga versionables, usuarios virtuales, informes HTML y métricas por operación | Requiere desarrollar y mantener simulaciones; autenticación y expiración de sesiones deben modelarse correctamente |
| **Apache JMeter** | Alternativa extendida con configuración visual y opciones de automatización | El mantenimiento de escenarios complejos puede requerir archivos y complementos adicionales |
| **k6** | Escenarios automatizables mediante código y ejecución por línea de comandos | Requeriría adoptar otro entorno de scripting y ajustar las herramientas de trabajo del equipo |

Estas ventajas y limitaciones son criterios generales de comparación, no resultados de un benchmark medido en RedVital.

## Decisión

**Proponer la adopción de Gatling Community Edition**, con simulaciones en **Java** y ejecución mediante **Maven** dentro de un contenedor **Docker**, para LT-01 a LT-04 y otros escenarios de rendimiento que apruebe el equipo.

Las pruebas deben ejecutarse desde la máquina **Tools** contra el entorno QA autorizado. Los reportes HTML de Gatling y los registros de ejecución constituyen evidencia; los indicadores de infraestructura requieren observabilidad adicional (por ejemplo, Prometheus/Grafana, cuando estén configurados).

## Justificación

1. Es coherente con la entrada 80 del Documento de Herramientas V4.
2. Permite declarar perfiles de carga con incrementos de usuarios, concurrencia y duración determinados.
3. Distingue las operaciones de **autenticación** y **consulta de inventario**, indispensable para no atribuir a inventario los rechazos de acceso.
4. Genera informes HTML con recuentos OK/KO, percentiles y evolución temporal de las solicitudes.
5. Las simulaciones pueden conservarse en control de versiones y volver a ejecutarse con parámetros documentados.

## Consecuencias

### Positivas

- Escenarios automatizables y repetibles.
- Evidencias cuantitativas por operación y por ejecución.
- Posibilidad de comparar resultados sin depender de capturas manuales durante la carga.

### Riesgos y restricciones

- **Usuarios virtuales no equivalen a usuarios autenticados:** los rechazos HTTP 429 del Gateway reducen la carga efectiva que alcanza inventario.
- Las sesiones de duración prolongada deben gestionar de manera autorizada la caducidad del token; de lo contrario, pueden aparecer HTTP 401 y dejar de medirse correctamente las operaciones de negocio.
- El generador debe evitar ciclos de reemplazo de usuarios fallidos que produzcan un volumen artificial de autenticaciones.
- Un `BUILD SUCCESS` en Maven significa que terminó la ejecución, **no** que el sistema cumpliera los criterios de rendimiento.
- Gatling no prueba por sí solo CPU, memoria, recuperación completa del sistema ni integridad de datos: deben recogerse evidencias independientes.
- Las credenciales de QA, tokens y contraseñas no deben publicarse en Git; deben manejarse fuera del repositorio.
- Las pruebas de estrés necesitan autorización y supervisión del entorno.

## Evidencia de aplicación en RedVital

Las cuatro pruebas LT-01 a LT-04 fueron ejecutadas con Gatling. Se produjeron reportes HTML individuales y se documentaron los resultados distinguiendo errores de autenticación (429), errores de autorización (401) y respuestas de inventario. Las ejecuciones disponibles no acreditan por sí solas los objetivos de concurrencia autenticada: la interpretación y las limitaciones se consignan en cada reporte LT correspondiente.

## Criterios de revisión

Revisar esta decisión cuando cambie el modelo de autenticación, el entorno de ejecución, la estrategia de observabilidad, el tipo de protocolos que deban probarse o las herramientas de QA aprobadas.

## Documentos relacionados

- Documento de Herramientas y Políticas V4 — entrada 80, Gatling.
- TD y reportes LT-01, LT-02, LT-03, LT-04.
- Guía operativa de ejecución de Gatling (por incorporar al repositorio de pruebas).
