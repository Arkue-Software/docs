# Documento de pruebas TD_V2 - Sprint 4

## Control del documento

| Campo | Información |
|---|---|
| Código del documento | TD-V2-4 |
| Versión | 2.0 |
| Estado | Borrador para revisión |
| Sprint o entrega | 4 |
| Responsable del documento | Tomás Silva |
| Revisado por | Pendiente |
| Aprobado por | Pendiente |
| Fecha de creación | Pendiente de confirmar |
| Última actualización | 21/10/2026 |
| Documento anterior | TD_V1 |
| Ambiente principal | Pruebas (QA) |

## Historial de cambios

| Versión | Fecha | Descripción del cambio | Autor |
|---|---|---|---|
| 2.0 | 18/10/2026 | Actualización de la estrategia de pruebas para Sprint 4, incorporación de resultados automatizados y E2E disponibles, revisión de riesgos, trazabilidad y planificación de actividades pendientes. | Tomás Silva |
| 2.1 | 21/10/2026 | Incorporación de los resultados consolidados de 40 pruebas E2E automatizadas de interfaz web con Playwright y actualización de los resúmenes cuantitativos, el alcance, la cobertura y las conclusiones. | Tomás Silva |

---

## 1. Introducción

### 1.1 Propósito

El propósito del Documento de Pruebas V2 (TD_V2) es definir la estrategia, el alcance, los criterios de ejecución, los datos, la trazabilidad y las actividades de verificación correspondientes al Sprint 4 del proyecto RedVital.

Este documento da continuidad al TD_V1 y considera las pruebas automatizadas y manuales que se han ejecutado, los resultados disponibles, las observaciones que requieren investigación y las actividades que aún deben realizarse.

El documento busca facilitar el seguimiento de la calidad del sistema y proporcionar información verificable para determinar si los componentes evaluados cumplen los comportamientos esperados.

### 1.2 Alcance

El alcance contempla las siguientes áreas:

- Servicio de Donación, desarrollado en Java.
- Servicio de Identidad, desarrollado en .NET.
- Servicio de Campañas, desarrollado en .NET.
- Integración entre los servicios y sus mecanismos de comunicación.
- Persistencia de datos en PostgreSQL.
- Autenticación y autorización de solicitudes.
- Registro de intenciones anónimas y donaciones.
- Publicación de eventos mediante el mecanismo de bandeja de salida (*outbox*) y Kafka.
- Publicación de campañas y disponibilidad de sus eventos.
- Pruebas de extremo a extremo (E2E) de backend/API.
- Pruebas E2E automatizadas de interfaz web.
- Validación de aspectos funcionales, de integración y de calidad del software.
- Planificación de pruebas de rendimiento, cuya ejecución corresponde a otro integrante del equipo.

Las pruebas de interfaz web incluyen verificaciones de formularios, validaciones de campos, navegación, acceso a páginas públicas y comportamiento de rutas protegidas sin sesión.

Las pruebas se concentran en los flujos y componentes que cuentan con evidencia de ejecución. La inclusión de un componente en el alcance no implica que todas sus funcionalidades hayan sido verificadas.

### 1.3 Exclusiones y limitaciones

Este documento no afirma que se haya validado exhaustivamente toda la plataforma RedVital.

Quedan fuera de los resultados confirmados las siguientes actividades, mientras no exista evidencia de su ejecución:

- Cobertura completa de todas las combinaciones de roles y endpoints.
- Validación exhaustiva de todos los escenarios negativos.
- Verificación de todos los consumidores posteriores de los eventos publicados.
- Pruebas de carga y estrés ejecutadas por el integrante responsable de rendimiento.
- Aprobación definitiva del documento y cierre de todos los pendientes.
- Confirmación de defectos funcionales que todavía no hayan sido reproducidos y aislados.
- Cobertura integral de todos los flujos de interfaz hasta su procesamiento completo en los servicios y su persistencia en la base de datos.
- Validación de todos los navegadores y dispositivos soportados.
- Verificación de todos los requisitos funcionales y no funcionales mediante una matriz de trazabilidad completa.

Las pruebas E2E de interfaz se ejecutaron en Chromium. La configuración utilizada permitió ignorar errores de certificados HTTPS durante las pruebas, por lo que sus resultados no validan por sí mismos la confianza del certificado desde la perspectiva de un navegador sin esa configuración.

### 1.4 Referencias

- TD_V1: documento de pruebas del sprint anterior.
- Documento de requisitos funcionales (RF), requisitos no funcionales (RNF) y especificaciones de casos de uso (EC) del proyecto RedVital.
- Documento de herramientas de prueba V3.0.
- Informes de ejecución de las pruebas automatizadas del Sprint 4.
- Informes individuales de los escenarios E2E de backend/API.
- Resultados consolidados de las pruebas E2E de interfaz web con Playwright.
- Especificaciones de las API y contratos de eventos de los servicios de RedVital.

**Nota:** las versiones definitivas de los requisitos y las referencias documentales deben asociarse cuando se confirme su ubicación y versión oficial.

---

## 2. Estrategia de pruebas

### 2.1 Enfoque general

La estrategia combina pruebas automatizadas, pruebas de integración y verificaciones manuales y automatizadas de extremo a extremo.

Cada tipo de prueba tiene un objetivo distinto:

