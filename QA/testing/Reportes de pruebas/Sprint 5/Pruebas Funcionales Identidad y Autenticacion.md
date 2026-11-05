# Informe de pruebas - Servicio de Identidad

## Control del documento

| Campo | Información |
|---|---|
| Código del documento | TST-IDENTIDAD |
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
| 0.1 | 05/11/2026 | Tomás Silva | Registro de las verificaciones de autenticación realizadas sobre el servicio de Identidad. |

---

## 1. Resumen ejecutivo

Se realizaron verificaciones manuales sobre el servicio de Identidad de RedVital, enfocadas en comprobar el inicio de sesión mediante el endpoint de sesiones y la emisión de tokens de acceso para cuentas de prueba existentes.

Se verificó el inicio de sesión de una cuenta con rol auditor y otra con rol administrador de banco. Ambas solicitudes fueron respondidas satisfactoriamente con HTTP 200 y devolvieron la información del token de acceso.

## 2. Información de ejecución

| Campo | Información |
|---|---|
| Componente | Identidad |
| Tipo de prueba | Prueba manual de API |
| Alcance | Inicio de sesión y emisión de token |
| Endpoint | `POST /v1/sesiones` |
| Ambiente | Pruebas |
| Fecha | 05/11/2026 |
| Ejecutor | Tomás Silva |
| Dependencias | Base de datos de Identidad |
| Tipo de dependencia | Real, dentro del ambiente de pruebas |

## 3. Preparación del escenario

Se identificó el endpoint de sesiones y la estructura de la solicitud, que utiliza los campos de correo y credencial.

Se utilizaron cuentas de prueba existentes. Las credenciales y los tokens generados se mantuvieron únicamente en el entorno de ejecución y no se incluyen en este informe.

No se modificaron usuarios, roles ni permisos.

## 4. Ejecución de pruebas

### 4.1 Inicio de sesión con rol auditor

**Descripción:** Se realizó una solicitud al endpoint `POST /v1/sesiones` utilizando una cuenta de prueba con rol auditor.

**Resultado esperado:** El servicio debe validar las credenciales y devolver un token de acceso cuando el inicio de sesión sea exitoso.

**Resultado obtenido:** La solicitud respondió con HTTP 200 y devolvió la información del token de acceso, el tipo de token y su expiración.

**Veredicto:** Aprobado.

### 4.2 Inicio de sesión con rol administrador de banco

**Descripción:** Se realizó una solicitud al endpoint `POST /v1/sesiones` utilizando una cuenta de prueba con rol `admin_banco`.

**Resultado esperado:** El servicio debe autenticar correctamente la cuenta y emitir la información del token de acceso.

**Resultado obtenido:** La solicitud respondió con HTTP 200 y devolvió la información correspondiente al token de acceso.

**Veredicto:** Aprobado.

## 5. Resumen de resultados

| ID | Prueba | Resultado obtenido | Estado |
|---|---|---|---|
| ID-01 | Inicio de sesión de cuenta auditor | HTTP 200 y token de acceso | Aprobado |
| ID-02 | Inicio de sesión de cuenta admin_banco | HTTP 200 y token de acceso | Aprobado |

**Total de casos ejecutados:** 2  
**Casos aprobados:** 2  
**Casos fallidos:** 0

## 6. Defectos encontrados

No se identificaron defectos durante las solicitudes de inicio de sesión realizadas.

## 7. Conclusiones

El servicio de Identidad permitió autenticar correctamente las dos cuentas de prueba utilizadas y emitir sus respectivos tokens de acceso. Las respuestas obtenidas cumplieron con el comportamiento esperado para los escenarios ejecutados.

## 8. Pendientes y observaciones

- Ampliar las pruebas con credenciales inválidas y otros escenarios de autenticación.
- Verificar expiración, renovación y revocación de tokens en pruebas específicas.
- Validar otros flujos de sesión cuando se encuentren dentro del alcance de QA.
- No se modificaron usuarios, credenciales ni roles.
- Este informe registra las pruebas directas de inicio de sesión. Las verificaciones de permisos y auditoría a nivel de base de datos se documentan por separado en `TST-BASES-DE-DATOS.md`.

## 9. Evidencias

Se cuenta con las respuestas HTTP 200 de las dos solicitudes de inicio de sesión. Los correos de prueba pueden identificarse por su rol, pero las credenciales y los tokens deben permanecer redactados.

## 10. Revisión y aprobación

| Rol | Responsable | Estado |
|---|---|---|
| Responsable del informe | Tomás Silva | Elaborado |
| Revisado por | Pendiente | Pendiente |
| Aprobado por | Pendiente | Pendiente |