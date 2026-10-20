
# Informe de pruebas de integración - Sprint 4

## Control del documento

| Campo | Información |
|---|---|
| Código del documento | TST-INT-04 |
| Versión | 0.1 |
| Estado | Borrador |
| Sprint o entrega | 4 |
| Responsable del informe | Tomás Silva |
| Revisado por | Pendiente |
| Aprobado por | Pendiente |
| Fecha de creación | Pendiente de confirmar |
| Última actualización | Pendiente de confirmar |
| Plan de pruebas relacionado | Pendiente de asociar |
| Versión o commit probado | Pendiente de identificar |

## Historial de cambios

| Versión | Fecha | Descripción del cambio | Autor |
|---|---|---|---|
| 0.1 | 17/10/26 | Creación inicial del informe de integración del servicio de Donación. | Tomás Silva |

---

## 1. Resumen ejecutivo

Durante el Sprint 4 se ejecutó la suite `ServicioDonacionIntegracionTest` para verificar las interacciones del servicio de Donación con dependencias de infraestructura utilizadas en pruebas.

La ejecución empleó Testcontainers para proporcionar instancias de PostgreSQL y Kafka. Determinadas dependencias, entre ellas Identidad y la validación del JWT, se simularon para controlar las condiciones de la prueba. El evento de campaña se publicó directamente en Kafka durante la ejecución automatizada.

El resultado registrado fue de siete pruebas aprobadas.

**Conclusión preliminar:** la suite de integración finalizó correctamente. Su alcance corresponde a las interacciones verificadas con las dependencias configuradas para la prueba y no sustituye la validación E2E entre los servicios reales de RedVital.

## 2. Información de ejecución

| Campo | Información |
|---|---|
| Periodo de ejecución | 16 a 17 de Octubre de 2026|
| Ambiente | Entorno automatizado de pruebas |
| Componente principal | Servicio de Donación |
| Suite | `ServicioDonacionIntegracionTest` |
| Herramientas | Testcontainers, PostgreSQL, Kafka y herramienta de pruebas del proyecto |
| Datos utilizados | Datos y eventos de prueba |
| Responsable de ejecución | Tomás Silva |
| Plan de pruebas | Pendiente de asociar |

### 2.1 Dependencias utilizadas

- **PostgreSQL mediante Testcontainers:** permite ejecutar la prueba contra una instancia de base de datos preparada para la ejecución.
- **Kafka mediante Testcontainers:** permite verificar las interacciones implementadas con el sistema de mensajería.
- **Simulaciones de Identidad y JWT:** permiten controlar el comportamiento de estas dependencias sin depender de la autenticación real entre servicios durante esta suite.

### 2.2 Alcance y limitaciones

La utilización de contenedores proporciona dependencias controladas para las pruebas. Sin embargo, el hecho de utilizar PostgreSQL y Kafka reales dentro de contenedores no significa que se hayan ejecutado todos los servicios reales de RedVital.

La publicación directa del evento en Kafka permite evaluar el comportamiento de Donación ante ese evento, pero omite el recorrido completo desde la publicación de una campaña mediante la API de Campañas.

## 3. Resumen de resultados

| Indicador | Resultado |
|---|---:|
| Casos registrados como ejecutados | 7 |
| Casos aprobados | 7 |
| Casos fallidos | 0 |
| Casos bloqueados | 0 |
| Porcentaje de aprobación | 100 % |

Los resultados corresponden a la suite ejecutada. La cobertura de requisitos debe verificarse por separado.

## 4. Resultados por caso de prueba

### 4.1 Integración del servicio de Donación

**Identificador:** pendiente de asociar con los casos individuales de la suite.

**Objetivo**

Verificar las interacciones del servicio de Donación con PostgreSQL y Kafka dentro de las condiciones controladas de la suite de integración.

**Precondiciones**

- El proyecto y sus dependencias de prueba deben estar disponibles.
- Testcontainers debe poder preparar los contenedores requeridos.
- La suite debe contar con su configuración y sus datos de prueba.
- Las simulaciones de dependencias externas deben estar configuradas según la implementación de la prueba.

**Procedimiento ejecutado**

1. Se ejecutó la suite `ServicioDonacionIntegracionTest`.
2. La configuración de pruebas preparó las dependencias de infraestructura mediante Testcontainers.
3. Se ejecutaron los escenarios implementados en la suite.
4. Durante la ejecución se publicó directamente en Kafka un evento de campaña utilizado por las pruebas.
5. Las dependencias de Identidad y validación del JWT se controlaron mediante simulaciones.
6. Se revisó el resumen de la ejecución para identificar fallos, errores y casos omitidos.

**Comando exacto**

Pendiente de recuperar del registro original. No se reconstruye para evitar atribuir a la ejecución un comando que no se ha confirmado.

**Resultado esperado**

Las interacciones verificadas por la suite deben finalizar correctamente con las dependencias de prueba configuradas.

**Resultado obtenido**

Se registraron siete pruebas aprobadas.

**Veredicto:** Aprobado para la suite de integración ejecutada.

**Evidencias**

- Resumen de ejecución de `ServicioDonacionIntegracionTest`.
- Configuración de Testcontainers utilizada por la suite.
- Implementación de los escenarios y de las simulaciones de dependencias.

**Limitaciones**

Este resultado no demuestra por sí solo:

- Que la autenticación real entre servicios funcione correctamente en todos los escenarios.
- Que Campañas publique eventos correctamente en el entorno desplegado.
- Que la comunicación entre todos los servicios sea correcta en el ambiente QA.
- Que la proyección de Donación se actualice en todos los escenarios posibles de publicación.

Estas verificaciones corresponden a pruebas adicionales, incluidas las pruebas E2E.

## 5. Defectos encontrados

No se dispone de defectos funcionales confirmados asociados a esta ejecución.

Esto no implica que el componente esté libre de defectos; únicamente indica que no se registraron fallos en las siete pruebas reportadas.

## 6. Riesgos y pendientes

| Elemento | Impacto | Acción requerida | Responsable |
|---|---|---|---|
| Dependencias externas simuladas | Medio | Contrastar las simulaciones con pruebas de los servicios reales. | QA / equipo técnico |
| Comando de ejecución pendiente | Bajo | Recuperar el comando original o volver a ejecutar la suite si se necesita evidencia reproducible. | QA |
| Trazabilidad de casos pendiente | Medio | Relacionar los escenarios de la suite con requisitos y casos formales. | QA |
| Cobertura de comunicación entre servicios | Medio | Complementar con escenarios E2E en el ambiente QA. | QA |

## 7. Conclusión

La suite `ServicioDonacionIntegracionTest` finalizó con siete pruebas aprobadas. La ejecución proporciona evidencia favorable de las interacciones evaluadas bajo las condiciones controladas de la suite.

- **Recomendación:** resultado aprobado para la ejecución registrada.
- **Riesgos aceptados:** ninguno formalmente documentado.
- **Acciones pendientes:** completar la trazabilidad, recuperar el comando de ejecución si está disponible y complementar la evidencia con pruebas E2E entre servicios reales.

## 8. Revisión y aprobación

| Actividad | Persona | Fecha | Resultado |
|---|---|---|---|
| Revisión de resultados | Carlos | Pendiente | Pendiente |
| Aprobación final | Tomás Silva | Pendiente | Pendiente |

El documento permanece en borrador hasta completar la revisión de las evidencias.
