# Informe de pruebas - Componente Institucional

## Control del documento

| Campo | Información |
|---|---|
| Código del documento | TST-INSTITUCIONAL |
| Versión | 0.1 |
| Estado | Borrador |
| Responsable del informe | Tomás Silva |
| Ejecutado por | Tomás Silva |
| Fecha de ejecución | 05/11/2026 |
| Ambiente | Pruebas |
| Rama | release |

## Historial de cambios

| Versión | Fecha | Responsable | Descripción |
|---|---|---|---|
| 0.1 | 05/11/2026 | Tomás Silva | Consolidación de las pruebas manuales realizadas al componente Institucional. |

---

## 1. Resumen ejecutivo

Se realizaron pruebas manuales de API sobre el componente Institucional de RedVital, con el objetivo de verificar su disponibilidad, los mecanismos de autenticación y autorización, y las operaciones de consulta de auditoría, territorios, instituciones e indicadores.

Las pruebas se ejecutaron directamente contra el servicio desplegado en el ambiente de pruebas, utilizando cuentas existentes y solicitudes HTTP. Se ejecutaron 16 casos, todos con resultados satisfactorios. No se identificaron defectos durante los escenarios evaluados.

## 2. Información de ejecución

| Campo | Información |
|---|---|
| Componente | Institucional |
| Tipo de prueba | Pruebas manuales de API |
| Alcance | Disponibilidad, autenticación, autorización y consultas |
| Servicio | API Institucional |
| Endpoint de salud | `/salud` |
| Puerto | 8084 |
| Ambiente | Pruebas |
| Fecha | 05/11/2026 |
| Ejecutor | Tomás Silva |
| Dependencias | Servicio de Identidad y datos del componente Institucional |
| Tipo de dependencia | Real, dentro del ambiente de pruebas |

### 2.1 Preparación del escenario

Se verificó la disponibilidad del servicio y se obtuvieron tokens mediante el endpoint de sesiones del servicio de Identidad. Se inspeccionaron los controladores y las políticas de autorización en modo de solo lectura para identificar los permisos requeridos.

No se modificaron archivos, configuraciones, datos ni permisos.

## 3. Ejecución de pruebas

### 3.1 Disponibilidad, autenticación y autorización

| ID | Prueba | Resultado obtenido | Estado |
|---|---|---|---|
| INST-01 | Consulta de `/salud` | HTTP 200, `Healthy` | Aprobado |
| INST-02 | Acceso sin autenticación a territorios, instituciones, indicadores y auditoría | Los cuatro endpoints respondieron HTTP 401 | Aprobado |
| INST-03 | Inicio de sesión con cuenta de auditor | HTTP 200 y token de acceso | Aprobado |
| INST-04 | Acceso a auditoría con rol `admin_nacional` | HTTP 403, permiso correctamente restringido | Aprobado |

### 3.2 Consulta de auditoría

Endpoint: `GET /v1/auditoria`.

| ID | Prueba | Resultado obtenido | Estado |
|---|---|---|---|
| INST-05 | Consulta inicial con `tamano=5` | HTTP 200, 5 registros y cursor de continuación | Aprobado |
| INST-06 | Consulta de página adicional mediante `antes_de` | HTTP 200, 5 registros adicionales | Aprobado |
| INST-07 | Filtrado mediante `correlacion_id` | HTTP 200, un registro coincidente | Aprobado |
| INST-08 | Consulta con `tamano=500` | HTTP 400, se informó el rango permitido de 1 a 200 | Aprobado |

### 3.3 Consulta de territorios

Endpoint: `GET /v1/territorios`.

| ID | Prueba | Resultado obtenido | Estado |
|---|---|---|---|
| INST-09 | Consulta con `pagina=1&tamano=10` | HTTP 200, 10 elementos | Aprobado |
| INST-10 | Verificación del total de registros | La respuesta indicó 123 elementos | Aprobado |

### 3.4 Consulta de instituciones

Endpoint: `GET /v1/instituciones`.

| ID | Prueba | Resultado obtenido | Estado |
|---|---|---|---|
| INST-11 | Consulta con `pagina=1&tamano=10` | HTTP 200, 10 instituciones | Aprobado |
| INST-12 | Verificación del total de registros | La respuesta indicó 22 instituciones | Aprobado |

### 3.5 Consulta de indicadores

Endpoint: `GET /v1/indicadores`.

| ID | Prueba | Resultado obtenido | Estado |
|---|---|---|---|
| INST-13 | Consulta general de indicadores con rol `admin_banco` | HTTP 200 | Aprobado |
| INST-14 | Verificación de indicadores de captación | Se devolvió información de donaciones y unidades generadas | Aprobado |
| INST-15 | Verificación de indicadores de inventario | Se devolvieron métricas de disponibilidad y estado del inventario | Aprobado |
| INST-16 | Verificación de indicadores de cobertura | Se devolvieron métricas territoriales e institucionales | Aprobado |

## 4. Resumen de resultados

| Estado | Cantidad |
|---|---:|
| Aprobados | 16 |
| Fallidos | 0 |
| Bloqueados | 0 |
| No ejecutados | 0 |
| **Total ejecutado** | **16** |

## 5. Defectos encontrados

No se identificaron defectos durante la ejecución de los casos registrados.

## 6. Conclusiones

El componente Institucional presentó un comportamiento satisfactorio en los escenarios evaluados. Se verificó su disponibilidad, el funcionamiento de los controles de autenticación y autorización y la recuperación de información mediante los endpoints de auditoría, territorios, instituciones e indicadores.

Los 16 casos ejecutados cumplieron con los resultados esperados. Estos resultados corresponden al alcance probado y no representan una validación completa de todas las funcionalidades del componente.

## 7. Pendientes y observaciones

- Ampliar las pruebas de autorización con otros roles.
- Validar filtros adicionales de territorios e instituciones.
- Probar diferentes periodos y criterios de agrupación en indicadores.
- Verificar la consistencia de los cálculos de indicadores frente a sus fuentes.
- No se ejecutaron operaciones POST o PUT.
- Las pruebas fueron manuales de API; no constituyen una validación E2E completa.

## 8. Evidencias

Se cuenta con las respuestas HTTP y los resultados obtenidos durante las consultas realizadas. Los tokens, identificadores internos y datos sensibles deben mantenerse redactados en las evidencias compartidas.

## 9. Revisión y aprobación

| Rol | Responsable | Estado |
|---|---|---|
| Responsable del informe | Tomás Silva | Elaborado |
| Revisado por | Pendiente | Pendiente |
| Aprobado por | Pendiente | Pendiente |