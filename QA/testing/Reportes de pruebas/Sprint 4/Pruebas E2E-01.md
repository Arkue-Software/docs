
# Informe de pruebas TST-E2E-01 - Sprint 4

## Control del documento

| Campo | Información |
|---|---|
| Código del documento | TST-E2E-01-4 |
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
| 0.1 | 18/10/26 | Documentación inicial del flujo E2E de publicación de campaña y propagación a Donación. | Tomás Silva |

---

## 1. Resumen ejecutivo

Se ejecutó una prueba de extremo a extremo para verificar la publicación de una campaña mediante la API de Campañas y la propagación del evento correspondiente hasta su persistencia en la proyección de Donación.

La prueba incluyó los siguientes componentes:

1. Servicio de Identidad para obtener un token de acceso autorizado.
2. API de Campañas para ejecutar la publicación.
3. Registro outbox para comprobar la generación y publicación del evento.
4. Kafka como sistema de mensajería.
5. Consumidor de eventos del servicio de Donación.
6. Base de datos de Donación para comprobar la proyección persistida.

La API respondió HTTP `200 OK`, el registro outbox quedó en estado `publicado`, los logs confirmaron que el consumidor aplicó el evento y la consulta a `campania_publicada` devolvió la campaña esperada.

**Resultado:** aprobado para el escenario ejecutado.

Este resultado valida una publicación concreta y no implica que todos los flujos E2E de RedVital estén cubiertos.

## 2. Información de ejecución

| Campo | Información |
|---|---|
| Periodo de ejecución | 18 de octubre de 2026, según las marcas de tiempo UTC registradas |
| Ambiente | QA |
| Datos utilizados | Campaña e institución sintéticas |
| Responsable de ejecución | Tomás Silva |
| Servicio de origen | Campañas |
| Servicio consumidor | Donación |
| Mensajería | Kafka |
| Persistencia verificada | PostgreSQL, tabla `public.campania_publicada` |

### 2.1 Datos de prueba

| Campo | Valor |
|---|---|
| Identificador de campaña | `e5100000-0000-4000-8000-202610170001` |
| Nombre | `QA SINTETICA E2E 20261017` |
| Institución | `a1000000-0000-4000-8000-000000000001` |
| Código territorial | `05001` |
| Ruta territorial | `/00/05/05001` |
| Cupo total inicial | 10 |
| Estado inicial registrado | `borrador` |
| Estado final esperado | `publicada` |
| Correlación de la solicitud | `3bbb1c99-76bc-4b73-9017-d17d994e52e8` |
| Identificador del evento procesado | `d48d9c1c-96ae-4d20-b869-01bfe7695385` |
| Tema del evento | `redvital.campanias.campaign-published.v1` |

### 2.2 Preparación del escenario

#### Paso 1. Preparar los datos de prueba

Se creó previamente un registro de campaña sintética directamente en la base de datos de Campañas para disponer de los datos necesarios para ejecutar la prueba. Este registro se utilizó como dato de preparación y no como parte de la operación de publicación que se pretendía validar.

La campaña se identificó con el UUID `e5100000-0000-4000-8000-202610170001`, con nombre `QA SINTETICA E2E 20261017`, asociada a una institución de prueba y al territorio `05001`. El registro se preparó inicialmente en estado `borrador`.

**Resultado:** se dispuso de una campaña sintética para ejecutar el flujo de publicación.

**Consideración:** la creación directa en la base de datos forma parte de la preparación del escenario. Por tanto, no se considera evidencia de que la API de creación de campañas funcione correctamente.

#### Paso 2. Autenticarse mediante el servicio de Identidad

Se envió una solicitud al servicio de Identidad para iniciar sesión con una cuenta sintética de QA. La respuesta proporcionó un token de acceso que posteriormente se utilizó para autorizar la petición de publicación de la campaña.

**Resultado:** se obtuvo el token de acceso necesario para realizar la operación autorizada.

#### Paso 3. Publicar la campaña mediante la API

Se envió una petición HTTP `POST` al endpoint `/v1/campanias/{id}/publicacion`, utilizando el identificador de la campaña preparada y el token obtenido en el paso anterior.

