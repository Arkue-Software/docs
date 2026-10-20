# Informe de pruebas TST-E2E-03 - Sprint 4

## Control del documento

| Campo | Información |
|---|---|
| Código del documento | TST-E2E-03-4 |
| Versión | 0.1 |
| Estado | Borrador |
| Sprint o entrega | 4 |
| Responsable del informe | Tomás Silva |
| Revisado por | Pendiente |
| Aprobado por | Pendiente |
| Fecha de creación | 18/10/2026 |
| Última actualización | 18/10/2026 |
| Plan de pruebas relacionado | Pendiente de asociar |
| Ambiente | Pruebas (QA) |

## Historial de cambios

| Versión | Fecha | Descripción del cambio | Autor |
|---|---|---|---|
| 0.1 | 18/10/26 | Documentación inicial de la prueba E2E de autenticación y autorización para la consulta de donaciones. | Tomás Silva |

---

## 1. Resumen ejecutivo

Se ejecutó una prueba de extremo a extremo para verificar los controles de autenticación y autorización del endpoint de consulta de donaciones del servicio de Donación de RedVital.

La prueba contempló tres verificaciones:

1. Consultar una donación sin proporcionar un token de acceso.
2. Consultar la misma donación utilizando un token válido de una cuenta con rol `operador`.
3. Consultar la misma donación utilizando un token válido de una cuenta con rol `auditor`.

Los resultados obtenidos fueron los esperados. La solicitud sin token recibió HTTP `401 Unauthorized`; la solicitud autenticada con rol `operador` recibió HTTP `200 OK`; y la solicitud autenticada con rol `auditor` recibió HTTP `403 Forbidden`.

**Resultado: aprobado para el escenario ejecutado.**

La evidencia permite confirmar el comportamiento de autenticación y autorización del endpoint probado para los casos ejecutados. No demuestra que todos los endpoints ni todas las combinaciones de roles del sistema tengan sus permisos correctamente configurados.

## 2. Información de ejecución

| Campo | Información |
|---|---|
| Código del caso | E2E-03 |
| Periodo de ejecución | Octubre de 2026 |
| Ambiente | Pruebas (QA) |
| Responsable de ejecución | Tomás Silva |
| Servicio probado | Donación |
| Servicio de autenticación | Identidad |
| Método HTTP | `GET` |
| Endpoint | `/v1/donaciones/{id}` |
| Identificador de donación consultada | `513ec2d3-5d6c-4800-b62e-eab0f6325d92` |
| Tipo de prueba | Manual E2E |
| Resultado general | Aprobado |

### 2.1 Datos de prueba

| Campo | Valor |
|---|---|
| Identificador de donación | `513ec2d3-5d6c-4800-b62e-eab0f6325d92` |
| Rol autorizado probado | `operador` |
| Rol no autorizado probado | `auditor` |
| Primera verificación | Solicitud sin token |
| Segunda verificación | Solicitud con token de operador |
| Tercera verificación | Solicitud con token de auditor |
| Modificación de datos | Ninguna; todas las solicitudes fueron de lectura |

Por seguridad, no se incluyen las credenciales ni los tokens utilizados para autenticarse.

## 3. Resumen de resultados

| Verificación | Resultado esperado | Resultado obtenido | Veredicto |
|---|---|---|---|
| Consulta sin token | HTTP `401 Unauthorized` | HTTP `401` | Aprobado |
| Consulta con rol `operador` | HTTP `200 OK` | HTTP `200` | Aprobado |
| Consulta con rol `auditor` | HTTP `403 Forbidden` | HTTP `403` | Aprobado |
| Resultado general | Cumplimiento de los tres comportamientos esperados | Los tres resultados coincidieron con lo esperado | Aprobado |

## 4. Resultado por caso de prueba

### 4.1 TC-E2E-03: Control de acceso a la consulta de donaciones

**Objetivo**

Verificar que el endpoint de consulta de donaciones requiera autenticación, permita el acceso a un usuario con el rol autorizado y rechace la operación para un usuario autenticado cuyo rol no tenga permiso para realizarla.

**Precondiciones**

- Los servicios de Identidad y Donación están disponibles en el ambiente QA.
- Existe una donación de prueba que puede consultarse.
- Se dispone de una cuenta sintética de QA con rol `operador`.
- Se dispone de una cuenta sintética de QA con rol `auditor`.
- La API permite obtener tokens de acceso mediante el servicio de Identidad.
- El endpoint de consulta está disponible para ejecutar solicitudes HTTP `GET`.

### 4.2 Procedimiento detallado

#### Paso 1. Consultar la donación sin token de acceso

Se envió una solicitud HTTP `GET` al endpoint:

```text
GET /v1/donaciones/513ec2d3-5d6c-4800-b62e-eab0f6325d92
```

La solicitud no incluyó la cabecera `Authorization`.

**Resultado esperado:** el servicio rechaza la solicitud porque no se proporcionó un token de acceso válido.

**Resultado obtenido:** HTTP `401 Unauthorized`.