| Tipo de prueba | Objetivo | Evidencia esperada |
|---|---|---|
| Unitaria y de componentes | Verificar la lógica de los componentes de manera aislada. | Reportes de ejecución de las suites automatizadas. |
| Integración | Comprobar la interacción entre componentes y dependencias. | Resultados de pruebas de integración, logs y respuestas. |
| API | Validar contratos, códigos HTTP, validaciones y permisos. | Solicitudes, respuestas y correlaciones. |
| Persistencia | Comprobar que los datos esperados se almacenan y relacionan correctamente. | Consultas SQL de solo lectura. |
| E2E de backend/API | Verificar recorridos funcionales que atraviesan operaciones de los servicios y sus dependencias. | Informes individuales por escenario, respuestas y evidencias asociadas. |
| E2E de interfaz web | Verificar el comportamiento observable de la aplicación web, incluyendo navegación, formularios, validaciones y acceso a rutas. | Reportes automatizados, resultados por caso y evidencias de ejecución. |
| Seguridad funcional | Comprobar autenticación, autorización y restricciones de acceso en los escenarios seleccionados. | Respuestas HTTP y registros de correlación. |
| Rendimiento | Evaluar el comportamiento bajo las cargas definidas para el proyecto. | Reporte del integrante responsable, métricas y condiciones de ejecución. |

### 2.2 Herramientas

| Herramienta o tecnología | Uso previsto |
|---|---|
| JUnit 5 y Mockito | Pruebas unitarias y de componentes del Servicio de Donación. |
| xUnit | Pruebas automatizadas de Identidad, Campañas y componentes .NET correspondientes. |
| Vitest | Pruebas de la aplicación web. |
| Playwright | Automatización de pruebas E2E de interfaz web. |
| Chromium | Navegador utilizado en la ejecución de las pruebas E2E de interfaz. |
| PostgreSQL y `psql` | Verificación de persistencia y relaciones entre registros. |
| Docker y Docker Compose | Ejecución de servicios y dependencias del ambiente QA. |
| Kafka | Verificación de la disponibilidad y publicación de eventos cuando corresponda al escenario. |
| Testcontainers | Preparación de dependencias aisladas para determinadas pruebas de integración. |
| GitHub Actions | Ejecución automatizada y validación de los controles configurados en los flujos de integración. |
| OpenAPI 3.1 | Consulta y verificación de contratos de API. |
| ESLint y herramientas de análisis estático | Revisión de calidad del código y reglas configuradas. |
| Herramientas de rendimiento seleccionadas por el equipo | Ejecución de los escenarios de carga definidos en este documento. |

La existencia de una herramienta en esta lista no constituye evidencia de que todas las pruebas previstas con ella hayan sido ejecutadas.

### 2.3 Niveles de prueba

**Nivel 1. Pruebas unitarias y de componentes**

Se ejecutan sobre las reglas y componentes de los servicios. Permiten detectar errores localizados sin requerir necesariamente el recorrido completo de una operación.

**Nivel 2. Pruebas de integración**

Se enfocan en las interacciones entre servicios, bases de datos y mecanismos de mensajería. Los resultados deben indicar qué dependencias fueron reales y cuáles fueron simuladas.

**Nivel 3. Pruebas de API**

Comprueban los contratos expuestos por los servicios, incluidos los códigos HTTP, las respuestas de error, los datos retornados y los requisitos de autenticación.

**Nivel 4. Pruebas de extremo a extremo**

Validan recorridos funcionales concretos. En RedVital se distinguen dos grupos:

- **E2E de backend/API:** verifican recorridos que involucran operaciones de los servicios, sus respuestas y, cuando corresponde, la persistencia de datos o la publicación de eventos.
- **E2E de interfaz web:** verifican el comportamiento observable desde la aplicación web, incluyendo acceso a páginas, navegación, validaciones de formularios y restricciones de acceso a rutas.

El alcance de cada caso debe describir qué componentes atraviesa realmente. Una prueba de interfaz que no completa una operación contra los servicios no demuestra la integración completa de dicha operación.

**Nivel 5. Pruebas no funcionales**

Incluyen las verificaciones de seguridad, accesibilidad, rendimiento y otros atributos de calidad definidos para el proyecto. Su estado debe registrarse por separado del de las pruebas funcionales.

### 2.4 Ambientes de prueba

| Campo | Definición |
|---|---|
| Ambiente principal | Pruebas (QA). |
| Infraestructura | Servicios y dependencias desplegados para el ambiente QA. |
| Bases de datos | PostgreSQL, con bases independientes por servicio según la configuración del ambiente. |
| Mensajería | Kafka para los flujos que utilizan eventos. |
| Datos | Datos sintéticos de QA y registros preparados para los escenarios específicos. |
| Acceso | Cuentas de prueba y permisos asignados según el escenario. |
| Interfaz web | Aplicación disponible en `https://10.43.98.220`. |
| Navegador E2E de interfaz | Chromium. |
| Evidencias | Reportes automatizados, respuestas HTTP, consultas SQL y logs pertinentes. |

Las pruebas deben registrar la configuración realmente utilizada. Cuando una dependencia se simule, se debe indicar expresamente, ya que el resultado no demuestra el comportamiento de una integración real que haya sido sustituida por un *mock*.

### 2.5 Datos de prueba

Se utilizarán datos sintéticos y controlados para evitar la exposición de información personal real.

Los datos pueden incluir:

