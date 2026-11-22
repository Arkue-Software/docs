# REDVITAL
## Documento de Pruebas — TD Final

**Metodología, estrategia, cobertura y resultados consolidados de pruebas**

**Arkhé Software S.A.S.**  
*De la raíz a la red*

| Campo | Información |
|---|---|
| Código | TD-PRUEBAS-FINAL |
| Versión | 3.0 |
| Estado | Borrador vivo para revisión y actualización |
| Sprint o entrega | Consolidación de Sprint 3 y Sprint 4 |
| Responsable del documento | Tomás Silva — QA / Tester |
| Ambiente principal | Pruebas (QA) |
| Rama de referencia | `release` |
| Fecha de actualización | 05/11/2026 |
| Documento base | TD V1 y TD V2 |
| Clasificación | Uso interno del proyecto |

---

# Control del documento

| Campo | Información |
|---|---|
| Código del documento | TD-PRUEBAS-FINAL |
| Responsable de pruebas | Tomás Silva |
| Elaboración y ejecución QA | Tomás Silva |
| Ambiente | Pruebas (QA) |
| Rama | `release` |
| Estado | Borrador para revisión |
| Documentos consolidados | TD V1 — Sprint 3; TD V2 — Sprint 4 |
| Informes relacionados | Informes individuales de Institucional, Bases de Datos, Gateway, Identidad, Donación y Campañas |

## Historial de cambios

| Versión | Fecha | Autor | Descripción |
|---|---|---|---|
| 1.0 | 08/10/2026 | Equipo Arkhé | Documento de pruebas del Sprint 3 (TD V1). |
| 2.0 | 18/10/2026 | Tomás Silva | Actualización de estrategia y resultados disponibles del Sprint 4 (TD V2). |
| 2.2 | 02/11/2026 | Equipo Arkhé | Incorporación de resultados automatizados y de rendimiento; actualización de trazabilidad y hallazgos. |
| 3.0 | 05/11/2026 | Tomás Silva | Consolidación de TD V1 y TD V2; incorporación de resultados QA de Institucional, Bases de Datos, Gateway, Identidad, Donación y Campañas; actualización del estado general y exclusiones de cobertura. |

## Aprobaciones

| Rol | Responsable | Firma / Fecha |
|---|---|---|
| QA / Tester | Tomás Silva | Pendiente |
| Scrum Master / Project Manager | Carlos Santiago Pinzón | Pendiente |
| Product Owner | Alexander Aponte | Pendiente |
| Arquitectura de Software | Sara | Pendiente |
| DevOps | Veli | Pendiente |

---

# 1. Introducción

## 1.1 Propósito

Este documento consolida la estrategia, el diseño, la cobertura y los resultados de las pruebas realizadas sobre RedVital durante los Sprint 3 y 4. Su propósito es mantener en un único documento la planificación heredada, las verificaciones ejecutadas, las limitaciones identificadas y las actividades pendientes.

El documento se mantendrá como una versión viva: los resultados nuevos se incorporarán conservando la fecha, el componente, el tipo de prueba, el ambiente, la evidencia y el alcance real de cada ejecución.

## 1.2 Alcance

El alcance documentado comprende las verificaciones que cuentan con definición o evidencia disponible sobre:

- Servicio de Identidad.
- Servicio Institucional.
- Servicio de Donación.
- Servicio de Campañas.
- Servicio de Notificaciones, en lo relacionado con su inclusión en la arquitectura y las actividades pendientes de verificación.
- Bases de datos PostgreSQL y permisos de cuentas de servicio.
- Apache APISIX como API Gateway y Caddy como punto de entrada.
- Kafka y los mecanismos de publicación de eventos.
- Integración entre servicios.
- Pruebas unitarias, de componentes, integración, API, seguridad funcional, persistencia y E2E de backend/API.
- Pruebas de rendimiento LT-01 a LT-04 con Gatling.
- Validaciones manuales y automatizadas de QA que tengan evidencia verificable.

La inclusión de un servicio o funcionalidad en el alcance no significa que todas sus operaciones, roles, escenarios o requisitos hayan sido probados.

## 1.3 Exclusiones y limitaciones

No se afirma que la plataforma completa esté cubierta exhaustivamente. Permanecen fuera de una declaración de cobertura integral:

- Todas las combinaciones posibles de roles y endpoints.
- Todos los escenarios negativos y de borde.
- Todos los requisitos RF, RNF y escenarios de calidad.
- Todos los consumidores posteriores de los eventos publicados.
- La capacidad de rendimiento con la concurrencia objetivo de 20, 50 y 200 sesiones simultáneas.
- La suite completa del Servicio de Notificaciones.
- Los escenarios heredados que no tengan evidencia actual de ejecución.
- Las operaciones de escritura de Institucional que no fueron ejecutadas durante la validación reportada.
- Los casos de Gateway que requieren condiciones de entorno no disponibles o acciones que no se ejecutaron.

Los resultados se interpretan según el alcance real de cada caso. Una respuesta satisfactoria de API no demuestra por sí sola que todos los servicios posteriores hayan procesado la operación.

## 1.4 Referencias

