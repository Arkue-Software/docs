# REDVITAL
## Plataforma Nacional para la Gestión Integral del Ciclo de Vida de la Donación de Sangre
### DOCUMENTO DE PRUEBAS - TD V2 Consolidado 2.1
Metodología, diseño, cobertura, resultados y trazabilidad de escenarios de calidad  
**Sprint 4**

*Arkhé Software S.A.S.*  
*De la raíz a la red*  
*Arquitectura de Software Empresarial*  
*Pontificia Universidad Javeriana*  
*Bogotá, Colombia*  
*19 de octubre de 2026*

---

## 1. Control del documento

### 1.1 Control de versiones

| Versión | Fecha | Descripción del cambio | Autor |
|---|---|---|---|
| 0.1 | 03/10/2026 | Creación inicial del plan por Tomás. | Tomás Silva |
| 0.2 | 03/10/2026 | Incorporación de tareas y delimitación del alcance. | Tomás Silva |
| 0.3 | 07/10/2026 | Contraste con SRS V4.0, DD V3.0, Herramientas V4.0 y T-302.1; cobertura y casos adicionales. | Tomás Silva |
| 1.0 | 08/10/2026 | Integración de registros de ejecución del Sprint 3; actualización del gateway observado, indicadores y brechas. | Tomás Silva |
| 2.0 | 18/10/2026 | Actualización de la estrategia de pruebas para Sprint 4, incorporación de resultados de pruebas unitarias, integración y E2E (TST-E2E-01, TST-E2E-02, TST-E2E-03, TST-INT-04, TST-UNIT-04), revisión de riesgos y trazabilidad con Escenarios de Calidad (EC). | Tomás Silva |
| 2.1 | 19/10/2026 | Incorporación de ejecuciones LT-01 a LT-04 en Gatling, resultados observados, limitaciones de autenticación, evidencias y actualización de cobertura, hallazgos y conclusiones. | Sara Muñoz (aporte de rendimiento) |

### 1.2 Responsables de revisión

| Rol | Responsable | Estado |
|---|---|---|
| QA / Tester | Tomás Silva | Elaboración y consolidación completadas |
| Arquitecta de Software / Pruebas de rendimiento | Sara Muñoz | Aporte LT-01 a LT-04 integrado; revisión consolidada pendiente |
| Scrum Master / Project Manager | Carlos Santiago Pinzón | Revisión de gestión pendiente |
| Product Owner | Alexander Aponte | Aprobación final pendiente |
| DevOps | Veli | Validación de ambiente y CI/CD pendiente |

---

## 2. Introducción

### 2.1 Propósito

Definir la metodología, el diseño, la cobertura, la trazabilidad con los Escenarios de Calidad (EC) y el reporte consolidado de las pruebas ejecutadas durante el Sprint 4 en la plataforma RedVital, dando continuidad al consolidado documental TD_V1.

### 2.2 Fuentes y precedencia

La base documental de este informe se compone de:
1. Documento de Pruebas TD_V1 Consolidado 1.0 (Sprint 3).
2. Catálogo de 42 Escenarios de Calidad del SAD V1.0 (ISO/IEC 25010:2023).
3. SRS V4.0 y DD V3.0 (Contratos, estados y matriz de operaciones).
4. Informes individuales de ejecuciones de Sprint 4:
   - `TST-UNIT-04` (Informe de pruebas unitarias).
   - `TST-INT-04` (Informe de pruebas de integración).
   - `TST-E2E-01-4` (Publicación de campaña y propagación a Donación).
   - `TST-E2E-02-4` (Registro de intención anónima y donación).
   - `TST-E2E-03-4` (Autenticación y autorización en consulta de donaciones).
   - `LT-01`, `LT-02`, `LT-03`, `LT-04` (informes Gatling individuales, capturas y resúmenes de ejecución del 19/10/2026).

### 2.3 Alcance incluido

El alcance del Sprint 4 abarca:
- **Pruebas Unitarias y de Componentes:** Servicio de Donación (Java: transiciones, políticas, arquitectura), Servicio de Identidad (.NET), Servicio de Campañas (.NET).
- **Pruebas de Integración:** `ServicioDonacionIntegracionTest` evaluando la interacción entre Donación, PostgreSQL y Kafka vía Testcontainers.
- **Pruebas Extremo a Extremo (E2E):**
  - TST-E2E-01: Publicación de campaña en API Campañas y proyección asíncrona en Donación vía Kafka.
  - TST-E2E-02: Registro de intención anónima, creación de donación autenticada, persistencia de unidad y evento outbox `donation.completed`.
  - TST-E2E-03: Verificación de controles de autenticación (401), autorización positiva con rol `operador` (200) y denegación con rol `auditor` (403) en `/v1/donaciones/{id}`.
- **Pruebas de rendimiento y estrés (Gatling):** cuatro ejecuciones LT-01 a LT-04 sobre autenticación e inventario del ambiente QA, con resultados de rendimiento, rechazos HTTP y comprobación de accesibilidad posterior a LT-04.

### 2.4 Alcance excluido o diferido