**Resultado:** la API respondió HTTP `200 OK` y devolvió la campaña en estado `publicada`.

**Importancia para la prueba E2E:** aunque el registro inicial de la campaña se preparó directamente en la base de datos, la operación de publicación sí se ejecutó a través de la API. A partir de esa operación se verificó la generación y propagación del evento hasta Donación.

## 3. Resumen de resultados

| Verificación | Resultado |
|---|---|
| Disponibilidad de servicios requeridos | Verificada durante la preparación |
| Autenticación con cuenta sintética autorizada | Aprobada |
| Publicación mediante API | Aprobada |
| Respuesta HTTP | `200 OK` |
| Registro del evento outbox | Verificado |
| Estado del evento outbox | `publicado` |
| Procesamiento por consumidor de Donación | Verificado en logs |
| Persistencia en proyección de Donación | Verificada |
| Resultado general del escenario | Aprobado |

Este escenario se registra como una prueba E2E manual con verificaciones de API, base de datos y logs. No se incluye en el total de pruebas automatizadas de las suites unitarias o de integración.

## 4. Resultados por caso de prueba

### 4.1 TC-E2E-01: publicación de campaña y propagación del evento

**Objetivo**

Verificar que una campaña pueda publicarse mediante la API autorizada y que el evento de publicación sea procesado por Donación, dejando persistida la información correspondiente.

**Precondiciones**

- Los servicios de Identidad, Campañas, Kafka y Donación están disponibles.
- Existe una cuenta sintética de QA con permisos suficientes.
- Existe una campaña sintética válida en estado `borrador`.
- Las bases de datos y los servicios corresponden al ambiente QA.
- La cuenta utilizada tiene autorización para operar sobre la institución de la campaña.

**Procedimiento detallado**

#### Paso 1. Comprobar la disponibilidad de los servicios

Se verificó la disponibilidad de los servicios requeridos antes de ejecutar la operación de publicación.

La comprobación de disponibilidad reduce la posibilidad de interpretar una caída de infraestructura como un fallo funcional del flujo.

**Resultado:** los servicios requeridos estaban disponibles durante la ejecución.

#### Paso 2. Obtener autorización

Se inició sesión con una cuenta sintética de QA mediante el servicio de Identidad. La respuesta de autenticación proporcionó un token de acceso que se utilizó para autorizar la operación de publicación.

El token no debe incorporarse al informe ni compartirse como evidencia. Basta con registrar que la autenticación fue exitosa y que la solicitud posterior utilizó autorización válida.

**Resultado:** autenticación exitosa con una cuenta autorizada.

#### Paso 3. Publicar la campaña mediante la API

Se envió una petición HTTP `POST` al endpoint:

```text
POST /v1/campanias/{id}/publicacion
```

La petición incluyó el identificador de la campaña sintética y el token de acceso en la cabecera de autorización.

**Resultado esperado:** la API acepta la operación y devuelve el estado de publicación correspondiente.

**Resultado obtenido:** HTTP `200 OK`, estado `publicada` y fecha de publicación `2026-10-19T01:13:07.4183779+00:00`.

#### Paso 4. Verificar el registro outbox

Se consultó la tabla `public.evento_salida` en la base de datos de Campañas para comprobar que la operación hubiera generado el evento y que el proceso de publicación lo hubiera marcado como publicado.

Los campos verificados incluyeron:

- `tipo`
- `tema`
- `clave_natural`
- `correlacion_id`
- `estado`
- `intentos_publicacion`
- `creado_en`
- `publicado_en`

**Resultado obtenido:**

- Tipo: `campaign.published`.
- Tema: `redvital.campanias.campaign-published.v1`.
- Estado: `publicado`.
- Intentos de publicación registrados como reintentos: `0`.
- Correlación: `3bbb1c99-76bc-4b73-9017-d17d994e52e8`.

El valor cero en `intentos_publicacion` se registra tal como aparece en la base de datos; no debe interpretarse como una medición independiente de todas las entregas internas del sistema.

#### Paso 5. Verificar el procesamiento en Donación