- TD V1 — Documento de Pruebas, Sprint 3.
- TD V2 — Documento de Pruebas, Sprint 4.
- SRS V5.1.
- SAD V3.1, incluida la sección 35.3 de métricas y umbrales.
- Catálogo de escenarios de calidad.
- Documento de Herramientas, Políticas y Lineamientos V4.
- Informes LT-01 a LT-04 de rendimiento.
- Informes individuales E2E de backend/API.
- Informes QA por componente.
- Contratos OpenAPI y AsyncAPI aplicables.

Las versiones definitivas y ubicaciones de las referencias deberán confirmarse en la revisión documental.

## 1.5 Arquitectura de referencia

La arquitectura de RedVital contempla los servicios de Identidad, Institucional, Campañas, Donación y Notificaciones. La infraestructura de integración utiliza:

- Caddy como punto de entrada.
- Apache APISIX como API Gateway.
- PostgreSQL para persistencia.
- Apache Kafka para comunicación asíncrona.
- Transactional Outbox en los productores que requieren consistencia entre transacción local y publicación de eventos.

No se consideran YARP ni llamadas HTTP directas de negocio entre microservicios como la arquitectura vigente descrita en TD V1.

---

# 2. Estrategia y metodología de pruebas

## 2.1 Enfoque

Las pruebas se priorizan por riesgo y por el impacto que tendría un fallo en la operación del sistema. Se consideran especialmente:

1. Confidencialidad de la información.
2. Bloqueo de unidades no aptas o vencidas.
3. Autenticación, autorización y jurisdicción.
4. Integridad e idempotencia.
5. Trazabilidad y auditoría.
6. Funcionamiento general e interoperabilidad.

El camino feliz no es suficiente cuando una operación tiene permisos, estados o restricciones: deben considerarse también escenarios negativos y de borde.

## 2.2 Tipos y niveles de prueba

| Tipo | Propósito |
|---|---|
| Unitaria / componentes | Verificar reglas y componentes de manera aislada. |
| Integración | Comprobar la interacción entre componentes y dependencias, indicando cuáles son reales y cuáles simuladas. |
| API / funcional | Validar operaciones, contratos, códigos HTTP, datos retornados y validaciones. |
| Seguridad funcional | Verificar autenticación, autorización, roles, jurisdicción y restricciones. |
| Persistencia / bases de datos | Verificar permisos, integridad, restricciones y persistencia de datos. |
| Auditoría | Comprobar registro, atribución y consulta de acciones. |
| E2E de backend/API | Verificar recorridos que atraviesan operaciones de servicios y dependencias reales declaradas. |
| Rendimiento | Medir el comportamiento bajo las cargas definidas. |
| Infraestructura / configuración | Verificar disponibilidad y configuración de componentes de soporte. |

Una prueba E2E puede ser también una prueba de integración; para los resúmenes cuantitativos se registra una sola vez bajo su clasificación más específica, evitando duplicar conteos.

## 2.3 Clases de camino

| Código | Nombre | Descripción |
|---|---|---|
| F | Feliz | Entrada válida, permisos correctos y resultado esperado. |
| N | Negativo | Entrada inválida, rol sin permiso, jurisdicción ajena o transición prohibida. |
| B | Borde | Valor en un límite temporal, cuantitativo o de repetición. |
| X | Fallo inducido / carga | Dependencia no disponible o carga controlada. |

## 2.4 Herramientas

| Categoría | Herramientas |
|---|---|
| Unitarias Java | JUnit 5, Mockito, Maven |
| Unitarias .NET | xUnit |
| Integración | Testcontainers, PostgreSQL, Kafka |
| API y contratos | OpenAPI 3.1, cliente HTTP |
| Eventos | Kafka y contratos AsyncAPI |
| Infraestructura | Docker, Docker Compose, Caddy, Apache APISIX |
| Rendimiento | Gatling 3.16.0 |
| Observabilidad | Prometheus, Grafana y logs de servicios |
| Control de versiones y CI | GitHub y GitHub Actions |
| Análisis de seguridad | Gitleaks y Grype, cuando aplique |

La mención de una herramienta no constituye evidencia de que todas las pruebas previstas con ella hayan sido ejecutadas.

## 2.5 Ambiente y datos

El ambiente principal es Pruebas (QA). Las pruebas deben registrar la versión, rama y configuración realmente utilizadas, así como las dependencias reales o simuladas.

Se utilizan datos sintéticos y cuentas de prueba. Los informes no deben incluir contraseñas, tokens, claves privadas, secretos, identificadores personales innecesarios ni direcciones IP sensibles. Las evidencias deben depurarse cuando contengan información que no sea necesaria para demostrar el resultado.

## 2.6 Criterios de entrada

Antes de ejecutar una prueba se verifica, según corresponda:

- Disponibilidad del servicio y sus dependencias.
- Ambiente QA correcto.
- Rama y versión identificadas.
- Datos y cuentas de prueba preparados.
- Permisos requeridos disponibles.
- Precondiciones y resultados esperados definidos.
- Herramientas disponibles.
- Evidencias capturables sin exponer secretos.

## 2.7 Criterios de salida

Un caso se considera aprobado cuando se completan los pasos previstos, el resultado observado cumple el esperado y existe evidencia suficiente. Las dependencias simuladas y limitaciones deben quedar explícitas.