- Cuentas sintéticas con roles específicos.
- Campañas creadas para QA.
- Intenciones anónimas de donación.
- Donaciones y unidades asociadas.
- Identificadores de correlación.
- Eventos de salida y sus estados de publicación.
- Datos inválidos para pruebas negativas cuando se planifiquen.
- Datos de formulario preparados para verificar validaciones de la interfaz.

Cada escenario debe documentar los identificadores relevantes y distinguir entre datos creados mediante API, datos insertados como preparación en la base de datos y datos existentes antes de la prueba.

En las pruebas de interfaz se deben utilizar datos de entrada controlados que permitan comprobar los resultados esperados sin crear registros reales innecesarios.

No se deben incorporar contraseñas, tokens de acceso, claves privadas ni otros secretos en los informes.

---

## 3. Criterios de entrada y salida

### 3.1 Criterios de entrada

Antes de ejecutar una prueba deben cumplirse las condiciones aplicables al escenario:

- El servicio y las dependencias necesarias están disponibles.
- El ambiente corresponde a QA.
- La versión o rama probada está identificada.
- Los datos de prueba están preparados y sus identificadores registrados.
- Las cuentas y permisos requeridos están disponibles.
- Las precondiciones funcionales se han verificado.
- Los pasos y resultados esperados están definidos.
- Las herramientas necesarias están disponibles.
- Se han previsto mecanismos para conservar evidencias sin exponer secretos.

Para las pruebas automatizadas también debe existir una configuración reproducible que permita identificar el resultado de cada suite.

Para las pruebas E2E de interfaz se debe comprobar la disponibilidad de la aplicación, del navegador y del entorno de ejecución de Playwright.

### 3.2 Criterios de salida

Un escenario se considera aprobado cuando:

- Se completaron los pasos previstos.
- Los resultados observados cumplen los resultados esperados.
- Las evidencias respaldan las verificaciones principales.
- No se identificaron fallos que contradigan el objetivo del escenario.
- Las limitaciones y las dependencias simuladas están documentadas.

Una suite automatizada se considera aprobada cuando termina correctamente y no presenta pruebas fallidas, salvo que exista una política formal distinta documentada para su evaluación.

En las pruebas E2E de interfaz, la aprobación de un caso confirma únicamente los comportamientos definidos y comprobados por ese caso. No demuestra que todos los componentes internos, servicios, bases de datos o procesos posteriores funcionen correctamente.

El cierre global del Sprint 4 requiere revisar los resultados de las suites, los informes E2E, los defectos abiertos, las pruebas pendientes y las evidencias correspondientes. La aprobación de un escenario individual no implica la aprobación total del sistema.

---

## 4. Gestión de defectos y observaciones

### 4.1 Registro

Los defectos deben documentarse con información suficiente para reproducirlos y evaluar su impacto.

Cada registro debe contener, cuando aplique:

- Identificador del defecto.
- Fecha de detección.
- Servicio o componente afectado.
- Caso de prueba relacionado.
- Ambiente y versión probada.
- Precondiciones y datos utilizados.
- Pasos para reproducir el problema.
- Resultado esperado.
- Resultado obtenido.
- Evidencias y correlaciones.
- Severidad y prioridad.
- Responsable de análisis.
- Estado y resultado de la retest.

### 4.2 Estados propuestos

| Estado | Descripción |
|---|---|
| Nuevo | Se identificó un posible defecto y está pendiente de análisis. |
| En análisis | Se está determinando la causa y el alcance. |
| Confirmado | Se reprodujo el problema y se determinó que incumple el comportamiento esperado. |
| En corrección | El equipo responsable trabaja en la solución. |
| Corregido | Se informa que la solución está implementada y está pendiente de retest. |
| En retest | QA verifica nuevamente el escenario. |
| Cerrado | La corrección fue verificada y existe evidencia suficiente. |
| No reproducible / No es defecto | El análisis no confirmó un defecto funcional; se debe conservar la justificación. |

### 4.3 Clasificación de los hallazgos

Los problemas detectados deben diferenciarse según su naturaleza:

- **Defecto funcional:** el comportamiento contradice un requisito o contrato confirmado.
- **Problema de configuración o entorno:** una prueba no puede ejecutarse correctamente por una dependencia, ruta, secreto o configuración incorrecta.
- **Observación pendiente de análisis:** el comportamiento requiere investigación antes de concluir que existe un defecto.
- **Limitación de cobertura:** una funcionalidad no fue probada o una verificación quedó fuera del alcance.

Una prueba fallida no debe clasificarse automáticamente como defecto funcional. Se debe investigar si la causa corresponde al producto, a la infraestructura, a los datos de prueba o a la propia configuración de la prueba.

### 4.4 Hallazgos que requieren seguimiento

**Pruebas de Campañas**

Se registraron tres fallos en la suite automatizada de Campañas relacionados con la preparación del *fixture* de Testcontainers y la ausencia del archivo:

`/home/estudiante/Downloads/RedVital/BDs_RedVital/databases/campanas/00_roles.sql`

El resultado sugiere un problema de preparación o configuración de la prueba. No se considera confirmado un defecto funcional del servicio a partir de este resultado. Se debe revisar la ruta, el montaje de archivos y la reproducibilidad del *fixture* antes de clasificarlo definitivamente.

**Consulta de campañas en estado borrador**

Se observó una respuesta HTTP `200` para la consulta de campañas con el filtro `estado=borrador`, cuyo contenido aparentemente coincidía con campañas publicadas. Este comportamiento requiere reproducirse y contrastarse con el contrato de la API y los datos existentes antes de registrarlo como defecto funcional confirmado.