- Validación del objetivo de concurrencia con **20, 50 o 200 sesiones autenticadas efectivas**, no alcanzado en las ejecuciones por respuestas HTTP 429 durante la preparación.
- Verificación de CPU, memoria, conexiones PostgreSQL y reinicios con series históricas de Prometheus/Grafana; no se adjuntaron métricas de infraestructura al consolidado.
- Confirmación de ausencia de pérdida de datos y recuperación integral de todos los microservicios tras LT-04; solo se realizó comprobación de accesibilidad del Gateway.
- Consumo del evento `donation.completed` por consumidores de servicios externos fuera del alcance de Donación/Campañas.
- Validación de casos negativos adicionales y combinaciones exhaustivas de todos los roles sobre la totalidad de endpoints del sistema.

### 2.5 Servicios y arquitectura observada

El entorno de pruebas de QA incluye:
- **Servicio de Identidad** (.NET / PostgreSQL `db_identidad`).
- **Servicio de Donación** (Java / PostgreSQL `db_donacion`).
- **Servicio de Campañas** (.NET / PostgreSQL `db_campana`).
- **Mecanismos de Infraestructura y Borde:** Gateway APISIX / Caddy, Broker Kafka para mensajería de eventos, y motor PostgreSQL para tablas de dominio, outbox y proyecciones.

---

## 3. Metodología de pruebas

### 3.1 Enfoque y caminos

Se mantiene la clasificación de caminos del plan base para la evaluación de entradas y comportamientos:

| Camino | Definición |
|---|---|
| **F** | Entrada válida, rol autorizado y estado previsto (Camino feliz) |
| **N** | Entrada inválida, rol denegado, ámbito ajeno o transición prohibida (Camino negativo) |
| **B** | Límite de tiempo, cantidad, estado o reenvío (Borde) |
| **X** | Fallo inducido, sobrecarga o indisponibilidad (Resiliencia / Estrés) |

### 3.2 Tipos y cantidades del diseño

| Tipo de prueba | Nivel / Enfoque | Casos / Suites Sprint 4 |
|---|---|---|
| Pruebas Unitarias / Dominio | Lógica aislada, transiciones y políticas | 181 (Donación), 20 (Identidad), 24 (Campañas) |
| Pruebas de Integración | Interacción de servicios con DB y Kafka | 7 casos (`ServicioDonacionIntegracionTest`) |
| Pruebas E2E | Flujos de proceso, API, DB y eventos | 3 escenarios principales (TC-E2E-01, TC-E2E-02, TC-E2E-03) |
| Pruebas de Rendimiento | Carga, resistencia y estrés con Gatling | 4 escenarios ejecutados (LT-01 a LT-04), con observaciones |

### 3.3 Niveles y herramientas

- **Unitarias:** Maven, JUnit 5, Mockito para Java (Donación); .NET Test Platform / xUnit para C# (Identidad y Campañas).
- **Integración:** Testcontainers (PostgreSQL, Kafka) para `ServicioDonacionIntegracionTest`.
- **E2E y API:** Requests HTTP (cURL / cliente REST), consultas SQL directas en PostgreSQL (`psql`), verificación de logs del contenedor.
- **Rendimiento:** Gatling (simulaciones Java), Maven y Docker en la VM Tools; informes HTML y registros por escenario.
- **Orquestación y Ambiente:** Docker, Docker Compose, GitHub Actions.

### 3.4 Ambiente, datos y reproducibilidad

- **Ambiente:** Pruebas / QA.
- **Bases de datos:** Instancias PostgreSQL para `db_identidad`, `db_donacion` y `db_campana`.
- **Datos de prueba:** Datos sintéticos con semillas de QA, códigos de intención anónima (`NF7HT298BBQ4`), UUIDs predefinidos para campañas e instituciones sintéticas, y tokens de acceso Bearer JWT sintéticos.
- **Entorno de rendimiento:** Gatling ejecutado en VM Tools (`10.43.100.121`) mediante imagen Maven y Docker; tráfico HTTPS al Gateway QA (`10.43.98.220`), autenticación `POST /api/v1/sesiones` y consultas `GET /api/v1/inventario`. Se utilizaron variables de entorno protegidas; no se incluyen credenciales ni tokens en el TD.
- **Perfil ejecutado:** LT-01: 20 usuarios incorporados en 20 s y hasta 60 s de consultas por sesión autenticada; LT-02: rampa de 0 a 50 en 2 min y mantenimiento de 50 usuarios virtuales hasta 10 min; LT-03: perfil sostenido de 50 usuarios virtuales durante 30 min; LT-04: rampa a 200 en 1 min y mantenimiento hasta 5 min. **Usuario virtual activo no equivale necesariamente a usuario autenticado que consulta inventario.**

### 3.5 Protocolo y criterios de entrada/salida

- **Criterios de Entrada:** Disponibilidad de los microservicios en QA, contenedores de PostgreSQL y Kafka operativos, contratos OpenAPI / AsyncAPI publicados y credenciales sintéticas operativas.
- **Criterios de Salida:** Ejecución del 100% de los casos programados, reporte de evidencias sin exposición de secretos, y categorización/registro formal de hallazgos y defectos.