Una suite se considera aprobada cuando finaliza correctamente y no presenta pruebas fallidas, salvo que exista un criterio formal distinto.

El cierre global requiere revisar resultados, defectos, pruebas pendientes y evidencias. La aprobación de casos individuales no implica la aprobación integral del sistema.

## 2.8 Gestión de defectos y observaciones

Los hallazgos se clasifican como:

- **Defecto funcional:** comportamiento contrario a un requisito o contrato confirmado.
- **Problema de configuración o entorno:** una prueba no puede ejecutarse por una dependencia o configuración.
- **Observación pendiente:** comportamiento que requiere investigación.
- **Limitación de cobertura:** funcionalidad o escenario que no fue probado.

Una prueba fallida no se clasifica automáticamente como defecto funcional. Debe investigarse si la causa corresponde al producto, infraestructura, datos o configuración de prueba.

---

# 3. Diseño y cobertura planificada heredada del TD V1

## 3.1 Casos de prueba planificados

TD V1 definió 23 casos de prueba y 7 pruebas de carga, sobrecarga y estrés. De los 23 casos, 18 estaban contemplados para Sprint 3 y 5 quedaban fuera de ese sprint.

| Tipo | Casos | Total planificado | En Sprint 3 |
|---|---|---:|---:|
| Seguridad | TC-001 a TC-003 | 3 | 3 |
| Seguridad física | TC-005, TC-006, TC-014 | 3 | 3 |
| Funcional | TC-007, TC-022, TC-023 | 3 | 3 |
| Integridad e idempotencia | TC-004, TC-010 | 2 | 2 |
| Auditoría | TC-008, TC-009 | 2 | 2 |
| Resiliencia | TC-011 | 1 | 1 |
| Contrato | TC-012 | 1 | 1 |
| Instalabilidad | TC-013 | 1 | 1 |
| Interfaz y usabilidad | TC-015 a TC-018 | 4 | 0 |
| Mantenibilidad | TC-019 | 1 | 0 |
| Integración | TC-020 | 1 | 1 |
| Datos | TC-021 | 1 | 1 |
| **Casos de prueba** | | **23** | **18** |
| Carga | LT-01 a LT-03, LT-05, LT-06 | 5 | 0 |
| Sobrecarga | LT-04 | 1 | 0 |
| Estrés | LT-07 | 1 | 0 |
| **Carga y estrés** | | **7** | **0** |

Esta tabla conserva el diseño y alcance histórico del TD V1. No debe interpretarse como un conteo de casos aprobados en la ejecución actual.

## 3.2 Casos y escenarios de calidad

Los casos del TD V1 se trazaron a escenarios de calidad y requisitos funcionales, entre ellos:

| Caso | Escenario / requisito | Objetivo |
|---|---|---|
| TC-001 | EC-01 | Control de acceso y jurisdicción. |
| TC-002 | EC-03 | Autenticidad de sesión. |
| TC-003 | EC-02 | Confidencialidad de causa clínica. |
| TC-004 | EC-04 / EC-36 | Máquina de estados e integridad. |
| TC-005 | EC-12 | Bloqueo de unidades no aptas o vencidas. |
| TC-006 | EC-39 | Límite operativo de despacho. |
| TC-007 | EC-13 | Vencimiento automático. |
| TC-008 | EC-33 | Atribución de acciones automáticas. |
| TC-009 | EC-05 | Reconstrucción de trazabilidad. |
| TC-010 | EC-10 | Idempotencia del registro de donación. |
| TC-011 | EC-08 / RNF-06 | Indisponibilidad temporal de Kafka y Transactional Outbox. |
| TC-012 | EC-22 | Evolución de contratos. |
| TC-013 | EC-23 | Instalabilidad. |
| TC-014 | EC-19 / EC-41 | Acciones irreversibles. |
| TC-020 | Flujo del incremento | Integración a través del gateway. |
| TC-021 | HU-302 | Coherencia de datos sintéticos. |
| TC-022 | RF-08 | Funcionalidad de Campañas. |
| TC-023 | RF-02, RF-03, RF-05 | Donación por API. |

La ejecución de cada caso debe mantenerse vinculada a su evidencia individual. Los casos diseñados pero no ejecutados conservan estado pendiente, bloqueado o fuera del incremento según corresponda.

## 3.3 Métricas y umbrales relevantes

| Característica | Métrica / criterio |
|---|---|
| Confidencialidad | Cero exposición de campos de causa clínica. |
| Trazabilidad | Reconstrucción de la cadena en menos de 5 minutos. |
| Atribución | Acciones automáticas atribuidas al sistema, no al usuario conectado. |
| Seguridad física | Cero despachos por institución distinta a la custodia. |
| Vencimiento | Unidades vencidas retiradas de disponibilidad en máximo 15 minutos. |
| Idempotencia | Cero duplicados ante 100 reenvíos. |
| Resiliencia | La transacción local no se pierde cuando Kafka no está disponible; el evento queda pendiente y se publica al recuperarse el broker. |
| Rendimiento | p95 de consulta de inventario ≤ 3 s y p99 ≤ 5 s en carga nominal. |
| Pico | p95 ≤ 3 s y tasa de error < 1 % en el escenario definido. |
| Instalabilidad | Arranque completo en ≤ 10 minutos con un comando. |