**Pruebas E2E de interfaz web**

En la ejecución consolidada de los casos TST-UI-01 a TST-UI-40 no se registraron pruebas fallidas. Este resultado no descarta defectos en funcionalidades que no estén cubiertas por esos casos ni sustituye la revisión de los servicios y sus dependencias.

---

## 5. Trazabilidad de requisitos y escenarios

### 5.1 Criterio de trazabilidad

La trazabilidad relaciona los requisitos y especificaciones del sistema con los casos de prueba, sus resultados y sus evidencias.

Cada caso debe vincularse a un identificador oficial de requisito funcional (RF), requisito no funcional (RNF) o especificación de caso de uso (EC) cuando dicha relación pueda verificarse.

No se deben inventar identificadores ni asumir que un caso cubre un requisito completo solo porque evalúa una funcionalidad relacionada.

### 5.2 Matriz de trazabilidad

| Identificador de prueba | Funcionalidad evaluada | Tipo de prueba | Requisito asociado | Estado |
|---|---|---|---|---|
| TC-E2E-01 | Publicación de campaña y generación del evento correspondiente | E2E de backend/API | RF/EC por asociar con la versión oficial | Aprobado para el recorrido ejecutado |
| TC-E2E-02 | Registro de intención anónima, registro de donación y persistencia | E2E de backend/API | RF/EC por asociar con la versión oficial | Aprobado para el recorrido ejecutado |
| TC-E2E-03 | Autenticación y autorización en la consulta de una donación | E2E de backend/API | RF/RNF/EC de control de acceso por asociar | Aprobado para el endpoint y los roles probados |
| TC-DON-JAVA | Reglas y componentes del Servicio de Donación | Unitaria / componentes | RF y EC por mapear a los casos individuales | Ejecutado; suite aprobada |
| TC-ID-DOTNET | Componentes del Servicio de Identidad | Unitaria / componentes | RF/RNF/EC por mapear a los casos individuales | Ejecutado; suite aprobada |
| TC-CAMP-DOTNET | Componentes del Servicio de Campañas | Unitaria / componentes | RF/EC por mapear a los casos individuales | Ejecutado con fallos de preparación pendientes |
| TC-DON-INT | Integración del Servicio de Donación con PostgreSQL y Kafka | Integración | RF/EC por mapear | Ejecutado; suite aprobada con las dependencias documentadas |
| TST-UI-01 a TST-UI-40 | Formularios, validaciones, navegación, rutas públicas y protegidas de la aplicación web | E2E de interfaz web con Playwright | RF/EC por asociar con la versión oficial | 40 aprobadas; trazabilidad individual pendiente |
| TC-REN-01 a TC-REN-04 | Escenarios de rendimiento definidos para Sprint 4 | No funcional / rendimiento | RNF oficial por asociar | Pendiente de ejecución por el integrante responsable |

**Importante:** la matriz anterior es una trazabilidad inicial por funcionalidad. Para considerarla completa debe asociarse cada prueba con los identificadores oficiales y comprobarse la cobertura de cada requisito. La aprobación de un caso no implica que un requisito completo esté cubierto.

### 5.3 Escenarios heredados del TD_V1

El TD_V1 contemplaba 17 escenarios de Sprint 3 y seguimiento de EC-08, EC-22, EC-23 y EC-36.

La cobertura heredada debe revisarse contra los resultados y evidencias disponibles. La lista de escenarios del TD_V1 no constituye por sí misma evidencia de ejecución en Sprint 4.

| Elemento heredado | Acción para TD_V2 |
|---|---|
| 17 escenarios de Sprint 3 | Conservar los identificadores originales y actualizar el estado según las evidencias reales. |
| EC-08 | Verificar la relación con casos ejecutados y documentar el estado. |
| EC-22 | Verificar la relación con casos ejecutados y documentar el estado. |
| EC-23 | Verificar la relación con casos ejecutados y documentar el estado. |
| EC-36 | Verificar la relación con casos ejecutados y documentar el estado. |

No se asigna un estado de aprobado a estos elementos sin comprobar sus resultados. Su inclusión en esta sección tiene como objetivo mantener la continuidad documental entre versiones.

---

## 6. Plan de pruebas de Sprint 4

### 6.1 Pruebas automatizadas

Las siguientes ejecuciones cuentan con resultados registrados.

| Suite o ejecución | Resultado | Observaciones |
|---|---|---|
| Servicio de Donación, JUnit 5 / Maven | 181 aprobadas, 0 fallidas, 0 errores y 0 omitidas | Ejecución de `MotorTransicionesTest`, `PoliticasTest` y `ArquitecturaTest`. |
| Servicio de Identidad, .NET / xUnit | 20 aprobadas, 0 fallidas y 0 omitidas | Suite aprobada en la ejecución registrada. |
| Servicio de Campañas, .NET / xUnit | 21 aprobadas y 3 fallidas, de 24 | Los fallos se relacionan con la preparación del *fixture* y el archivo SQL faltante; la causa funcional no está confirmada. |
| Integración del Servicio de Donación | 7 aprobadas | PostgreSQL y Kafka mediante Testcontainers; Identidad/JWT se simuló y el evento de campaña se publicó directamente en Kafka. |
| Interfaz web, Playwright / Chromium | 40 aprobadas, 0 fallidas y 0 omitidas | Ejecución consolidada de TST-UI-01 a TST-UI-40. Duración: 26,8 segundos. |