### 3.6 Gestión de defectos

Los defectos se clasifican en cuatro niveles de severidad: Crítica, Alta, Media y Baja. Los fallos derivados de problemas de infraestructura o rutas de archivos en suites automatizadas se registran como problemas de entorno de pruebas hasta su confirmación técnica.

---

## 4. Diseño de pruebas

### 4.1 Índice y estado de recepción

| Código / Suite | Descripción / Alcance | Estado de recepción |
|---|---|---|
| **TST-UNIT-DON** | Pruebas unitarias de Donación (Transiciones, Políticas, Arquitectura) | Recibido (181/181 Aprobadas) |
| **TST-UNIT-ID** | Pruebas unitarias de Identidad (.NET) | Recibido (20/20 Aprobadas) |
| **TST-UNIT-CAMP** | Pruebas unitarias de Campañas (.NET) | Recibido (21/24 Aprobadas, 3 Fallidas por ruta SQL) |
| **TST-INT-DON** | Pruebas de integración de Donación con Testcontainers (DB + Kafka) | Recibido (7/7 Aprobadas) |
| **TC-E2E-01** | Publicación de campaña y propagación de evento a Donación | Recibido (Aprobado) |
| **TC-E2E-02** | Registro de intención anónima, donación, unidad y evento outbox | Recibido (Aprobado) |
| **TC-E2E-03** | Autenticación y autorización en consulta de donaciones | Recibido (Aprobado) |
| **LT-01 a LT-04** | Carga, sostenimiento, resistencia y estrés con Gatling | Ejecutadas; resultados con observaciones de autenticación y cobertura |

### 4.2 Condiciones mínimas de los casos con archivo externo

Las pruebas E2E requieren la verificación tripartita: respuesta del endpoint de la API (código de estado HTTP y payload JSON), verificación de persistencia en la base de datos PostgreSQL mediante consultas SQL, y verificación de publicación/consumo de eventos en los registros outbox o logs de contenedores.

### 4.3 Métricas y umbrales del plan

- **Pruebas Unitarias / Integración:** $100\%$ de aprobación esperado en suites limpias.
- **API y E2E:** Tiempos de respuesta HTTP $< 3000\text{ ms}$, persistencia exacta en tablas de dominio y transición de estados del evento outbox a `publicado`.

### 4.4 Carga, sobrecarga y estrés: diseño y ejecución

| Prueba | Tipo | Escenario de calidad | Objetivo de diseño | Resultado de ejecución |
|---|---|---|---|---|
| LT-01 | Carga | EC-15 | 20 usuarios concurrentes, p95 ≤ 3 s | Ejecutada: 10/20 autenticaciones exitosas; 600/600 inventario OK; concurrencia objetivo no validada |
| LT-02 | Sostenida | EC-16 | 50 usuarios, 10 min, errores < 1 % | Ejecutada: 20/50 autenticaciones exitosas; 11.313/11.313 inventario OK; error global 0,26 %, pero sin 50 sesiones válidas |
| LT-03 | Resistencia | EC-26 | 50 usuarios, 30 min, CPU < 80 % | Ejecutada 30 min 12 s: 19.870 errores 401 en inventario; métricas CPU no aportadas |
| LT-04 | Sobrecarga | EC-37 | 200 usuarios, 5 min, rechazo controlado y recuperación | Ejecutada 5 min 14 s: 10/200 autenticaciones exitosas; 3.104/3.104 inventario OK; Gateway accesible después, recuperación integral no verificada |

**Alcance de la evidencia:** la duración y la cantidad de usuarios virtuales son parámetros del generador Gatling; el número de sesiones efectivamente autenticadas es inferior al objetivo en LT-01, LT-02 y LT-04. La comparación de umbrales se interpreta con esa limitación.

---

## 5. Cobertura y Matriz de Calidad

### 5.1 Cobertura diseñada por operación

| ID Operación | Endpoint / Descripción | Pruebas asociadas Sprint 4 |
|---|---|---|
| **ID-01** | `POST /v1/sesiones` | TST-UNIT-ID, TC-E2E-01, TC-E2E-02, TC-E2E-03, LT-01 a LT-04 (ruta QA `/api/v1/sesiones`) |
| **DON-02** | `POST /v1/donaciones` | TST-UNIT-DON, TST-INT-DON, TC-E2E-02 |
| **DON-08** | `GET /v1/donaciones/{id}` | TST-UNIT-DON, TC-E2E-03 |
| **CAM-03** | `POST /v1/campanias/{id}/publicacion` | TST-UNIT-CAMP, TC-E2E-01 |
| **INT-01** | `POST /v1/intenciones` | TST-UNIT-DON, TC-E2E-02 |
| **INV-QA** | `GET /api/v1/inventario` (ruta observada en QA) | LT-01, LT-02, LT-03, LT-04 |

### 5.2 Cobertura funcional de la línea base

