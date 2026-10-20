
# Informe de pruebas TST-E2E-02 - Sprint 4

## Control del documento

| Campo | Información |
|---|---|
| Código del documento | TST-E2E-02-4 |
| Versión | 0.1 |
| Estado | Borrador |
| Sprint o entrega | 4 |
| Responsable del informe | Tomás Silva |
| Revisado por | Pendiente |
| Aprobado por | Pendiente |
| Fecha de creación | Pendiente de confirmar |
| Última actualización | Pendiente de confirmar |
| Plan de pruebas relacionado | Pendiente de asociar |
| Ambiente | QA |

## Historial de cambios

| Versión | Fecha | Descripción del cambio | Autor |
|---|---|---|---|
| 0.1 | 18/10/26 | Documentación inicial del flujo E2E de Registro de intención anónima y donación. | Tomás Silva |

---


## 1. Resumen ejecutivo

Se ejecutó una prueba de extremo a extremo para verificar el registro de una intención anónima, el registro de una donación asociada a dicha intención y la persistencia de la donación, su unidad y el evento de salida correspondiente.

La prueba incluyó los siguientes componentes:

1. Servicio de Identidad para obtener un token de acceso autorizado.
2. API de Donación para registrar una intención anónima.
3. API de Donación para registrar la donación mediante una solicitud autenticada.
4. Base de datos de Donación para comprobar la persistencia de la donación y la unidad generada.
5. Registro outbox para comprobar la generación y publicación del evento de finalización de la donación.

La API respondió HTTP `201 Created` tanto al registrar la intención como al registrar la donación. Las consultas a PostgreSQL confirmaron que la donación y su unidad quedaron persistidas, y que el evento `donation.completed` alcanzó el estado `publicado`.

**Resultado:** aprobado para el escenario ejecutado.

Este resultado valida el flujo concreto de registro de una intención anónima y una donación, incluyendo las verificaciones de persistencia y publicación del evento. No implica que todos los flujos E2E de RedVital estén cubiertos ni demuestra por sí solo el procesamiento del evento por todos sus posibles consumidores.

## 2. Información de ejecución

| Campo | Información |
|---|---|
| Periodo de ejecución | 18 de octubre de 2026, según las marcas de tiempo UTC registradas |
| Ambiente | Pruebas (QA) |
| Datos utilizados | Intención anónima y donación de prueba |
| Responsable de ejecución | Tomás Silva |
| Servicio de origen | Donación |
| Servicio de autenticación | Identidad |
| Persistencia verificada | PostgreSQL, base de datos `db_donacion` |
| Tablas verificadas | `public.donacion`, `public.unidad`, `public.evento_salida` |
| Resultado general | Aprobado |

### 2.1 Datos de prueba

| Campo | Valor |
|---|---|
| Código de intención | `NF7HT298BBQ4` |
| Identificador interno de intención | `05213b12-b51b-429c-b298-e04196959cd7` |
| Identificador de donación | `513ec2d3-5d6c-4800-b62e-eab0f6325d92` |
| Identificador de unidad | `6008010b-21d3-495c-a32f-9ca9eedcb38f` |
| Donante registrado | No aplica; se utilizó una intención anónima |
| Grupo sanguíneo | `O+` |
| Tipo de donación | `sangre_total` |
| Componente generado | `globulos_rojos` |
| Volumen de la unidad | 450 ml |
| Fecha de captación | `2026-10-19 02:33:08.384175 UTC` |
| Fecha de vencimiento de la unidad | `2026-11-29` |
| Identificador de institución | `a1000000-0000-4000-8000-000000000002` |
| Identificador del operador | `b2000000-0000-4000-8000-000000000006` |
| Correlación de la solicitud de donación | `d68e3a9b-a6bd-414b-acb9-54532fe39580` |
| Tipo de evento | `donation.completed` |
| Tema del evento | `redvital.donacion.donation-completed.v1` |
| Estado final del evento | `publicado` |

### 2.2 Preparación del escenario

#### Paso 1. Identificar la base de datos de Donación

Se identificó el contenedor PostgreSQL correspondiente al servicio de Donación en el host NBIG242. La consulta de bases de datos disponibles permitió confirmar que el nombre de la base de datos era `db_donacion`.