Se revisaron los logs del consumidor `ConsumidorCampaniaPublicada`.

El log registró el procesamiento del evento:

```text
Evento d48d9c1c-96ae-4d20-b869-01bfe7695385
de campaña e5100000-0000-4000-8000-202610170001 aplicado
```

**Resultado esperado:** el consumidor procesa el evento de la campaña y registra el resultado en sus logs.

**Resultado obtenido:** el log confirmó que el evento fue aplicado.

#### Paso 6. Verificar la persistencia en la proyección

Se consultó la estructura de la tabla `public.campania_publicada` de la base de datos `db_donacion`. Se identificó `campania_id` como la columna que permite localizar la campaña y `evento_id` como el identificador del evento asociado.

Posteriormente, se consultó la fila correspondiente al identificador sintético.

**Resultado obtenido:**

- `campania_id`: `e5100000-0000-4000-8000-202610170001`.
- `nombre`: `QA SINTETICA E2E 20261017`.
- `institucion_id`: `a1000000-0000-4000-8000-000000000001`.
- `territorio_codigo`: `05001`.
- `estado`: `publicada`.
- `evento_id`: `d48d9c1c-96ae-4d20-b869-01bfe7695385`.

El identificador de evento coincide con el que apareció en el log del consumidor. La fila también contiene la fecha de publicación y la fecha de actualización de la proyección.

### Resultado final del caso

**Resultado esperado:** la campaña se publica mediante la API, el evento se registra y se propaga, el consumidor lo aplica y la proyección de Donación persiste los datos correspondientes.

**Resultado obtenido:** se verificaron todas las etapas del escenario.

**Veredicto: Aprobado.**

### Evidencias que deben conservarse

1. Respuesta HTTP de la operación de publicación, sin credenciales ni tokens.
2. Resultado de la consulta SQL de `evento_salida`.
3. Fragmento del log que confirma la aplicación del evento.
4. Resultado de la consulta SQL de `campania_publicada`.

Las evidencias pueden almacenarse en una carpeta del informe con nombres consistentes. Las consultas y los resultados deben conservarse sin incluir información de autenticación.

## 5. Defectos encontrados

No se identificaron fallos en el recorrido E2E ejecutado.

Este resultado no demuestra que no existan defectos en otros escenarios. En particular, el comportamiento observado anteriormente en el filtro `estado=borrador` requiere una prueba independiente.

## 6. Riesgos y pendientes

| Elemento | Impacto | Acción requerida | Responsable |
|---|---|---|---|
| Cobertura limitada a una publicación | Medio | Ejecutar otros escenarios E2E de acuerdo con los requisitos. | QA |
| Filtro de estado observado con comportamiento inesperado | Medio | Reproducir la consulta y verificar el contrato de la API. | QA / equipo técnico |
| Autorización no cubierta en todos los escenarios | Medio | Ejecutar casos positivos y negativos con cuentas sintéticas autorizadas. | QA |
| Comportamiento ante publicaciones repetidas no verificado | Medio | Definir y ejecutar un caso de publicación repetida según el contrato. | QA |
| Evidencias sin ubicación documental definitiva | Bajo | Guardar los resultados en la carpeta de evidencias de la entrega. | QA |

## 7. Conclusión

La prueba E2E de publicación de campaña y propagación a Donación finalizó correctamente. La evidencia cubre la respuesta de la API, el registro outbox, el procesamiento del evento y su persistencia en la proyección del servicio consumidor.

- **Recomendación:** escenario aprobado.
- **Riesgos aceptados:** ninguno formalmente documentado.
- **Acciones pendientes:** ampliar la cobertura E2E y documentar los resultados de los escenarios adicionales.

La aprobación se limita al flujo y los datos utilizados en esta ejecución.

## 8. Revisión y aprobación

| Actividad | Persona | Fecha | Resultado |
|---|---|---|---|
| Revisión de resultados | Carlos | Pendiente | Pendiente |
| Aprobación final | Tomás Silva | Pendiente | Pendiente |

El documento permanece en borrador hasta completar la revisión de las evidencias.