Las ejecuciones del Sprint 4 cubren las funcionalidades clave de autenticación de usuarios sintéticos, registro de intenciones de donantes anónimos, creación de donaciones con generación automática de unidades, publicación de campañas institucionales y sincronización de proyecciones mediante arquitectura orientada a eventos.

### 5.3 Matriz de Calidad: Asociación de Nuevas Pruebas del Sprint 4 con Escenarios de Calidad (EC)

A continuación se asocia cada una de las pruebas ejecutadas en el Sprint 4 con los Escenarios de Calidad (EC) definidos en el catálogo del SAD:

| Código de Nueva Prueba | Componente / Suite | Escenario de Calidad (EC) Asociado | Atributo / Subcaracterística ISO 25010 | Descripción de la Verificación de Calidad |
|---|---|---|---|---|
| **TC-E2E-01** | Campañas / Donación / Kafka | **EC-20** (Correlación y propagación de eventos)<br>**EC-22** (Evolución y contrato entre servicios) | Integración / Trazabilidad / Modificabilidad | Verifica que la publicación de una campaña propague síncronamente el evento `campaign.published` vía Kafka y actualice la proyección `campania_publicada` en Donación manteniendo el identificador de correlación. |
| **TC-E2E-02** | Donación / API / DB Outbox | **EC-10** (Idempotencia en registro de donación)<br>**EC-17** (Registro de intención anónima)<br>**EC-04** (Transición de estados de unidad) | Integridad de datos / Usabilidad / Correctitud funcional | Valida la creación anónima de intenciones, la donación asociada mediante API autenticada, la generación de la unidad en estado `captada` y el registro outbox `donation.completed` con estado `publicado`. |
| **TC-E2E-03** | Donación / Identidad | **EC-01** (Control de acceso y permisos por rol)<br>**EC-03** (Autenticación y validación de tokens JWT) | Seguridad / Confidencialidad / Autenticidad | Comprueba la restricción de acceso en `/v1/donaciones/{id}`: rechazo sin token (`401`), acceso permitido para rol `operador` (`200`) y denegación explícita para rol `auditor` (`403`). |
| **TST-INT-DON** | Servicio de Donación (`ServicioDonacionIntegracionTest`) | **EC-08** (Aislamiento de fallo de servicios)<br>**EC-20** (Procesamiento de eventos asíncronos) | Resiliencia / Coexistencia / Fiabilidad | Evalúa con Testcontainers la persistencia en PostgreSQL real y el procesamiento de eventos Kafka desde dependencias aisladas de infraestructura. |
| **TST-UNIT-DON** | Servicio de Donación (Java) | **EC-04** (Máquina de estados y transiciones)<br>**EC-21** (Modificabilidad de reglas de negocio) | Mantenibilidad / Lógica de dominio | Cobertura unitaria de las suites `MotorTransicionesTest`, `PoliticasTest` y `ArquitecturaTest` validando transiciones de unidades y políticas del dominio. |
| **TST-UNIT-ID** | Servicio de Identidad (.NET) | **EC-03** (Sesiones, tokens y emisión JWT) | Seguridad / Autenticidad | Validación unitaria de credenciales, emisión de tokens Bearer y control de sesiones en el servicio de Identidad. |
| **TST-UNIT-CAMP** | Servicio de Campañas (.NET) | **EC-22** (Contratos de servicio y gestión de campañas) | Correctitud / Mantenibilidad | Verificación unitaria del ciclo de vida y contratos de campañas (21 pruebas aprobadas). |
| **LT-01** | Gateway / consulta de inventario | **EC-15** (Rendimiento de inventario) | Eficiencia de desempeño | Verifica latencias de consultas de inventario bajo carga gradual; limitación de autenticación impide demostrar 20 usuarios válidos. |
| **LT-02** | Gateway / inventario | **EC-16** (Operación sostenida) | Eficiencia de desempeño / Fiabilidad | Ejecuta 10 min de actividad virtual; se separan rechazos de autenticación y respuestas de inventario. |
| **LT-03** | Gateway / inventario | **EC-26** (Resistencia) | Fiabilidad / Eficiencia de desempeño | Ejecuta 30 min; documenta errores 401 durante consultas y la falta de registros CPU/memoria. |
| **LT-04** | Gateway / inventario | **EC-37** (Sobrecarga y recuperación) | Fiabilidad / Capacidad | Ejecuta estrés de 200 usuarios virtuales, documenta rechazos 429 y comprueba respuesta HTTP 401 sin token después de la carga. |

---

## 6. Reporte consolidado de pruebas

### 6.1 Reglas de estado

- **Aprobado:** El procedimiento fue ejecutado con éxito y los resultados coinciden con los esperados.
- **Ejecución Parcial / Con Observaciones:** La funcionalidad opera pero existen advertencias o fallos de preparación de ambiente/archivos que requieren atención.
- **Bloqueado:** La prueba no pudo ser ejecutada por falta de precondiciones o herramientas.

### 6.2 Indicadores consolidados del Sprint 4