El contenedor utilizado fue `redvital-qa-datos-db-donacion-1`.

**Resultado:** se identificó la instancia de PostgreSQL adecuada para consultar la persistencia del escenario.

#### Paso 2. Registrar una intención anónima

Se envió una petición HTTP `POST` al endpoint `/v1/intenciones`, con los campos `grupo_sanguineo` y `municipio_ruta`. Para el escenario se utilizó el grupo sanguíneo `O+` y la ruta territorial `/00/05/05001`.

La solicitud incluyó una clave de idempotencia para identificar la operación.

**Resultado obtenido:** HTTP `201 Created`. La respuesta proporcionó el código de intención `NF7HT298BBQ4` y su fecha de expiración.

#### Paso 3. Obtener autorización para registrar la donación

Se inició sesión mediante el servicio de Identidad con una cuenta sintética de QA que tenía el rol `operador`. El token de acceso obtenido se utilizó para autorizar la solicitud de registro de la donación.

**Resultado:** se obtuvo la autorización requerida para ejecutar la operación.

**Consideración:** por seguridad, el token y las credenciales no se incluyen en el informe.

#### Paso 4. Registrar la donación mediante la API

Se envió una petición HTTP `POST` al endpoint `/v1/donaciones`, utilizando el código de la intención anónima, el tipo de donación `sangre_total`, el grupo sanguíneo `O+` y un componente de `globulos_rojos` con volumen de 450 ml.

La solicitud se ejecutó con autorización de operador y una clave de idempotencia.

**Resultado obtenido:** HTTP `201 Created`. La respuesta devolvió el identificador de la donación, la referencia a la intención y la información de la unidad generada.

**Consideración:** la intención y la donación se registraron mediante las API correspondientes. No fue necesario crear directamente la donación en la base de datos.

## 3. Resumen de resultados

| Verificación | Resultado |
|---|---|
| Identificación de la base de datos de Donación | Verificada |
| Registro de intención anónima | Aprobado |
| Respuesta HTTP del registro de intención | `201 Created` |
| Autenticación con cuenta sintética de operador | Aprobada |
| Registro de donación mediante API | Aprobado |
| Respuesta HTTP del registro de donación | `201 Created` |
| Persistencia de la donación | Verificada |
| Persistencia de la unidad | Verificada |
| Asociación de la unidad con la donación | Verificada |
| Registro del evento de salida | Verificado |
| Estado del evento de salida | `publicado` |
| Resultado general del escenario | Aprobado |

Este escenario se registra como una prueba E2E manual con verificaciones de API y base de datos. No se incluye en el total de pruebas automatizadas de las suites unitarias o de integración.

## 4. Resultados por caso de prueba

### 4.1 TC-E2E-02: Registro de intención anónima y donación

**Objetivo**

Verificar que una persona pueda registrar una intención anónima y que un operador autorizado pueda registrar una donación asociada a dicha intención. También se busca comprobar que la operación persista la donación y su unidad, y que genere el evento de salida correspondiente.

**Precondiciones**

- Los servicios de Identidad y Donación están disponibles.
- Las bases de datos corresponden al ambiente QA.
- Existe una cuenta sintética de QA con el rol `operador`.
- La API de Donación permite registrar intenciones anónimas sin autenticación.
- Se dispone de una ruta territorial válida para la intención.
- La solicitud de registro de donación incluye una clave de idempotencia.
- La base de datos permite consultar las tablas necesarias para verificar los resultados.

**Procedimiento detallado**

#### Paso 1. Registrar la intención anónima

Se envió una petición HTTP `POST` al endpoint:

```text
POST /v1/intenciones
```

El cuerpo de la solicitud incluyó el grupo sanguíneo `O+` y la ruta territorial `/00/05/05001`.

La respuesta devolvió el código de intención `NF7HT298BBQ4` y su fecha de expiración.

**Resultado esperado:** la API acepta la solicitud y devuelve un identificador que permite consultar la intención registrada.

**Resultado obtenido:** HTTP `201 Created`. Se recibió el código de intención `NF7HT298BBQ4`.