---

# 4. Resultados consolidados

## 4.1 Resumen de suites automatizadas del TD V2

El siguiente consolidado excluye los resultados de interfaz web y considera únicamente las suites automatizadas de servicios e integración registradas en TD V2.

| Suite | Aprobadas | Fallidas | Total | Observación |
|---|---:|---:|---:|---|
| Donación — JUnit 5 / Maven | 181 | 0 | 181 | Suites `MotorTransicionesTest`, `PoliticasTest` y `ArquitecturaTest`. |
| Identidad — xUnit | 20 | 0 | 20 | Suite aprobada en la ejecución registrada. |
| Campañas — xUnit | 21 | 3 | 24 | Tres fallos de `EventosPostgresPruebas` por ruta de fixture. |
| Integración de Donación | 7 | 0 | 7 | PostgreSQL y Kafka mediante Testcontainers; Identidad/JWT simulado y evento de campaña publicado directamente en Kafka. |
| **Total automatizado** | **229** | **3** | **232** | **98,71 % aprobadas; 1,29 % fallidas.** |

Cálculo: 229 / 232 × 100 = 98,71 %; 3 / 232 × 100 = 1,29 %.

Los tres fallos de Campañas corresponden a la preparación del fixture, que no encuentra `00_roles.sql` debido a una ruta relativa heredada del repositorio único anterior. La causa identificada es de configuración de la prueba y no constituye, con la evidencia disponible, un defecto funcional confirmado. Las pruebas deben repetirse con la ruta de migraciones correcta porque cubren la bandeja de salida de Campañas y el consumidor asociado a RF-25.

El resultado de integración de Donación no representa una integración completa de todos los servicios reales: Identidad/JWT se simuló y el evento de campaña se publicó directamente en Kafka.

## 4.2 Pruebas funcionales y de seguridad de Institucional

**Fecha de ejecución:** 05/11/2026.  
**Tipo:** pruebas manuales funcionales de API, autenticación y autorización.  
**Ambiente:** QA.  
**Dependencias:** servicio Institucional e Identity reales en QA; solicitudes HTTP directas.

Se documentaron 16 verificaciones:

| Grupo funcional | Cantidad | Resultado |
|---|---:|---|
| Disponibilidad del servicio | 1 | Aprobada |
| Autenticación y control de acceso | 4 | Aprobadas |
| Consulta y paginación de auditoría | 6 | Aprobadas |
| Consulta y paginación de territorios | 1 | Aprobada |
| Consulta y paginación de instituciones | 1 | Aprobada |
| Consulta de indicadores | 1 | Aprobada |
| Validación de parámetros | 2 | Aprobadas |
| **Total** | **16** | **16 aprobadas** | Los resultados observados incluyen:

- `/salud`: HTTP 200.
- Endpoints protegidos sin token: HTTP 401.
- Rol `admin_nacional` consultando auditoría: HTTP 403, conforme a la política de acceso.
- Rol `auditor`: consulta de auditoría HTTP 200, paginación y filtro por correlación verificados.
- Tamaño de página fuera del máximo permitido: HTTP 400.
- Consulta de territorios: 10 elementos en primera página, total reportado 123.
- Consulta de instituciones: 10 elementos en primera página, total reportado 22.
- Consulta de indicadores con rol `admin_banco`: HTTP 200.

No se ejecutaron operaciones POST o PUT sobre Institucional. Tampoco se afirma que se hayan probado todos los filtros ni todas las combinaciones de roles.

## 4.3 Pruebas de bases de datos

**Tipo:** seguridad, permisos, restricciones e integridad.  
**Ambiente:** QA.  
**Alcance:** verificaciones sobre permisos de cuentas de servicio, roles y tablas de auditoría.

Resultados registrados:

- La cuenta de servicio pudo insertar y actualizar usuarios según los permisos previstos.
- Se rechazó el borrado de usuarios.
- Se rechazó la inserción de roles por la cuenta de servicio.
- Se rechazaron operaciones de actualización, eliminación y truncado sobre auditoría.
- Se rechazó la creación de objetos en el esquema `public`.
- Se verificaron seis roles configurados.
- Se rechazó una sesión que excedía el límite temporal definido.
- Se rechazó una institución sin jurisdicción válida.
- Se rechazó un cliente de servicio desconocido.
- Se permitió la inserción de auditoría anónima válida.
- Se rechazó un `actor_id` anónimo no permitido.
- Se rechazó un identificador de correlación superior a la longitud admitida.

Las pruebas funcionales que requerían escrituras temporales se realizaron con reversión de transacción, sin dejar cambios permanentes. No se ejecutó `verificar-fase1.sh` por incluir operaciones de escritura y truncado que no correspondían al alcance seguro de QA.

No se establece un total único de casos para esta sección porque las verificaciones fueron registradas como controles y escenarios, no como una matriz consolidada con identificador individual para cada uno.

## 4.4 Pruebas de Gateway

**Tipo:** seguridad, configuración y validación de escenarios.  
**Ambiente:** QA y entorno local simulado, según el caso.