| Métrica / Indicador | Valor registrado |
|---|---|
| Total de pruebas unitarias/integración registradas | 232 |
| Pruebas unitarias/integración aprobadas | 229 |
| Pruebas unitarias/integración fallidas | 3 (en Campañas por ruta SQL) |
| Porcentaje de aprobación en automatizadas | $98{,}71\%$ |
| Escenarios E2E ejecutados y aprobados | 3 de 3 ($100\%$) |
| Defectos funcionales confirmados en E2E | 0 |
| Escenarios Gatling ejecutados | 4 de 4 (LT-01 a LT-04; con observaciones) |
| Solicitudes Gatling totales, cuatro ejecuciones finales | 78.576 |
| Solicitudes Gatling OK / KO, cuatro ejecuciones finales | 58.436 OK / 20.140 KO |
| Resultado de inventario en LT-03 | 43.329 OK / 19.870 KO (401 observado) |

### 6.3 Historial de pruebas realizadas en TD_V1

En el Documento de Pruebas TD_V1 (Sprint 3) se registraron las siguientes 7 pruebas realizadas y aprobadas:

1. **TC-006M:** Control de acceso entre instituciones sobre unidades.
2. **TC-008:** Registro de auditoría de operaciones realizadas.
3. **TC-010:** Idempotencia en el registro de una donación.
4. **TC-011:** Aislamiento ante fallo de un servicio.
5. **TC-012:** Validación del contrato de consulta de campañas.
6. **TC-013:** Arranque completo de la plataforma.
7. **TC-020M:** Flujo completo de integración de donación.

### 6.4 Resultados por caso de las nuevas pruebas del Sprint 4

#### 6.4.1 Pruebas Extremo a Extremo (E2E)

- **TC-E2E-01: Publicación de campaña y propagación a Donación**
  - *Resultado:* Aprobado.
  - *Detalles:* Se publicó la campaña `e5100000-0000-4000-8000-202610170001` vía `POST /v1/campanias/{id}/publicacion` (HTTP `200 OK`). El evento outbox `campaign.published` quedó en estado `publicado`, el consumidor en Donación procesó la novedad y la tabla `public.campania_publicada` en `db_donacion` persistió la información correctamente.
- **TC-E2E-02: Registro de intención anónima y donación**
  - *Resultado:* Aprobado.
  - *Detalles:* Intención anónima registrada en `POST /v1/intenciones` (código `NF7HT298BBQ4`, HTTP `201 Created`). Registro de donación autenticado en `POST /v1/donaciones` (HTTP `201 Created`, donación `513ec2d3-5d6c-4800-b62e-eab0f6325d92`, unidad `6008010b-21d3-495c-a32f-9ca9eedcb38f`). Persistencia validada en `public.donacion` y `public.unidad`. Evento outbox `donation.completed` generado en estado `publicado`.
- **TC-E2E-03: Control de acceso a la consulta de donaciones**
  - *Resultado:* Aprobado.
  - *Detalles:* Endpoint `GET /v1/donaciones/513ec2d3-5d6c-4800-b62e-eab0f6325d92`: sin token devolvió HTTP `401 Unauthorized` (`sesion-invalida`); con token de `operador` devolvió HTTP `200 OK`; con token de `auditor` devolvió HTTP `403 Forbidden` (`operacion-no-permitida`).

#### 6.4.2 Pruebas Unitarias e Integración

- **Servicio de Donación (Java):** 181 de 181 pruebas aprobadas ($100\%$).
- **Servicio de Identidad (.NET):** 20 de 20 pruebas aprobadas ($100\%$).
- **Servicio de Campañas (.NET):** 21 de 24 pruebas aprobadas ($87{,}5\%$). 3 pruebas fallaron durante la preparación de la base de datos por búsqueda de un archivo SQL en la ruta no disponible `/home/estudiante/Downloads/RedVital/BDs_RedVital/databases/campanas/00_roles.sql`.
- **Integración Servicio de Donación:** 7 de 7 pruebas aprobadas ($100\%$) utilizando Testcontainers para PostgreSQL y Kafka.

#### 6.4.3 Pruebas de rendimiento, resistencia y estrés (Gatling)

Los resultados siguientes corresponden a las **ejecuciones finales conservadas** de cada caso; no incluyen preflight, ensayos interrumpidos ni la ejecución inicial de LT-02 que generó reintentos masivos. La clasificación OK/KO es la emitida por Gatling conforme a su chequeo de estado HTTP 200.

| Prueba | Duración reportada | Usuarios virtuales objetivo | Autenticación OK/KO | Inventario OK/KO | Solicitudes globales OK/KO | p95 inventario | Resultado |
|---|---|---:|---:|---:|---:|---:|---|
| **LT-01** | 1 min 18 s | 20 | 10 / 10 (429) | 600 / 0 | 610 / 10 | 14 ms | Ejecución completada; objetivo 20 autenticados no demostrado |
| **LT-02** | 10 min 12 s | 50 | 20 / 30 (429) | 11.313 / 0 | 11.333 / 30 | 13 ms | Ejecución completada; objetivo 50 autenticados no demostrado |
| **LT-03** | 30 min 12 s | 50 | 50 / 40 (429) | 43.329 / 19.870 (401) | 43.379 / 19.910 | 13 ms | Ejecución completada con 31,46 % KO global; no cumple operación sostenida sin errores |
| **LT-04** | 5 min 14 s | 200 | 10 / 190 (429) | 3.104 / 0 | 3.114 / 190 | 12 ms | Ejecución completada; el Gateway respondió tras la carga, pero no se validaron 200 usuarios autenticados |

