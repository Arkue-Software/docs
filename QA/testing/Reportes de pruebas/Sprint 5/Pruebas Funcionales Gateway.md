# Informe de pruebas - Gateway

## Control del documento

| Campo | Información |
|---|---|
| Código del documento | TST-GATEWAY |
| Versión | 0.1 |
| Estado | Borrador |
| Responsable del informe | Tomás Silva |
| Ejecutado por | Tomás Silva |
| Fecha de ejecución | Octubre de 2026 |
| Ambiente | Pruebas |
| Rama | release |

## Historial de cambios

| Versión | Fecha | Responsable | Descripción |
|---|---|---|---|
| 0.1 | 05/11/2026 | Tomás Silva | Consolidación de las verificaciones realizadas sobre el gateway y sus controles de acceso. |

---

## 1. Resumen ejecutivo

Se realizaron verificaciones sobre el gateway de RedVital, enfocadas en revisar la configuración y el comportamiento de los mecanismos de autenticación y control de acceso.

Se validó el rechazo de un token firmado con una llave de desarrollo que no corresponde a la llave pública confiable del ambiente QA. También se ejecutó una prueba local con dependencias simuladas para observar el comportamiento de rechazo de autenticación OIDC y el registro de denegaciones.

Algunas pruebas adicionales no se ejecutaron debido a restricciones del ambiente y a que requerían condiciones específicas de desarrollo o solicitudes de escritura.

## 2. Información de ejecución

| Campo | Información |
|---|---|
| Componente | Gateway |
| Tipo de prueba | Pruebas de configuración y seguridad |
| Alcance | Validación de autenticación, rechazo de tokens y controles de acceso |
| Ambiente | Pruebas y ejecución local simulada, según el caso |
| Ejecutor | Tomás Silva |
| Dependencias | APISIX, configuración de autenticación y servicios relacionados |
| Tipo de dependencia | Real en la prueba QA; simulada en la prueba local |

## 3. Ejecución de pruebas

### 3.1 Validación de token firmado con llave no confiable

**Identificador:** T-303.3.

**Descripción:** Se generó una llave de firma de desarrollo para comprobar si el gateway QA aceptaba un token firmado con una llave que no correspondía a la llave pública configurada para el ambiente.

**Resultado esperado:** El gateway debe rechazar el token si su firma no puede validarse con la llave pública confiable.

**Resultado obtenido:** La solicitud al gateway QA fue rechazada con HTTP 401.

**Veredicto:** Aprobado.

**Observación:** La llave generada fue de desarrollo y no se utilizó como sustituto de secretos del ambiente QA o producción.

### 3.2 Verificación local de rechazo OIDC y auditoría

**Identificador:** T-305.4.

**Descripción:** Se ejecutó el escenario local de prueba utilizando dependencias simuladas para verificar el rechazo de autenticación OIDC y el registro de la denegación hacia Identidad.

**Resultado esperado:** La solicitud sin una autenticación válida debe ser rechazada y el escenario debe contemplar el registro de la denegación.

**Resultado obtenido:** El escenario local simulado devolvió el rechazo esperado con HTTP 401 y registró la denegación en el contexto de la prueba.

**Veredicto:** Aprobado para el escenario local simulado.

**Limitación:** Esta ejecución no constituye una validación del flujo completo contra el servicio de Identidad real en QA.

### 3.3 Validación de configuración YAML

**Descripción:** Se ejecutó la validación de los archivos YAML relacionados con la configuración local del gateway.

**Resultado esperado:** La configuración debe poder analizarse y no presentar errores que impidan su interpretación.

**Resultado obtenido:** La validación terminó con tres advertencias no bloqueantes. No se reportaron errores que impidieran completar el análisis.

**Veredicto:** Aprobado con observaciones.

## 4. Pruebas no ejecutadas

| Identificador | Motivo |
|---|---|
| T-305.1 | No se ejecutó individualmente en esta actividad. |
| T-305.2 | No se ejecutó individualmente en esta actividad. |
| T-305.3 | No se ejecutó individualmente en esta actividad. |
| T-305.5 | No se ejecutó individualmente en esta actividad. |
| T-305.6 | Requería condiciones de Development y operaciones POST que no correspondían al ambiente QA disponible. |

Estas pruebas no se contabilizan como aprobadas ni como fallidas.

## 5. Resumen de resultados

| Estado | Cantidad |
|---|---:|
| Pruebas ejecutadas con resultado satisfactorio | 2 |
| Validaciones con observaciones | 1 |
| Pruebas no ejecutadas | 5 |

## 6. Defectos encontrados

No se identificaron defectos confirmados del gateway durante los escenarios ejecutados.

Las tres advertencias de YAML se registran como observaciones de configuración y no como defectos funcionales confirmados.

## 7. Conclusiones

El gateway rechazó correctamente el token firmado con una llave no confiable y el escenario local simulado presentó el comportamiento esperado ante una autenticación OIDC no válida. La validación de YAML finalizó con advertencias no bloqueantes.

La cobertura de esta actividad fue parcial, debido a que algunas pruebas requerían condiciones de desarrollo o no se ejecutaron individualmente. Por lo tanto, los resultados no representan una validación completa de todos los controles del gateway.

## 8. Pendientes y observaciones

- Revisar las tres advertencias de YAML con el equipo responsable de la configuración.
- Ejecutar los casos pendientes en un ambiente autorizado que cumpla sus requisitos.
- Validar los flujos de autenticación contra dependencias reales cuando el entorno lo permita.
- No se modificaron archivos de configuración ni código del gateway durante la actividad.

## 9. Evidencias

Se cuenta con la respuesta HTTP 401 de la prueba QA, la salida del escenario local simulado y el resultado de la validación YAML. Las llaves privadas y secretos utilizados en escenarios de desarrollo no deben incluirse en las evidencias.

## 10. Revisión y aprobación

| Rol | Responsable | Estado |
|---|---|---|
| Responsable del informe | Tomás Silva | Elaborado |
| Revisado por | Pendiente | Pendiente |
| Aprobado por | Pendiente | Pendiente |