| Verificación | Resultado | Alcance |
|---|---|---|
| Rechazo de JWT firmado con llave de desarrollo en Gateway QA | Aprobada — HTTP 401 | Verifica que QA no acepta el token firmado con la llave de desarrollo utilizada en la prueba. |
| Escenario OIDC y denegación de auditoría | Aprobado en simulación local | No representa una integración real contra todos los servicios QA. |
| Validación YAML | 3 advertencias no bloqueantes | No se registraron errores bloqueantes en la validación realizada. |

Las pruebas T-305.1, T-305.2, T-305.3 y T-305.5 no se ejecutaron por separado. T-305.6 se omitió porque requería condiciones de Development y operaciones POST que no correspondían al entorno QA disponible. Por tanto, no deben contabilizarse como aprobadas.

## 4.5 Pruebas de Identidad

**Tipo:** funcional de autenticación API.  
**Ambiente:** QA.

| Verificación | Resultado |
|---|---|
| Inicio de sesión con cuenta de auditor | Aprobada — HTTP 200 |
| Inicio de sesión con cuenta de administrador de banco | Aprobada — HTTP 200 |
| **Total** | **2 aprobadas** |

Los tokens se utilizaron únicamente durante las verificaciones y no se incluyen en este documento. Los resultados no implican que se hayan probado todas las políticas, roles, renovaciones, revocaciones o escenarios de sesión.

## 4.6 Pruebas E2E de backend/API

Los siguientes escenarios se documentan por separado de las suites automatizadas para evitar sumar dos veces los mismos resultados.

| Caso | Flujo | Resultado | Alcance y limitaciones |
|---|---|---|---|
| TC-E2E-01 | Publicación de campaña y generación del evento asociado | Aprobado para el recorrido ejecutado | La campaña sintética se preparó previamente en la base de datos. La preparación no valida la creación mediante API. Se verificó la publicación y el evento correspondiente. |
| TC-E2E-02 | Registro de intención anónima, registro de donación, persistencia y evento de salida | Aprobado para el recorrido ejecutado | Se verificaron respuestas HTTP 201, persistencia de donación y unidad, y evento `donation.completed` publicado. No se verificó el procesamiento por todos los consumidores posteriores. |
| TC-E2E-03 | Autenticación y autorización de consulta de donación | Aprobado para endpoint y roles probados | Sin token: HTTP 401; rol `operador`: HTTP 200; rol `auditor`: HTTP 403. No cubre todos los endpoints y roles. |

Los recorridos E2E se consideran aprobados únicamente para los pasos y dependencias realmente comprobados.

## 4.7 Pruebas de Donación adicionales

Se conserva el resultado de TC-020M, ejecutado el 08/10/2026 a través de Caddy/APISIX:

- Solicitud de creación de donación.
- Respuesta HTTP 201.
- Verificación de persistencia de la donación.
- Verificación de generación de tres unidades.

Este caso se mantiene como evidencia independiente y no se suma nuevamente al conteo de suites automatizadas ni a TC-E2E-02.

## 4.8 Pruebas de rendimiento

Las pruebas LT-01 a LT-04 se ejecutaron el 19/10/2026 con Gatling 3.16.0 desde la VM Tools contra QA, a través de Caddy y APISIX.

Criterios de aceptación: RNF-04 del SRS V5.1 y sección 35.3 del SAD V3.1.

| ID | Carga programada | Sesiones autenticadas | Consultas exitosas | p95 / p99 | Errores | Estado |
|---|---|---:|---:|---|---|---|
| LT-01 | 20 usuarios virtuales | 10 de 20 | 600 (100 %) | 14 ms / 16 ms | 10 HTTP 429 en autenticación | Parcial; concurrencia objetivo no demostrada |
| LT-02 | 50 usuarios durante 10 min | 20 de 50 | 11.313 (100 %) | 13 ms / 17 ms | 30 HTTP 429 en autenticación | Parcial; concurrencia efectiva no alcanzada |
| LT-03 | 50 usuarios durante 30 min | 50 de 90 intentos | 43.329 de 63.199 (68,56 %) | 13 ms / 17 ms | 19.870 HTTP 401 en inventario y 40 HTTP 429 en autenticación | No satisfactorio |
| LT-04 | 200 usuarios durante 5 min | 10 de 200 | 3.104 (100 %) | 12 ms / 14 ms | 190 HTTP 429 en autenticación | Parcial; capacidad objetivo no demostrada |

Interpretación:

- Las consultas de inventario admitidas registraron p95 de 12 a 14 ms, por debajo del umbral de 3 segundos.
- Los HTTP 429 corresponden al límite de inicio de sesión del Gateway (10 intentos por minuto por IP). Los usuarios virtuales compartieron la IP de la VM Tools, reduciendo la concurrencia efectiva.
- Los HTTP 401 de LT-03 son compatibles con expiración del token de acceso sin renovación durante la simulación; la causa exacta requiere confirmación mediante registros.
- Las pruebas no demuestran todavía la capacidad con 20, 50 o 200 sesiones simultáneas.