**Interpretación por escenario:**

- **LT-01 (EC-15):** el inventario respondió satisfactoriamente a las 600 solicitudes cursadas, con p95 de 14 ms; 10 rechazos HTTP 429 impidieron demostrar 20 sesiones autenticadas simultáneas.
- **LT-02 (EC-16):** las 11.313 consultas cursadas no presentaron KO, con p95 de 13 ms. La proporción de error global fue 0,26 %, inferior al umbral numérico del 1 %, **pero la carga efectiva fue inferior al objetivo de 50 usuarios autenticados**, por lo que no se declara aprobación integral.
- **LT-03 (EC-26):** las 19.870 solicitudes de inventario KO devolvieron HTTP 401. Una hipótesis es el vencimiento o la falta de renovación de JWT, **no confirmada**; el error ocurrió en inventario y no debe confundirse con latencia alta. El registro de 90 intentos de autenticación (50 OK, 40 KO) responde a la dinámica del modelo de usuarios virtuales durante el escenario; no permite inferir 50 sesiones válidas permanentes. No se verificaron los umbrales de CPU, RAM, conexiones de PostgreSQL ni reinicios.
- **LT-04 (EC-37):** 190 intentos de autenticación devolvieron HTTP 429; las 3.104 consultas admitidas de inventario resultaron OK. Después de la carga, desde Tools, `GET /api/v1/inventario` **sin token** devolvió HTTP 401 en **0,025140 s**. Esto demuestra que el Gateway recibió y respondió a la petición posterior, **pero no valida la recuperación integral, la persistencia de datos ni la capacidad para 200 sesiones autenticadas**.

**Hallazgo transversal:** los rechazos HTTP 429 de inicio de sesión limitan la preparación de sesiones para medir inventario bajo concurrencia objetivo. El generador fue corregido para no reemplazar inmediatamente usuarios rechazados, evitando una multiplicación artificial de autenticaciones. Una ejecución anterior de LT-02 registró 163.293 errores de autenticación por esa dinámica y **no se usa como resultado representativo de inventario**.

### 6.5 Brechas entre lo ejecutado y el criterio original

- **Pruebas de Campañas (.NET):** Requieren corrección en la ruta de montaje del script SQL `00_roles.sql` para alcanzar el $100\%$ de ejecución limpia.
- **Consumo multisericio extenso:** Los eventos outbox se comprobaron en las tablas de persistencia y en la proyección directa de Donación; el consumo por parte de otros componentes externos diferidos se mantiene fuera de la frontera de esta prueba.
- **Carga y resistencia:** no se demostraron todas las sesiones concurrentes objetivo; en LT-03 se observaron 401 en inventario y no hay confirmación de renovación de JWT.
- **Infraestructura y recuperación:** faltan series CPU/RAM, conexiones de bases de datos, reinicios y verificaciones de integridad de datos para cerrar EC-26 y EC-37.

### 6.6 Evidencias y trazabilidad

Se conservan los identificadores de correlación HTTP (`3bbb1c99-76bc-4b73-9017-d17d994e52e8`, `d68e3a9b-a6bd-414b-acb9-54532fe39580`, `41bbc9b3-dbb0-47cc-8afe-502ddd84f10f`), los logs de consola de `ConsumidorCampaniaPublicada`, los resúmenes de ejecución de Maven y .NET Test Platform, y las capturas/consultas SQL a PostgreSQL.

**Evidencia Gatling:** informes individuales `LT-01.md` a `LT-04.md`, capturas de la tabla `Details` y `Number of responses per second`, y reportes HTML de las siguientes ejecuciones:

| Prueba | Directorio del reporte Gatling (`target/gatling/`) |
|---|---|
| LT-01 | `redvitallt01simulation-20261019003806090` |
| LT-02 | `redvitallt0104simulation-20261019005235849` |
| LT-03 | `redvitallt0104simulation-20261019000623808` |
| LT-04 | `redvitallt0104simulation-20261019011842655` |

El reporte HTML y las capturas deben conservarse como archivos asociados al TD para mantener evidencias verificables; **no se incluyen contraseñas, tokens ni valores de `.env.qa`**.

---

## 7. Plan de pruebas y ejecuciones pendientes

### 7.1 Pruebas automatizadas (Unitarias e Integración)

1. Reejecutar la suite de Campañas (.NET) tras ajustar la ruta del archivo `00_roles.sql`.
2. Mantener la ejecución continua de `MotorTransicionesTest`, `PoliticasTest`, `ArquitecturaTest` e Identidad en el pipeline de CI/CD.