**Veredicto:** aprobado.

#### Paso 2. Obtener autorización mediante Identidad

Se envió una solicitud de autenticación al servicio de Identidad utilizando una cuenta sintética de QA con permisos de operador.

El token de acceso se utilizó en la cabecera `Authorization` de la petición posterior. No se incorporan credenciales ni tokens al informe.

**Resultado esperado:** el servicio de Identidad autentica la cuenta y entrega un token válido para registrar la donación.

**Resultado obtenido:** autenticación exitosa y obtención del token de acceso requerido.

**Veredicto:** aprobado.

#### Paso 3. Registrar la donación mediante la API

Se envió una petición HTTP `POST` al endpoint:

```text
POST /v1/donaciones
```

La solicitud incluyó los siguientes datos:

- Código de intención: `NF7HT298BBQ4`.
- Tipo de donación: `sangre_total`.
- Grupo sanguíneo: `O+`.
- Componente: `globulos_rojos`.
- Volumen: 450 ml.

La solicitud se envió con el token de operador y una clave de idempotencia.

**Resultado esperado:** la API registra la donación asociada a la intención y devuelve el identificador de la donación junto con la información de las unidades generadas.

**Resultado obtenido:** HTTP `201 Created`. La respuesta devolvió el identificador `513ec2d3-5d6c-4800-b62e-eab0f6325d92` y la unidad `6008010b-21d3-495c-a32f-9ca9eedcb38f`.

La unidad se devolvió con estado `captada`, grupo sanguíneo `O+`, volumen de 450 ml y fecha de vencimiento `2026-11-29`.

**Veredicto:** aprobado.

#### Paso 4. Verificar la persistencia de la donación

Se consultó la tabla `public.donacion` de la base de datos `db_donacion`, filtrando por el identificador de la donación.

Se verificaron los siguientes campos:

- `id`
- `donante_id`
- `intencion_id`
- `institucion_id`
- `campania_id`
- `fecha_captacion`
- `operador_id`

**Resultado obtenido:**

- `id`: `513ec2d3-5d6c-4800-b62e-eab0f6325d92`.
- `donante_id`: `NULL`, coherente con el registro mediante una intención anónima.
- `intencion_id`: `05213b12-b51b-429c-b298-e04196959cd7`.
- `institucion_id`: `a1000000-0000-4000-8000-000000000002`.
- `campania_id`: `NULL`.
- `operador_id`: `b2000000-0000-4000-8000-000000000006`.

La consulta devolvió una fila con los datos esperados. La donación quedó persistida y vinculada a la intención anónima.

**Veredicto:** aprobado.

#### Paso 5. Verificar la persistencia de la unidad

Se consultó la tabla `public.unidad`, filtrando por el identificador de la donación.

Se verificaron el identificador de la unidad, la relación con la donación, el volumen, la fecha de vencimiento y la fecha de creación.

**Resultado obtenido:**

- `id`: `6008010b-21d3-495c-a32f-9ca9eedcb38f`.
- `donacion_id`: `513ec2d3-5d6c-4800-b62e-eab0f6325d92`.
- `volumen_ml`: 450.
- `fecha_vencimiento`: `2026-11-29`.
- `creado_en`: `2026-10-19 02:33:08.384175+00`.

La consulta devolvió una fila asociada a la donación registrada.

**Resultado esperado:** existe una unidad persistida con los datos devueltos por la API.

**Resultado obtenido:** se confirmó la persistencia de la unidad y su asociación con la donación.

**Veredicto:** aprobado.

#### Paso 6. Verificar el registro y la publicación del evento de salida

Se consultó la tabla `public.evento_salida` para localizar el evento asociado al identificador de la donación.

Se verificaron los campos:

- `tipo`
- `tema`
- `clave`
- `clave_natural`
- `correlacion_id`
- `estado`
- `intentos_publicacion`
- `creado_en`
- `publicado_en`

**Resultado obtenido:**