El resultado de integración del Servicio de Donación no equivale a una verificación completa de todos los servicios reales de la plataforma. La sustitución de Identidad/JWT y la publicación directa del evento de campaña deben tenerse en cuenta al interpretar el alcance.

Las pruebas de interfaz se ejecutaron contra `https://10.43.98.220`, en el ambiente QA, utilizando Playwright 1.64.1, Node.js 22.23.0 y Chromium.

La configuración de ejecución permitió ignorar errores de certificados HTTPS dentro de las pruebas. Esta condición debe tenerse en cuenta al interpretar los resultados, pues no valida la confianza del certificado desde la perspectiva de un usuario o navegador sin esa configuración.

Los casos se limitaron a las interacciones definidas en cada prueba. No se finalizaron operaciones que crearan registros reales mediante formularios de la interfaz. Por lo tanto, los resultados de estas 40 pruebas no deben utilizarse como evidencia adicional de persistencia, procesamiento de eventos o funcionamiento integral del backend.

### 6.2 Resumen cuantitativo de las ejecuciones automatizadas

Con base en las cifras registradas en la sección 6.1:

| Métrica | Resultado |
|---|---:|
| Total de pruebas registradas | 272 |
| Pruebas aprobadas | 269 |
| Pruebas fallidas | 3 |
| Pruebas omitidas | 0 |
| Porcentaje de aprobación | 98,90 % |
| Porcentaje de fallo | 1,10 % |

Los porcentajes se calculan de la siguiente manera:

- Porcentaje de aprobación = `269 / 272 × 100 = 98,90 %`.
- Porcentaje de fallo = `3 / 272 × 100 = 1,10 %`.

Estas cifras describen las ejecuciones automatizadas enumeradas en este documento. No deben interpretarse como cobertura del código, cobertura completa de requisitos o porcentaje de funcionalidades validadas.

Los tres fallos corresponden a la suite de Campañas y continúan pendientes de análisis; no se han confirmado como defectos funcionales.

Los escenarios E2E manuales de backend/API no se incluyen en este conteo.

### 6.3 Pruebas E2E de backend/API

Los escenarios E2E de backend/API se documentan de forma individual para conservar sus datos, evidencias y conclusiones específicas.

| Caso | Objetivo | Resultado documentado | Alcance de la conclusión |
|---|---|---|---|
| TC-E2E-01 | Publicar una campaña y comprobar la generación del evento asociado. | Aprobado para el recorrido ejecutado. | La campaña sintética se preparó previamente directamente en la base de datos de Campañas. La preparación no valida la creación de la campaña mediante API. La publicación y las verificaciones posteriores sí se realizaron sobre el flujo evaluado. |
| TC-E2E-02 | Registrar una intención anónima y una donación, y verificar su persistencia y el evento de salida. | Aprobado para el recorrido ejecutado. | Se verificaron respuestas HTTP `201 Created`, persistencia de donación y unidad, y evento `donation.completed` en estado `publicado`. No se verificó de forma independiente el procesamiento por todos los consumidores posteriores. |
| TC-E2E-03 | Verificar la autenticación y autorización de una consulta de donación. | Aprobado para el endpoint y los roles probados. | Sin token: HTTP `401`; rol `operador`: HTTP `200`; rol `auditor`: HTTP `403`. No demuestra que todos los endpoints y roles estén correctamente cubiertos. |

Los informes individuales deben conservarse por separado del TD_V2. Este documento resume sus resultados y no sustituye las evidencias detalladas de cada ejecución.

### 6.3.1 Pruebas E2E de interfaz web

Se ejecutaron 40 casos automatizados de interfaz para comprobar el comportamiento observable de la aplicación web RedVital. La ejecución consolidada finalizó sin fallos ni casos omitidos.

#### Resumen de casos ejecutados

| Grupo de casos | Casos | Verificaciones principales | Resultado |
|---|---|---|---|
| Formularios y validaciones iniciales | TST-UI-01 a TST-UI-08 | Presentación de formularios, campos requeridos, formatos y validaciones iniciales. | 8 aprobadas |
| Validaciones adicionales y navegación | TST-UI-09 a TST-UI-12 | Confirmación de contraseña, datos de entrada y navegación entre pasos. | 4 aprobadas |
| Navegación y acceso | TST-UI-13 a TST-UI-16 | Redirección desde rutas protegidas y navegación entre páginas. | 4 aprobadas |
| Validaciones y recuperación | TST-UI-17 a TST-UI-24 | Longitud de documentos, caracteres no permitidos, fechas, corrección de errores y validación de códigos. | 8 aprobadas |
| Acceso a rutas protegidas | TST-UI-25 a TST-UI-33 | Comportamiento de nueve rutas al intentar acceder sin sesión. | 9 aprobadas |
| Rutas públicas y acceso directo | TST-UI-34 a TST-UI-40 | Página de inicio, rutas inexistentes, formularios accesibles y estados iniciales de las páginas. | 7 aprobadas |
| **Total** | **40 casos** | **Validaciones y comportamiento observable de la interfaz web.** | **40 aprobadas** |

#### Detalle funcional de los casos