### 7.2 Pruebas E2E

1. Ampliar escenarios de autorización para incluir combinaciones con roles `administrador` e `institucional`.
2. Ejecutar validaciones de idempotencia ante peticiones duplicadas consecutivas sobre los endpoints de registro.

### 7.3 Pruebas de rendimiento

Las cuatro pruebas LT-01 a LT-04 **ya se ejecutaron**; no se programan nuevas ejecuciones automáticamente. Para validar los criterios pendientes se requiere, en una futura campaña autorizada: preparar sesiones QA válidas respetando las protecciones de autenticación; implementar o confirmar la renovación de JWT en las simulaciones largas; capturar métricas de Prometheus/Grafana (CPU, memoria, conexiones y reinicios); y verificar explícitamente la integridad/recuperación posterior a estrés. Preservar los resultados actuales como evidencia diagnóstica, sin etiquetarlos como aprobación de la concurrencia objetivo.

---

## 8. Riesgos, dependencias y hallazgos

### 8.1 Actualización de hallazgos heredados

- **APISIX / Gateway:** Confirmada la operación de APISIX como proxy de borde activo en las ejecuciones de integración y E2E.
- **Idempotencia:** La estructura outbox e idempotencia operativa fue comprobada a nivel de persistencia en `TC-E2E-02`.

### 8.2 Hallazgos de las ejecuciones recibidas en Sprint 4

- **E-07 (Entorno / Rutas SQL en Campañas):** Las 3 pruebas fallidas en Campañas obedecen a una ruta absoluta hardcodeada en el fixture de prueba de la base de datos.
  - *Acción:* Ajustar la ruta hacia un camino relativo dentro del repositorio.
- **E-08 (Observación sobre filtro de estado en Campañas):** En exploraciones de API se observó respuesta HTTP `200 OK` al consultar campañas con `estado=borrador`.
  - *Acción:* Verificar contra el contrato OpenAPI y ajustar las validaciones de filtro si aplica.
- **E-09 (Carga / Límite de autenticación):** HTTP 429 en LT-01 a LT-04, impidiendo completar la cantidad objetivo de sesiones autenticadas.
  - *Acción:* Coordinar preparación de sesiones de QA o configuración autorizada para pruebas, manteniendo controles de seguridad.
- **E-10 (Resistencia / Inventario no autorizado):** 19.870 solicitudes HTTP 401 en LT-03; posible expiración de tokens, sin prueba causal definitiva.
  - *Acción:* Inspeccionar trazas de autorización y el mecanismo vigente de renovación JWT antes de repetir una validación prolongada.
- **E-11 (Evidencia operacional insuficiente):** no se recibieron series de CPU/RAM ni pruebas de integridad para LT-03 y LT-04.
  - *Acción:* Adjuntar series de Prometheus/Grafana y verificaciones de recuperación en la próxima campaña.

### 8.3 Riesgos que permanecen

1. **Riesgo de Configuración de Pruebas:** Dependencias de rutas locales en scripts de inicialización de bases de datos de prueba pueden afectar ejecuciones en entornos de CI/CD limpios.
2. **Cobertura de Consumidores Adicionales:** Verificación limitada al consumidor de Donación; otros consumidores futuros de eventos de dominio deben ser validados en entregas posteriores.
3. **Validez de la carga:** la concurrencia virtual no representa necesariamente concurrencia autenticada por los rechazos del Gateway.
4. **Resistencia:** la vigencia del JWT puede afectar la continuidad de peticiones de inventario; falta confirmar causa de los 401.
5. **Observabilidad incompleta:** no puede afirmarse cumplimiento de umbrales de recursos ni recuperación integral sin sus métricas y controles.

---

## 9. Revisión y conclusión de cierre

El plan de pruebas del Sprint 4 presenta resultados positivos en las suites funcionales y **observaciones relevantes en rendimiento**:
- Se logró un **98,71 % de aprobación** en las suites unitarias/de integración (229/232 pruebas); **este porcentaje no incluye E2E ni Gatling**.
- Los **3 escenarios E2E ejecutados (TC-E2E-01, TC-E2E-02, TC-E2E-03)** fueron **aprobados al 100%**, verificando exitosamente el flujo entre las API, la persistencia en PostgreSQL, la seguridad por roles (401, 200, 403) y la mensajería asíncrona de eventos vía Kafka.
- Se trazaron y asociaron los casos con los **Escenarios de Calidad (EC-01, EC-03, EC-04, EC-08, EC-10, EC-15, EC-16, EC-17, EC-20, EC-21, EC-22, EC-26 y EC-37)**.
- **LT-01 a LT-04 se ejecutaron** y cuentan con evidencias Gatling; los rechazos 429 limitaron la concurrencia efectiva y LT-03 presentó 19.870 respuestas 401 en inventario. Estas pruebas se registran **ejecutadas con observaciones**, no aprobadas integralmente.

**Recomendación:** Aprobar los escenarios E2E e integración ejecutados; corregir la ruta SQL de Campañas; conservar los informes de Gatling como evidencia de ejecución y tramitar los hallazgos de autenticación, renovación de sesiones y observabilidad antes de validar formalmente los umbrales de carga y resistencia.