Se requiere repetir LT-01 a LT-04 con renovación de sesión y una preparación de autenticación que no reduzca la concurrencia objetivo. También se debe registrar CPU y memoria con Prometheus.

## 4.9 Pruebas E2E de Salud

Se ejecutaron tres pruebas automatizadas, utilizando un único trabajador (*worker*).

| Servicio | Prueba realizada  | Resultado |
| -------- | ----------------- | --------- |
| Donation | Endpoint de salud | Aprobada  |
| Campaign | Endpoint de salud | Aprobada  |
| Identity | Endpoint de salud | Aprobada  |

### Resumen de resultados

- **Pruebas ejecutadas:** 3
- **Pruebas aprobadas:** 3
- **Pruebas fallidas:** 0
- **Tasa de éxito:** 100 %
- **Tiempo total de ejecución:** 1,2 segundos
- **Workers utilizados:** 1

Salida obtenida en la terminal:

```text
Running 3 tests using 1 worker

✓ 1 services.api.spec.js:31:5
  › API - donation
  › responde correctamente en el endpoint de salud (53ms)

✓ 2 services.api.spec.js:31:5
  › API - campaign
  › responde correctamente en el endpoint de salud (35ms)

✓ 3 services.api.spec.js:31:5
  › API - identity
  › responde correctamente en el endpoint de salud (34ms)

3 passed (1.2s)
```
Los resultados muestran que los tres servicios evaluados respondieron satisfactoriamente a las solicitudes realizadas por Playwright.

Los tiempos individuales registrados fueron:

- Donation: 53 ms.
- Campaign: 35 ms.
- Identity: 34 ms.

El servicio Identity presentó el menor tiempo de respuesta, mientras que Donation registró el mayor.

No se presentaron errores durante la ejecución y todas las pruebas finalizaron correctamente.

## 4.10 Resumen de resultados por componente

| Componente / área | Resultado resumido | Estado |
|---|---|---|
| Donación — unitarias/componentes | 181 aprobadas, 0 fallidas | Aprobado |
| Identidad — unitarias/componentes | 20 aprobadas, 0 fallidas | Aprobado |
| Campañas — unitarias/componentes | 21 aprobadas, 3 fallidas por configuración de fixture | Parcial; reejecución pendiente |
| Donación — integración automatizada | 7 aprobadas, con dependencias simuladas declaradas | Aprobado para el alcance ejecutado |
| Institucional — API funcional | Verificaciones manuales de disponibilidad, autenticación, auditoría, territorios, instituciones e indicadores | Aprobado para los casos ejecutados; reconciliar total |
| Bases de datos | Permisos, restricciones, roles y auditoría | Verificaciones registradas como satisfactorias |
| Gateway | JWT rechazado en QA; escenario local simulado; YAML con advertencias no bloqueantes | Parcial |
| Identidad — login API | 2 aprobadas | Aprobado para las cuentas probadas |
| Campañas — E2E backend/API | Publicación y evento | Aprobado para el recorrido ejecutado |
| Donación — E2E backend/API | Intención, donación, persistencia y evento | Aprobado para el recorrido ejecutado |
| Rendimiento | Consultas rápidas para sesiones admitidas; concurrencia objetivo no demostrada | Parcial / no satisfactorio en LT-03 |
| E2E Salud | 3 aprobadas, 0 fallidas | Aprovado |

---

# 5. Cobertura y trazabilidad

## 5.1 Cobertura confirmada

La evidencia disponible respalda verificaciones seleccionadas de:

- Reglas y componentes del Servicio de Donación.
- Componentes del Servicio de Identidad.
- Componentes del Servicio de Campañas, con tres fallos de configuración pendientes de reejecución.
- Integración seleccionada de Donación con PostgreSQL y Kafka, con dependencias simuladas identificadas.
- Autenticación API de cuentas de prueba.
- Consultas y controles de acceso del Servicio Institucional.
- Permisos y restricciones de cuentas de servicio y auditoría en bases de datos.
- Rechazo de JWT firmado con llave de desarrollo en Gateway QA.
- Publicación de campaña y verificación del evento asociado.
- Registro de intención anónima y donación, con verificaciones de persistencia.
- Consulta de donación con los roles probados.
- Consulta de inventario bajo las sesiones admitidas en LT-01 a LT-04.

## 5.2 Cobertura no confirmada

No se puede afirmar que se hayan cubierto:

- Todos los requisitos RF, RNF y escenarios EC.
- Todos los escenarios heredados del TD V1.
- Todas las operaciones de las API.
- Todas las combinaciones de roles y permisos.
- Todas las validaciones negativas y de borde.
- La idempotencia en todos los escenarios de repetición y reintento.
- Todos los consumidores posteriores de eventos.
- El procesamiento integral de Notificaciones.
- La bandeja de salida de Campañas y el consumidor asociado a RF-25, pendientes de reejecución de las tres pruebas fallidas.
- La concurrencia objetivo de las pruebas de rendimiento.
- La renovación de sesión durante LT-03.
- Las operaciones de escritura de Institucional no ejecutadas.

## 5.3 Matriz inicial de trazabilidad