El cuerpo de la respuesta identificó el error como `sesion-invalida`.

**Identificador de correlación:** `bbc7c60f-3fe3-4612-9cee-8cbd426a0b04`.

**Veredicto:** aprobado.

#### Paso 2. Consultar la donación con un token de operador

Se inició sesión mediante el servicio de Identidad utilizando una cuenta sintética de QA con rol `operador`. Después se repitió la consulta incluyendo la cabecera `Authorization: Bearer` con el token obtenido.

**Resultado esperado:** el servicio permite la consulta porque el usuario está autenticado y tiene un rol autorizado para la operación.

**Resultado obtenido:** HTTP `200 OK`. La respuesta incluyó la información de la donación consultada y de la unidad asociada.

**Identificador de correlación:** `41bbc9b3-dbb0-47cc-8afe-502ddd84f10f`.

**Veredicto:** aprobado.

#### Paso 3. Consultar la donación con un token de auditor

Se inició sesión mediante el servicio de Identidad utilizando la cuenta sintética de QA con rol `auditor`. Se repitió la misma consulta utilizando el token de acceso obtenido.

**Resultado esperado:** el servicio rechaza la operación si el rol auditor no está autorizado para consultar la donación.

**Resultado obtenido:** HTTP `403 Forbidden`.

El cuerpo de la respuesta presentó los siguientes valores:

- Tipo de error: `operacion-no-permitida`.
- Título: `Operación no permitida`.
- Estado: `403`.
- Detalle: `El rol no autoriza esta operación.`

**Identificador de correlación:** `89b8d22b-79bb-41c4-a7f5-1f037508d0ec`.

**Veredicto:** aprobado.

### 4.3 Resultado final del caso

**Resultado esperado:** las solicitudes sin autenticación deben rechazarse; los usuarios con un rol autorizado deben poder consultar la donación; y los usuarios autenticados sin el permiso requerido deben recibir una respuesta de acceso denegado.

**Resultado obtenido:** las tres solicitudes produjeron los códigos HTTP esperados: `401`, `200` y `403`, respectivamente.

**Veredicto final: Aprobado.**

La prueba confirma el comportamiento esperado de autenticación y autorización para el endpoint y los roles evaluados.

## 5. Defectos encontrados

No se identificaron fallos durante la ejecución de este escenario.

Las tres respuestas coincidieron con los resultados esperados:

- `401 Unauthorized` para la solicitud sin token.
- `200 OK` para la solicitud autenticada con rol `operador`.
- `403 Forbidden` para la solicitud autenticada con rol `auditor`.

Esta conclusión se limita a las solicitudes ejecutadas y no representa una validación completa de todos los permisos de la aplicación.

## 6. Riesgos y pendientes

| Elemento | Impacto | Acción requerida | Responsable |
|---|---|---|---|
| Prueba limitada a un endpoint | Medio | Ampliar las verificaciones de autorización a otros endpoints protegidos. | QA |
| Prueba limitada a dos roles autenticados | Medio | Validar otros roles según la matriz de permisos definida para RedVital. | QA |
| Otros escenarios de autenticación no evaluados | Medio | Probar tokens vencidos, inválidos o con otros problemas de validación, si están contemplados en el plan de pruebas. | QA |
| Evidencias pendientes de organizar | Bajo | Conservar las respuestas HTTP y los identificadores de correlación en la carpeta de evidencias de Sprint 4. | QA |

## 7. Evidencias que deben conservarse

1. Respuesta HTTP `401` de la solicitud sin token.
2. Respuesta HTTP `200` de la consulta realizada con rol `operador`.
3. Respuesta HTTP `403` de la consulta realizada con rol `auditor`.
4. Identificadores de correlación de las tres solicitudes.
5. Comandos utilizados para ejecutar las solicitudes, sin credenciales ni tokens de acceso.

Las evidencias deben conservarse en la carpeta correspondiente a las pruebas E2E del Sprint 4. No se deben incluir contraseñas, tokens de acceso ni otros secretos.

## 8. Conclusión

La prueba E2E de autenticación y autorización de consulta de donaciones finalizó satisfactoriamente. Se verificó que el endpoint rechaza solicitudes sin autenticación, permite consultar la donación a un usuario con rol `operador` y deniega la operación a un usuario con rol `auditor`.

Los resultados obtenidos respaldan el funcionamiento esperado del control de acceso para el endpoint evaluado.

- **Resultado:** aprobado.
- **Defectos identificados durante la ejecución:** ninguno.
- **Acciones pendientes:** ampliar la cobertura a otros endpoints, roles y condiciones de autenticación.
- **Alcance de la conclusión:** únicamente el endpoint, los roles y las solicitudes descritos en este informe.

## 9. Revisión y aprobación

| Actividad | Persona | Fecha | Resultado |
|---|---|---|---|
| Revisión de resultados | Pendiente de confirmar | Pendiente | Pendiente |
| Aprobación final | Tomás Silva | Pendiente | Pendiente |

El documento permanece en borrador hasta completar la revisión de las evidencias y la aprobación correspondiente.