| Caso | Comportamiento verificado |
|---|---|
| TST-UI-01 | Visualización del formulario de inicio de sesión, encabezado y enlaces disponibles. |
| TST-UI-02 | Impedimento de continuar con una donación anónima antes de seleccionar el grupo sanguíneo o la opción de desconocimiento. |
| TST-UI-03 | Continuación del flujo de donación anónima al seleccionar la opción de grupo sanguíneo desconocido. |
| TST-UI-04 | Comportamiento del campo de municipio opcional en el flujo de donación anónima, sin finalizar la operación. |
| TST-UI-05 | Validación de longitud mínima del código en la consulta de donaciones. |
| TST-UI-06 | Validación de longitud del documento de identificación. |
| TST-UI-07 | Mensaje de validación para un formato de correo electrónico inválido. |
| TST-UI-08 | Validación de requisitos de contraseña. |
| TST-UI-09 | Validación cuando la confirmación de contraseña no coincide. |
| TST-UI-10 | Rechazo de documentos que contienen espacios. |
| TST-UI-11 | Validación de campos requeridos en el registro inicial. |
| TST-UI-12 | Navegación hacia el paso anterior en el flujo de donación anónima. |
| TST-UI-13 | Redirección al inicio de sesión al intentar acceder a una ruta protegida sin sesión. |
| TST-UI-14 | Navegación desde el inicio de sesión hacia el registro. |
| TST-UI-15 | Navegación desde el inicio de sesión hacia la donación anónima. |
| TST-UI-16 | Navegación hacia la página de inicio mediante la opción correspondiente. |
| TST-UI-17 | Límite de longitud máxima del campo de documento de identificación. |
| TST-UI-18 | Rechazo de documentos que contienen signos no permitidos. |
| TST-UI-19 | Impedimento de continuar cuando la fecha de nacimiento introducida está en el futuro. |
| TST-UI-20 | Aceptación de un formato válido de correo electrónico. |
| TST-UI-21 | Eliminación del mensaje de error de contraseña al introducir una contraseña válida. |
| TST-UI-22 | Eliminación del mensaje de discrepancia cuando se corrige la confirmación de contraseña. |
| TST-UI-23 | Validación de la longitud mínima del código sin contar los espacios. |
| TST-UI-24 | Permanencia del botón de consulta deshabilitado cuando el código está vacío. |
| TST-UI-25 | Redirección al inicio de sesión desde `/donantes/historial` sin sesión. |
| TST-UI-26 | Redirección al inicio de sesión desde `/donantes/reconocimientos` sin sesión. |
| TST-UI-27 | Redirección al inicio de sesión desde `/donantes/consulta` sin sesión. |
| TST-UI-28 | Redirección al inicio de sesión desde `/ciclovida/registro-donacion` sin sesión. |
| TST-UI-29 | Redirección al inicio de sesión desde `/ciclovida/unidades` sin sesión. |
| TST-UI-30 | Redirección al inicio de sesión desde `/inventario` sin sesión. |
| TST-UI-31 | Redirección al inicio de sesión desde `/inventario/alertas` sin sesión. |
| TST-UI-32 | Redirección al inicio de sesión desde `/campanias` sin sesión. |
| TST-UI-33 | Redirección al inicio de sesión desde `/sin-modulos` sin sesión. |
| TST-UI-34 | Acceso a la página de inicio. |
| TST-UI-35 | Presentación de la interfaz de ruta no encontrada para una ruta inexistente. |
| TST-UI-36 | Acceso directo a la página de inicio de sesión. |
| TST-UI-37 | Acceso directo a la página de registro. |
| TST-UI-38 | Acceso directo a la página de donación anónima. |
| TST-UI-39 | Acceso a la página de consulta de donaciones y botón deshabilitado cuando el código está vacío. |
| TST-UI-40 | Impedimento de continuar con el registro inicial de cuenta cuando los datos están vacíos. |

#### Datos de ejecución

- Ambiente: Pruebas (QA).
- Aplicación: `https://10.43.98.220`.
- Herramienta: Playwright 1.64.1.
- Runtime: Node.js 22.23.0.
- Navegador: Chromium.
- Resultado consolidado: 40 aprobadas, 0 fallidas y 0 omitidas.
- Duración registrada: 26,8 segundos.

#### Alcance de las pruebas

Las pruebas verifican los comportamientos definidos en los casos automatizados, incluyendo validaciones de campos, mensajes de error, navegación, disponibilidad de páginas y redirecciones al intentar acceder sin sesión a rutas protegidas.

Los casos incluyen la verificación de formularios de registro y donación, pero no finalizaron operaciones que crearan registros reales mediante la interfaz.

#### Limitaciones

Los resultados no permiten concluir que todos los roles, endpoints o permisos del backend estén correctamente protegidos. Tampoco demuestran que las operaciones de creación de cuentas, donaciones o campañas se completen correctamente en toda la plataforma.

La ejecución se realizó en Chromium y no constituye evidencia de compatibilidad exhaustiva con todos los navegadores. La configuración utilizada para ignorar errores de certificados HTTPS también limita las conclusiones que pueden extraerse sobre la validación de certificados.

Por lo tanto, estos resultados representan la aprobación de los 40 casos de interfaz ejecutados, no una validación integral de la plataforma ni un sustituto de las pruebas de API, integración y persistencia.

### 6.4 Alcance de las pruebas de integración

La integración del Servicio de Donación se ejecutó con PostgreSQL y Kafka mediante Testcontainers. En la ejecución registrada, la autenticación de Identidad/JWT fue simulada y el evento de campaña se publicó directamente en Kafka.