- Tipo: `donation.completed`.
- Tema: `redvital.donacion.donation-completed.v1`.
- Clave: `513ec2d3-5d6c-4800-b62e-eab0f6325d92`.
- Correlación: `d68e3a9b-a6bd-414b-acb9-54532fe39580`.
- Estado: `publicado`.
- `intentos_publicacion`: `0`.
- Fecha de creación: `2026-10-19 02:33:08.384175+00`.
- Fecha de publicación: `2026-10-19 02:33:09.267506+00`.

**Resultado esperado:** el registro de la donación genera un evento de finalización y el proceso de publicación lo marca como publicado.

**Resultado obtenido:** el evento quedó registrado en la bandeja de salida con estado `publicado`.

El valor de `intentos_publicacion` se registra tal como aparece en la base de datos; no se interpreta como una medición independiente de todas las entregas internas del sistema.

**Veredicto:** aprobado.

### Resultado final del caso

**Resultado esperado:** la intención anónima se registra correctamente; un operador autorizado registra una donación asociada; la donación y su unidad quedan persistidas; y el evento de finalización alcanza el estado `publicado`.

**Resultado obtenido:** se verificaron todas las etapas descritas en el alcance del escenario.

**Veredicto: Aprobado.**

La prueba confirma el registro y la persistencia de la donación y la publicación del evento en la bandeja de salida. No se verificó de manera independiente el procesamiento de este evento por un consumidor posterior.

**### Evidencias que deben conservarse**

1. Respuesta HTTP del registro de intención, sin datos de autenticación.
2. Respuesta HTTP del registro de donación, con los identificadores de la donación y de la unidad.
3. Resultado de la consulta SQL a `public.donacion`.
4. Resultado de la consulta SQL a `public.unidad`.
5. Resultado de la consulta SQL a `public.evento_salida`.

Las evidencias deben conservarse en la carpeta correspondiente al informe de pruebas E2E. No se deben incluir contraseñas, tokens de acceso ni otros secretos.

## 5. Defectos encontrados

No se identificaron fallos en el recorrido ejecutado.

Las operaciones de registro devolvieron HTTP `201 Created`, y las consultas a PostgreSQL confirmaron la persistencia de la donación, su unidad y el evento publicado.

Este resultado se limita al escenario ejecutado. No demuestra que no existan defectos en otros flujos del servicio de Donación.

## 6. Riesgos y pendientes

| Elemento | Impacto | Acción requerida | Responsable |
|---|---|---|---|
| Cobertura limitada a una intención y una donación | Medio | Ejecutar escenarios adicionales con diferentes datos y condiciones. | QA |
| Consumo del evento por servicios posteriores no verificado | Medio | Revisar los logs y la persistencia del consumidor correspondiente, si aplica al contrato del evento. | QA / equipo técnico |
| Comportamiento ante solicitudes duplicadas no verificado | Medio | Ejecutar casos específicos de idempotencia para confirmar el comportamiento ante reintentos. | QA |
| Validaciones negativas no incluidas en este escenario | Medio | Probar datos inválidos, autorización insuficiente y errores de validación. | QA |
| Evidencias sin ubicación documental definitiva | Bajo | Guardar las respuestas y consultas SQL en la carpeta de evidencias de Sprint 4. | QA |

## 7. Conclusión

La prueba E2E de registro de intención anónima y donación finalizó correctamente. Se verificó el registro de la intención, la autenticación del operador, la creación de la donación mediante la API, la persistencia de la unidad y la publicación del evento `donation.completed`.

La evidencia permite confirmar que el escenario funcionó de acuerdo con las verificaciones establecidas.

- **Recomendación:** escenario aprobado.
- **Defectos identificados durante la ejecución:** ninguno.
- **Acciones pendientes:** ampliar la cobertura E2E, comprobar los casos de idempotencia y autorización, y verificar el procesamiento posterior del evento cuando corresponda.

La aprobación se limita al flujo y los datos utilizados en esta ejecución.

## 8. Revisión y aprobación

| Actividad | Persona | Fecha | Resultado |
|---|---|---|---|
| Revisión de resultados | Carlos | Pendiente | Pendiente |
| Aprobación final | Tomás Silva | Pendiente | Pendiente |

El documento permanece en borrador hasta completar la revisión de las evidencias y la aprobación correspondiente.