| Identificador | Funcionalidad | Tipo | Requisito / escenario | Estado |
|---|---|---|---|---|
| TC-E2E-01 | Publicación de campaña y evento | E2E backend/API | RF-08; EC-22 | Aprobado para el recorrido ejecutado |
| TC-E2E-02 | Intención anónima, donación y persistencia | E2E backend/API | RF-01, RF-03; EC-10, EC-17 | Aprobado para el recorrido ejecutado |
| TC-E2E-03 | Autenticación y autorización de consulta | E2E backend/API | RF-23, RNF-02; EC-01, EC-03 | Aprobado para endpoint y roles probados |
| TC-DON-JAVA | Reglas y componentes de Donación | Unitaria / componentes | RF-02, RF-03, RF-04, RF-05, RF-06, RF-11, RF-18 | Suite aprobada |
| TC-ID-DOTNET | Componentes de Identidad | Unitaria / componentes | RF-22, RF-23, RF-24 | Suite aprobada |
| TC-CAMP-DOTNET | Componentes de Campañas | Unitaria / componentes | RF-08, RF-25 | 21 aprobadas, 3 fallidas por configuración |
| TC-DON-INT | Integración Donación con PostgreSQL y Kafka | Integración | RF-03; EC-10 | 7 aprobadas, con simulaciones declaradas |
| TST-INST | Consultas, auditoría y control de acceso Institucional | Funcional API / seguridad | Políticas y operaciones Institucionales | Aprobado para casos ejecutados; total por reconciliar |
| TST-DB | Permisos, roles, restricciones e integridad | Seguridad / persistencia | RNF de seguridad y reglas de datos | Verificaciones registradas; matriz individual pendiente |
| TST-GW | Validación JWT, OIDC simulado y YAML | Seguridad / configuración | EC-01, EC-03 y configuración Gateway, según caso | Parcial |
| TST-ID-LOGIN | Inicio de sesión de auditor y admin_banco | Funcional API | RF-22 / autenticación | 2 aprobadas |
| LT-01 a LT-04 | Consulta de inventario bajo carga | Rendimiento | RF-07, RNF-04; EC-06, EC-15, EC-16 | Cumplimiento parcial; LT-03 no satisfactorio |

La matriz es inicial. La trazabilidad completa requiere relacionar cada caso con los identificadores oficiales y comprobar la cobertura de cada requisito. La aprobación de un caso no implica cobertura completa de un requisito.

---

# 6. Hallazgos, riesgos y acciones pendientes

## 6.1 Hallazgos

### H-01 — Suite de Campañas: configuración del fixture

Tres pruebas de `EventosPostgresPruebas` fallaron porque el fixture no encuentra `00_roles.sql` en la ruta relativa esperada. La causa identificada corresponde a la estructura de repositorios separados. Se requiere reejecutar la suite indicando la ruta correcta mediante `REDVITAL_MIGRACIONES_CAMPANAS`. No se ha confirmado un defecto funcional.

### H-02 — Filtro de campañas en estado borrador

La consulta sin sesión o con rol de donante devuelve únicamente campañas publicadas e ignora el filtro de estado. La revisión disponible indica que este comportamiento es intencional para esos perfiles; no se clasifica como defecto. Se debe documentar en el contrato de API.

### H-03 — Rendimiento y concurrencia

El límite de inicio de sesión del Gateway redujo las sesiones autenticadas en LT-01, LT-02 y LT-04. LT-03 registró respuestas HTTP 401 compatibles con expiración del token sin renovación. La concurrencia objetivo no ha quedado demostrada.

### H-04 — Cobertura de Institucional

Se probaron consultas y controles de acceso, pero no se ejecutaron operaciones POST o PUT. La cobertura debe interpretarse como parcial y limitada a los endpoints, parámetros y roles efectivamente utilizados.

### H-05 — Gateway: escenarios pendientes

Algunas pruebas del conjunto T-305 no se ejecutaron por requerimientos de entorno o porque implicaban operaciones que no se realizaron en QA. Los escenarios simulados localmente no deben presentarse como integraciones reales contra QA.

### H-06 — Trazabilidad documental

La matriz de trazabilidad todavía requiere asociar todos los casos con los RF, RNF y EC oficiales y con sus evidencias individuales.

## 6.2 Acciones pendientes

| ID | Acción | Responsable sugerido | Estado |
|---|---|---|---|
| P-01 | Reejecutar las tres pruebas de Campañas con la ruta correcta de migraciones. | Desarrollo / QA | Pendiente |
| P-02 | Documentar en el contrato el comportamiento del filtro `estado=borrador` según el perfil. | QA / Campañas | Pendiente |
| P-03 | Completar trazabilidad de casos con RF, RNF y EC oficiales. | QA / requisitos | Pendiente |
| P-04 | Verificar consumidores posteriores de eventos cuando el contrato lo requiera. | QA / equipos responsables | Pendiente |
| P-05 | Ampliar pruebas de idempotencia y reintentos. | QA | Pendiente |
| P-06 | Ampliar combinaciones de roles y endpoints. | QA | Pendiente |
| P-07 | Repetir LT-01 a LT-04 con renovación de sesión y concurrencia controlada. | Responsable de rendimiento / QA | Pendiente |
| P-08 | Asociar evidencias definitivas y referencias a cada caso. | QA | Pendiente |
| P-09 | Reconciliar los resultados heredados del TD V1. | QA | Pendiente |
| P-10 | Ejecutar e incorporar las pruebas unitarias e integración de Notificaciones. | QA / equipo de Notificaciones | Pendiente |
| P-11 | Reconciliar el total exacto de pruebas manuales de Institucional y la clasificación de sus casos. | QA | Pendiente |
| P-12 | Convertir las verificaciones de Bases de Datos y Gateway en matrices de casos individuales con identificador y resultado. | QA | Pendiente |