Por lo tanto, el resultado respalda el comportamiento de la integración evaluada, pero no demuestra por sí solo el funcionamiento completo de la cadena de autenticación real ni de todos los productores y consumidores de eventos.

### 6.5 Pruebas de rendimiento

Las pruebas de rendimiento forman parte del alcance general del plan. Su ejecución está asignada a otro integrante del equipo.

Los siguientes escenarios se conservan como planificación y no deben marcarse como ejecutados hasta recibir el reporte correspondiente.

| ID | Escenario previsto | Carga definida | Estado |
|---|---|---|---|
| LT-01 | Evaluación de rendimiento del inventario | 20 usuarios concurrentes | Pendiente de ejecución por el responsable |
| LT-02 | Carga sostenida según el escenario definido | 50 usuarios durante 10 minutos | Pendiente de ejecución por el responsable |
| LT-03 | Carga sostenida según el escenario definido | 50 usuarios durante 30 minutos | Pendiente de ejecución por el responsable |
| LT-04 | Evaluación de carga elevada | Hasta 200 usuarios durante 5 minutos | Pendiente de ejecución por el responsable |

Antes de emitir conclusiones de rendimiento, se deben registrar la herramienta utilizada, los endpoints y operaciones evaluados, el perfil de carga, las condiciones del ambiente, la duración, los errores, los tiempos de respuesta y los criterios de aceptación establecidos por los requisitos no funcionales.

No se asignan umbrales de latencia o disponibilidad sin una referencia oficial que los respalde.

---

## 7. Cobertura y estado general

### 7.1 Cobertura confirmada

La evidencia disponible respalda las siguientes verificaciones:

- Reglas y componentes seleccionados del Servicio de Donación.
- Componentes seleccionados del Servicio de Identidad.
- Componentes seleccionados del Servicio de Campañas, con tres fallos pendientes de análisis.
- Integración seleccionada del Servicio de Donación con PostgreSQL y Kafka, considerando las dependencias simuladas.
- Publicación de una campaña sintética y verificación del evento correspondiente.
- Registro de una intención anónima y una donación, con comprobaciones de persistencia.
- Control de acceso a una consulta de donación para los tres casos de autenticación y autorización ejecutados.
- Comportamiento de formularios y validaciones de entrada seleccionadas en la interfaz web.
- Navegación entre páginas y pasos de los formularios incluidos en los casos.
- Acceso directo a rutas públicas y comportamiento de rutas inexistentes.
- Redirección al intentar acceder sin sesión a nueve rutas protegidas.
- Ejecución automatizada de 40 casos de interfaz con Playwright en Chromium, todos aprobados en la ejecución consolidada.

### 7.2 Cobertura no confirmada

No se puede afirmar todavía que se hayan cubierto:

- Todos los requisitos RF, RNF y EC.
- Todos los escenarios heredados del TD_V1.
- Todas las operaciones de las API.
- Todas las combinaciones de roles y permisos.
- Todos los escenarios negativos de validación.
- La idempotencia en condiciones de repetición y reintento.
- El procesamiento de todos los eventos por sus consumidores posteriores.
- La finalización de todas las operaciones desde la interfaz hasta su persistencia en la base de datos.
- La compatibilidad de todos los flujos de interfaz en otros navegadores.
- La protección integral de todos los endpoints y combinaciones de roles.
- La totalidad de las pruebas de rendimiento planificadas.

### 7.3 Estado de salida del Sprint 4

El estado general del plan se considera **parcialmente ejecutado**.

Existen resultados positivos en las suites automatizadas y en los escenarios E2E documentados. Las 40 pruebas E2E de interfaz finalizaron con resultado satisfactorio para los casos ejecutados.

Sin embargo, los fallos de la suite de Campañas, las observaciones pendientes de análisis, las verificaciones no ejecutadas y la ausencia de una trazabilidad oficial completa impiden declarar una cobertura integral del sistema.

La decisión de cierre del sprint debe considerar los criterios de salida definidos, la clasificación de los hallazgos y las evidencias definitivas.

---

## 8. Riesgos y acciones pendientes

| ID | Riesgo o pendiente | Impacto | Acción requerida | Responsable sugerido | Estado |
|---|---|---|---|---|---|
| P-01 | Tres pruebas de Campañas fallidas por la preparación del *fixture*. | Medio | Revisar la ruta del archivo SQL y la configuración de la prueba; volver a ejecutar la suite cuando se haya verificado la preparación. | Desarrollo / QA | Pendiente |
| P-02 | Comportamiento del filtro `estado=borrador` por confirmar. | Medio | Reproducir la consulta y contrastar la respuesta con el contrato de API y los datos existentes. | QA / equipo de Campañas | Pendiente de análisis |
| P-03 | Trazabilidad oficial incompleta entre requisitos y pruebas. | Medio | Asociar los casos con los identificadores oficiales de RF, RNF y EC. | QA / responsable de requisitos | Pendiente |
| P-04 | Consumo posterior de algunos eventos no comprobado. | Medio | Revisar los logs y la persistencia de los consumidores correspondientes cuando el contrato lo requiera. | QA / equipo técnico | Pendiente |
| P-05 | Comportamiento de idempotencia no verificado en todos los escenarios. | Medio | Ejecutar casos de repetición de solicitudes y confirmar el comportamiento esperado. | QA | Pendiente |
| P-06 | Combinaciones de roles y endpoints limitadas. | Medio | Ampliar los casos de autorización y las pruebas negativas. | QA | Pendiente |
| P-07 | Pruebas de rendimiento sin resultados incorporados. | Medio | Recibir y revisar el informe del integrante responsable. | Responsable de rendimiento / QA | Pendiente |
| P-08 | Evidencias y referencias documentales definitivas por asociar. | Bajo | Consolidar los enlaces o ubicaciones de los informes y resultados de ejecución. | QA | Pendiente |
| P-09 | Resultados heredados del TD_V1 por reconciliar. | Medio | Actualizar la matriz de cobertura con los resultados verificables de los 17 escenarios y las EC correspondientes. | QA | Pendiente |
| P-10 | Los 40 casos de interfaz se ejecutaron en Chromium y no cubren por sí solos los flujos completos de backend ni todos los navegadores. | Medio | Mantener diferenciadas las evidencias de interfaz, API e integración; ampliar la cobertura solo si lo requieren los requisitos y el plan de pruebas. | QA | Pendiente de evaluación |