---

## Anexo A: Fichas y resúmenes de las nuevas pruebas del Sprint 4

### A.1 Resumen de TST-E2E-01 (Publicación de Campaña)
- **Caso:** TC-E2E-01.
- **Operaciones:** `POST /v1/campanias/{id}/publicacion`.
- **Verificación:** HTTP `200 OK`, evento outbox `campaign.published` en estado `publicado`, log de `ConsumidorCampaniaPublicada` confirmado, registro en `public.campania_publicada` en `db_donacion`.
- **Veredicto:** Aprobado.

### A.2 Resumen de TST-E2E-02 (Intención Anónima y Donación)
- **Caso:** TC-E2E-02.
- **Operaciones:** `POST /v1/intenciones`, `POST /v1/donaciones`.
- **Verificación:** Intención `NF7HT298BBQ4` (`201 Created`), Donación `513ec2d3-5d6c-4800-b62e-eab0f6325d92` (`201 Created`), unidad `6008010b-21d3-495c-a32f-9ca9eedcb38f` en `public.unidad`, evento `donation.completed` publicado.
- **Veredicto:** Aprobado.

### A.3 Resumen de TST-E2E-03 (Control de Acceso)
- **Caso:** TC-E2E-03.
- **Operaciones:** `GET /v1/donaciones/{id}`.
- **Verificación:** Sin token $\rightarrow$ `401 Unauthorized`; Rol `operador` $\rightarrow$ `200 OK`; Rol `auditor` $\rightarrow$ `403 Forbidden`.
- **Veredicto:** Aprobado.

### A.4 Resumen de TST-INT-04 (Integración Donación)
- **Suite:** `ServicioDonacionIntegracionTest`.
- **Infraestructura:** Testcontainers (PostgreSQL + Kafka).
- **Resultado:** 7 de 7 pruebas aprobadas.
- **Veredicto:** Aprobado.

### A.5 Resumen de TST-UNIT-04 (Pruebas Unitarias)
- **Donación:** 181/181 aprobadas.
- **Identidad:** 20/20 aprobadas.
- **Campañas:** 21/24 aprobadas (3 fallidas por ruta de archivo SQL).
- **Veredicto:** Parcialmente Aprobado (pendiente ajuste de entorno en Campañas).

---

## Anexo B: Fichas resumidas de las pruebas Gatling

### B.1 LT-01 — Carga de inventario
- **Perfil:** 20 usuarios virtuales incorporados progresivamente en 20 s; hasta 60 s de consultas por usuario autenticado.
- **Resultados:** 20 autenticaciones (10 OK, 10 HTTP 429); 600 consultas de inventario, todas OK; p95 de inventario 14 ms.
- **Estado:** Ejecutada con observaciones; no se acreditó concurrencia efectiva de 20 sesiones autenticadas.
- **Evidencia:** `LT-01.md` y reporte `redvitallt01simulation-20261019003806090`.

### B.2 LT-02 — Operación sostenida
- **Perfil:** rampa hasta 50 usuarios virtuales en 2 min; mantenimiento hasta 10 min.
- **Resultados:** 50 autenticaciones (20 OK, 30 HTTP 429); 11.313 consultas de inventario, todas OK; p95 de inventario 13 ms; error global 0,26 %.
- **Estado:** Ejecutada con observaciones; no se acreditó concurrencia efectiva de 50 sesiones autenticadas.
- **Evidencia:** `LT-02.md` y reporte `redvitallt0104simulation-20261019005235849`.

### B.3 LT-03 — Resistencia de 30 minutos
- **Perfil:** escenario sostenido de 50 usuarios virtuales; duración reportada de 30 min 12 s.
- **Resultados:** 90 autenticaciones (50 OK, 40 HTTP 429); 63.199 solicitudes de inventario (43.329 OK, 19.870 HTTP 401); p95 de inventario 13 ms; error global 31,46 %.
- **Estado:** Ejecutada con incumplimiento observable de continuidad autorizada; causa específica de los 401 por confirmar y métricas de infraestructura pendientes.
- **Evidencia:** `LT-03.md` y reporte `redvitallt0104simulation-20261019000623808`.

### B.4 LT-04 — Estrés de 200 usuarios virtuales
- **Perfil:** rampa hasta 200 usuarios virtuales en 1 min; mantenimiento durante los 4 min restantes.
- **Resultados:** 200 autenticaciones (10 OK, 190 HTTP 429); 3.104 consultas de inventario, todas OK; p95 de inventario 12 ms; error global 5,75 %.
- **Verificación posterior:** Gateway QA respondió HTTP 401 sin token en 25,14 ms, señal de accesibilidad del punto de entrada, no prueba de recuperación integral.
- **Estado:** Ejecutada con observaciones; capacidad de 200 sesiones autenticadas y ausencia de pérdida de datos no verificadas.
- **Evidencia:** `LT-04.md` y reporte `redvitallt0104simulation-20261019011842655`.