---

# 7. Evidencias y control documental

## 7.1 Evidencias requeridas

Se deben conservar, según el tipo de prueba:

- Reportes de suites automatizadas.
- Logs relevantes.
- Solicitudes y respuestas HTTP de pruebas API y E2E.
- Consultas SQL de solo lectura y resultados depurados.
- Identificadores de correlación no sensibles.
- Evidencias de estado de eventos cuando aplique.
- Configuración relevante del ambiente sin secretos.
- Informes individuales por componente.
- Informes de rendimiento y métricas de infraestructura.
- Relación entre cada evidencia, caso y requisito.

## 7.2 Organización de evidencias

Los informes individuales se mantienen separados del documento consolidado. La estructura propuesta es:

```text
Documentacion_RedVital/
└── Pruebas/
    ├── TD-PRUEBAS-FINAL.md
    ├── Informes/
    │   ├── TST-INSTITUCIONAL.md
    │   ├── TST-BASES-DE-DATOS.md
    │   ├── TST-GATEWAY.md
    │   ├── TST-IDENTIDAD.md
    │   ├── TST-DONACION.md
    │   └── TST-CAMPANAS.md
    ├── Resultados_Automatizados/
    │   ├── Pruebas_Unitarias/
    │   └── Pruebas_Integracion/
    ├── Evidencias_API/
    ├── Evidencias_SQL/
    └── Rendimiento/
```

La estructura debe ajustarse a la ubicación real del repositorio y de los documentos del proyecto.

## 7.3 Protección de información

No se deben incluir contraseñas, tokens de acceso, claves privadas, secretos ni identificadores personales innecesarios. Las respuestas y logs se deben depurar antes de incorporarlos al documento.

## 7.4 Incorporación de resultados posteriores

Para incorporar nuevos resultados se debe añadir una entrada en la sección 4 y actualizar la matriz de trazabilidad, cobertura, hallazgos, pendientes y conclusiones. Cada nueva ejecución debe registrar:

- Fecha.
- Servicio o componente.
- Tipo y alcance de prueba.
- Ambiente, rama y versión.
- Dependencias reales o simuladas.
- Cantidad de casos ejecutados, aprobados, fallidos, bloqueados y no ejecutados.
- Resultado esperado y obtenido.
- Evidencia asociada.
- Limitaciones y observaciones.

Los nuevos resultados se agregan sin sobrescribir los resultados históricos. Si una prueba se repite, se conserva el resultado anterior y se documenta la nueva ejecución como retest o nueva corrida.

---

# 8. Conclusiones

La consolidación de los documentos de Sprint 3 y Sprint 4 permite reunir la estrategia de pruebas, los casos planificados y los resultados disponibles de los servicios y componentes evaluados de RedVital.

Las suites automatizadas de servicios e integración consideradas en este documento suman 232 pruebas: 229 aprobadas y 3 fallidas por una causa de configuración del fixture de Campañas, pendiente de reejecución. Los resultados manuales y E2E de backend/API se presentan por separado para no mezclarlos ni duplicarlos con las suites automatizadas.

Las verificaciones de Institucional aportan evidencia sobre disponibilidad, autenticación, auditoría, consultas y control de acceso. Las pruebas de bases de datos revisan permisos y restricciones de integridad; las de Gateway comprueban controles seleccionados de autenticación y configuración. Los recorridos E2E documentados respaldan los flujos específicos de publicación de campañas y registro de donaciones.

Las pruebas de rendimiento muestran tiempos bajos de respuesta de inventario para las sesiones admitidas, pero no demuestran todavía la concurrencia objetivo. También permanecen pendientes la reejecución de pruebas de Campañas, la ampliación de cobertura de roles y endpoints, la ejecución de Notificaciones y la finalización de la trazabilidad.

**Estado general:** parcialmente ejecutado; documento en borrador vivo para revisión y actualización.

**Defectos funcionales confirmados:** no se establece ninguno de forma definitiva a partir de los hallazgos descritos. Los fallos y observaciones identificados conservan sus acciones de seguimiento.

---

# 9. Revisión y aprobación

| Actividad | Responsable | Fecha | Estado |
|---|---|---|---|
| Elaboración y consolidación | Tomás Silva | 05/11/2026 | Borrador |
| Revisión técnica | Pendiente | Pendiente | Pendiente |
| Revisión de cobertura y evidencias | Pendiente | Pendiente | Pendiente |
| Aprobación final | Pendiente | Pendiente | Pendiente |

El documento permanecerá en estado de borrador hasta que se revisen las evidencias, se reconcilien los conteos señalados, se actualicen los pendientes y se complete el proceso de aprobación correspondiente.