---

## 9. Evidencias y control documental

### 9.1 Evidencias requeridas

Para mantener la reproducibilidad, se deben conservar:

- Reportes de las suites automatizadas.
- Logs relevantes de las ejecuciones.
- Solicitudes y respuestas HTTP de los escenarios E2E de backend/API.
- Consultas SQL de solo lectura y sus resultados.
- Identificadores de correlación.
- Evidencias del estado de los eventos cuando aplique.
- Configuración relevante del ambiente, sin secretos.
- Reporte consolidado de Playwright con los resultados de TST-UI-01 a TST-UI-40.
- Evidencias individuales de los casos de interfaz cuando estén disponibles.
- Informe de rendimiento elaborado por el integrante responsable.
- Referencias a los casos y requisitos asociados.

### 9.2 Organización de las evidencias

Los informes individuales deben mantenerse separados del TD_V2. Una estructura de referencia puede ser:

```text
Documentacion_RedVital/
└── Pruebas/
    └── Sprint_4/
        ├── TD_V2.md
        ├── Informes_E2E/
        │   ├── Informe_Pruebas_E2E-01_Sprint4.md
        │   ├── Informe_Pruebas_E2E-02_Sprint4.md
        │   └── Informe_Pruebas_E2E-03_Sprint4.md
        ├── Resultados_Automatizados/
        │   ├── Pruebas_Unitarias/
        │   ├── Pruebas_Integracion/
        │   └── Pruebas_E2E_Interfaz/
        ├── Evidencias_SQL/
        ├── Evidencias_API/
        └── Rendimiento/
```

Esta estructura es una propuesta organizativa; debe ajustarse a la ubicación real de la documentación del proyecto.

### 9.3 Protección de la información

Las evidencias deben excluir contraseñas, tokens, claves privadas y valores de secretos. Cuando una respuesta o un log contenga información sensible, se debe conservar una versión depurada que mantenga los datos necesarios para verificar el resultado.

---

## 10. Conclusiones

El TD_V2 consolida la estrategia de pruebas del Sprint 4 y registra los resultados disponibles de las pruebas unitarias y de componentes, las pruebas de integración, los escenarios E2E de backend/API y las pruebas E2E de interfaz web.

Las ejecuciones automatizadas enumeradas en este documento suman 272 pruebas registradas: 269 aprobadas, tres fallidas y ninguna omitida. Las 40 pruebas de interfaz ejecutadas con Playwright y Chromium finalizaron con 40 aprobadas, cero fallidas y cero omitidas, en una duración de 26,8 segundos.

Los tres fallos corresponden a la suite de Campañas y requieren análisis de la preparación del *fixture* antes de determinar su causa definitiva. No se consideran defectos funcionales confirmados únicamente a partir de esos fallos.

Los escenarios E2E de backend/API documentados respaldan los recorridos específicos evaluados. Por su parte, las pruebas de interfaz aportan evidencia adicional sobre formularios, validaciones, navegación y acceso a rutas. Sin embargo, no demuestran por sí solas la integración completa entre la interfaz, los servicios, las bases de datos y los consumidores de eventos.

Para completar el cierre documental se requiere analizar los fallos pendientes, finalizar la trazabilidad con los requisitos oficiales, reconciliar las evidencias heredadas del TD_V1 y recibir los resultados de las pruebas de rendimiento.

**Estado general del TD_V2:** borrador para revisión.

**Resultado de las pruebas documentadas:** parcialmente ejecutadas, con los resultados y limitaciones de cada grupo identificados por separado.

**Defectos funcionales confirmados a partir de los hallazgos descritos:** ninguno establecido de forma definitiva en este documento. Existen fallos y observaciones que requieren seguimiento.

**Recomendación:** mantener el documento en revisión hasta completar las acciones pendientes y validar la trazabilidad y las evidencias de cierre.

---

## 11. Revisión y aprobación

| Actividad | Persona | Fecha | Resultado |
|---|---|---|---|
| Elaboración del documento | Tomás Silva | Aprobado | Borrador |
| Revisión técnica | Pendiente | Pendiente | Pendiente |
| Revisión de cobertura y evidencias | Pendiente | Pendiente | Pendiente |
| Aprobación final | Pendiente | Pendiente | Pendiente |

El TD_V2 permanecerá en estado de borrador hasta que se revisen las evidencias, se actualicen los pendientes y se complete el proceso de aprobación correspondiente.