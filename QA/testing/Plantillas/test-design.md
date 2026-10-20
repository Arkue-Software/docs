# Diseño de Pruebas — Red Vital

## 1. Propósito

Este documento define el **diseño de pruebas de Red Vital**.

Su objetivo es especificar de manera estructurada:

- qué se prueba;
- por qué se prueba;
- qué requisito, escenario o riesgo origina la prueba;
- qué precondiciones se requieren;
- qué datos se utilizan;
- qué pasos deben ejecutarse;
- cuál es el resultado esperado;
- qué evidencia debe generarse;
- cuál es el estado de ejecución.

Este documento complementa:

- la metodología de calidad;
- el plan de pruebas;
- el reporte de pruebas.

---

# 2. Fuentes del diseño de pruebas

Los casos de prueba se derivan de:

- requisitos funcionales;
- requisitos no funcionales;
- historias de usuario;
- criterios de aceptación;
- escenarios de calidad;
- SAD;
- SDD;
- ADRs;
- DD;
- contratos;
- OpenAPI;
- infraestructura;
- riesgos;
- reglas de negocio.

---

# 3. Estructura de un caso de prueba

Cada caso debe documentar, como mínimo:

| Campo | Descripción |
|---|---|
| ID | Identificador único del caso. |
| Nombre | Nombre corto y descriptivo. |
| Tipo | Funcional, integración, seguridad, rendimiento, infraestructura, etc. |
| Fuente | Requisito, escenario, ADR, riesgo o criterio relacionado. |
| Prioridad | Alta, media o baja. |
| Precondiciones | Condiciones requeridas antes de ejecutar. |
| Datos | Datos de prueba utilizados. |
| Pasos | Secuencia de ejecución. |
| Resultado esperado | Comportamiento correcto esperado. |
| Evidencia | Evidencia que debe generarse. |
| Estado | Pendiente, aprobado, fallido o bloqueado. |

---

# 4. Convención de identificadores

Se recomienda utilizar la siguiente convención:

    TC-<AREA>-<NUMERO>

Ejemplos:

    TC-ID-001
    TC-INS-001
    TC-CAM-001
    TC-DON-001
    TC-NOT-001
    TC-SEC-001
    TC-INF-001
    TC-PERF-001

Donde:

- `ID` = Identidad;
- `INS` = Institucional;
- `CAM` = Campañas;
- `DON` = Donación;
- `NOT` = Notificaciones;
- `SEC` = Seguridad;
- `INF` = Infraestructura;
- `PERF` = Rendimiento.

---

# 5. Estados de los casos

Los casos pueden utilizar los siguientes estados:

| Estado | Significado |
|---|---|
| Pendiente | Aún no ejecutado. |
| Aprobado | Resultado obtenido igual al esperado. |
| Fallido | Resultado diferente al esperado. |
| Bloqueado | No puede ejecutarse por una dependencia. |
| No aplica | El caso dejó de aplicar por cambio de alcance. |

---

# 6. Prioridad

La prioridad de ejecución puede clasificarse como:

| Prioridad | Criterio |
|---|---|
| Alta | Función crítica, seguridad, disponibilidad o flujo principal. |
| Media | Función importante pero no bloqueante. |
| Baja | Función secundaria o validación complementaria. |

---

# 7. Casos funcionales — Identidad

## TC-ID-001 — Inicio de sesión válido

**Tipo:** Funcional / Seguridad  
**Prioridad:** Alta  
**Fuente:** Requisitos de autenticación  

### Precondiciones

- Usuario activo.
- Credenciales válidas.
- Servicio de Identidad disponible.

### Datos

- Correo válido.
- Credencial válida.

### Pasos

1. Ingresar credenciales válidas.
2. Enviar la solicitud de autenticación.
3. Esperar respuesta.

### Resultado esperado

- El sistema autentica al usuario.
- Se emite un token válido.
- El token contiene únicamente la información autorizada.
- No se expone información sensible adicional.

### Evidencia

- Respuesta de API.
- Código HTTP.
- Claims del token sin exponer secretos.

### Estado

Pendiente.

---

## TC-ID-002 — Inicio de sesión con credenciales inválidas

**Tipo:** Seguridad  
**Prioridad:** Alta  

### Precondiciones

- Usuario existente.

### Pasos

1. Enviar credenciales incorrectas.
2. Revisar respuesta.

### Resultado esperado

- Autenticación rechazada.
- No se emite token.
- La respuesta no revela información innecesaria sobre la cuenta.

### Evidencia

- Respuesta HTTP.
- Log sin datos sensibles.

### Estado

Pendiente.

---

## TC-ID-003 — Token expirado

**Tipo:** Seguridad  
**Prioridad:** Alta  

### Pasos

1. Utilizar un token expirado.
2. Invocar una ruta protegida.

### Resultado esperado

- Solicitud rechazada.
- No se ejecuta lógica de negocio.
- Se retorna código de autenticación correspondiente.

### Evidencia

- Código HTTP.
- Log correlacionado.

### Estado

Pendiente.

---

# 8. Casos funcionales — Institucional

## TC-INS-001 — Consulta de institución válida

**Tipo:** Funcional  
**Prioridad:** Alta  

### Precondiciones

- Institución registrada.
- Usuario autorizado.

### Pasos

1. Solicitar la institución por identificador.
2. Revisar respuesta.

### Resultado esperado

- Se retorna la institución correcta.
- Solo se muestran datos autorizados.

### Evidencia

- Respuesta de API.

### Estado

Pendiente.

---

## TC-INS-002 — Acceso fuera de jurisdicción

**Tipo:** Seguridad / Funcional  
**Prioridad:** Alta  

### Precondiciones

- Usuario autenticado.
- Institución fuera de su jurisdicción.

### Pasos

1. Solicitar información de la institución.
2. Revisar respuesta.

### Resultado esperado

- Acceso rechazado.
- No se entrega información restringida.
- El intento queda trazado cuando corresponda.

### Evidencia

- Respuesta.
- Registro de auditoría.

### Estado

Pendiente.

---

# 9. Casos funcionales — Campañas

## TC-CAM-001 — Consulta de campañas publicadas

**Tipo:** Funcional  
**Prioridad:** Alta  

### Precondiciones

- Existen campañas publicadas.

### Pasos

1. Consultar campañas disponibles.
2. Revisar respuesta.

### Resultado esperado

- Se retornan únicamente campañas visibles.
- La información coincide con el estado vigente.

### Evidencia

- Respuesta de API.

### Estado

Pendiente.

---

## TC-CAM-002 — Crear campaña con usuario autorizado

**Tipo:** Funcional / Seguridad  
**Prioridad:** Alta  

### Precondiciones

- Usuario con rol autorizado.
- Institución válida.

### Pasos

1. Enviar los datos requeridos.
2. Crear campaña.
3. Consultar la campaña creada.

### Resultado esperado

- Campaña creada correctamente.
- Se asigna identificador.
- Se registra estado inicial.
- Se conserva trazabilidad.

### Evidencia

- Respuesta.
- Registro persistido.
- Log o auditoría.

### Estado

Pendiente.

---

## TC-CAM-003 — Crear campaña sin permisos

**Tipo:** Seguridad  
**Prioridad:** Alta  

### Pasos

1. Autenticarse con usuario sin rol requerido.
2. Intentar crear campaña.

### Resultado esperado

- Operación rechazada.
- No se crea información.
- Se registra el intento cuando corresponda.

### Evidencia

- Respuesta HTTP.
- Validación de persistencia.

### Estado

Pendiente.

---

# 10. Casos funcionales — Donación

## TC-DON-001 — Registrar donación válida

**Tipo:** Funcional  
**Prioridad:** Alta  

### Precondiciones

- Usuario autorizado.
- Institución válida.
- Datos requeridos completos.

### Pasos

1. Enviar solicitud de registro.
2. Revisar respuesta.
3. Consultar el registro generado.

### Resultado esperado

- Donación creada correctamente.
- Se genera identificador único.
- Se guarda el estado inicial.
- La operación queda trazada.

### Evidencia

- Respuesta.
- Persistencia.
- Auditoría.

### Estado

Pendiente.

---

## TC-DON-002 — Transición válida de estado de unidad

**Tipo:** Funcional  
**Prioridad:** Alta  

### Precondiciones

- Unidad existente.
- Estado actual compatible.

### Pasos

1. Solicitar cambio de estado.
2. Consultar unidad.
3. Consultar historial.

### Resultado esperado

- Estado actualizado.
- Historial conserva estado anterior y nuevo.
- Se mantiene trazabilidad.

### Evidencia

- Respuesta.
- Registro de unidad.
- Registro de historial.

### Estado

Pendiente.

---

## TC-DON-003 — Transición inválida de estado

**Tipo:** Funcional negativa  
**Prioridad:** Alta  

### Pasos

1. Seleccionar una transición no permitida.
2. Intentar actualizar la unidad.

### Resultado esperado

- Operación rechazada.
- Estado original permanece intacto.
- No se genera historial incorrecto.

### Evidencia

- Respuesta.
- Consulta posterior.

### Estado

Pendiente.

---

# 11. Casos — Notificaciones

## TC-NOT-001 — Consumo de evento válido

**Tipo:** Integración  
**Prioridad:** Alta  

### Precondiciones

- Evento disponible.
- Servicio de Notificaciones activo.

### Pasos

1. Generar evento notificable.
2. Permitir consumo por Notificaciones.
3. Verificar procesamiento.

### Resultado esperado

- Evento procesado.
- Se crea la notificación correspondiente.
- No se requiere acceso directo a bases ajenas.

### Evidencia

- Logs.
- Registro de procesamiento.
- Resultado de entrega.

### Estado

Pendiente.

---

## TC-NOT-002 — Evento duplicado

**Tipo:** Integración / Resiliencia  
**Prioridad:** Alta  

### Pasos

1. Enviar dos veces el mismo identificador de evento.
2. Revisar resultado.

### Resultado esperado

- No se generan duplicados indebidos.
- El evento repetido se identifica como ya procesado.

### Evidencia

- Registro de eventos.
- Conteo de notificaciones.

### Estado

Pendiente.

---

# 12. Casos de integración

## TC-INT-001 — Gateway hacia servicio autorizado

**Tipo:** Integración  
**Prioridad:** Alta  

### Pasos

1. Invocar una ruta pública o protegida válida.
2. Gateway valida la solicitud.
3. Gateway enruta al servicio.
4. Servicio responde.

### Resultado esperado

- La solicitud llega únicamente al servicio correcto.
- Se conserva el correlation ID.
- El servicio valida nuevamente el token cuando aplique.

### Evidencia

- Logs de gateway.
- Logs del servicio.
- correlation ID.

### Estado

Pendiente.

---

## TC-INT-002 — Servicio intentando acceder a base ajena

**Tipo:** Infraestructura / Seguridad  
**Prioridad:** Alta  

### Pasos

1. Desde un servicio, intentar conectar con la base de otro contexto.

### Resultado esperado

- Conexión rechazada.
- El aislamiento de red o credenciales impide el acceso.

### Evidencia

- Resultado de conexión.
- Registros de red o base.

### Estado

Pendiente.

---

# 13. Casos de seguridad

## TC-SEC-001 — Ruta interna desde gateway

**Tipo:** Seguridad  
**Prioridad:** Alta  

### Pasos

1. Intentar invocar una ruta `/internal/` desde el exterior.

### Resultado esperado

- El gateway no enruta la operación.
- El servicio interno no recibe la petición.

### Evidencia

- Respuesta.
- Logs.

### Estado

Pendiente.

---

## TC-SEC-002 — Token de servicio en ruta de usuario

**Tipo:** Seguridad  
**Prioridad:** Alta  

### Pasos

1. Obtener token de servicio.
2. Invocar ruta reservada a usuarios.

### Resultado esperado

- Acceso rechazado.

### Evidencia

- Respuesta.
- Log de autorización.

### Estado

Pendiente.

---

## TC-SEC-003 — Usuario sin rol requerido

**Tipo:** Seguridad  
**Prioridad:** Alta  

### Pasos

1. Autenticarse con usuario válido.
2. Invocar operación de otro rol.

### Resultado esperado

- Operación rechazada.
- No se modifica información.

### Evidencia

- Respuesta.
- Persistencia sin cambios.

### Estado

Pendiente.

---

# 14. Casos de infraestructura

## TC-INF-001 — Puertos expuestos

**Tipo:** Infraestructura  
**Prioridad:** Alta  

### Objetivo

Verificar que únicamente los puertos permitidos estén expuestos externamente.

### Resultado esperado

- No existen puertos internos expuestos accidentalmente.
- Bases de datos no son accesibles desde el exterior.

### Evidencia

- Resultado de inspección de red.
- Configuración de contenedores o firewall.

### Estado

Pendiente.

---

## TC-INF-002 — Separación QA / Producción

**Tipo:** Infraestructura / Seguridad  
**Prioridad:** Alta  

### Pasos

1. Intentar conexión directa desde componentes QA hacia recursos exclusivos de Producción.
2. Repetir en dirección inversa.

### Resultado esperado

- Las comunicaciones no autorizadas son rechazadas.

### Evidencia

- Pruebas de conectividad.
- Reglas de firewall.

### Estado

Pendiente.

---

## TC-INF-003 — Health checks

**Tipo:** Infraestructura  
**Prioridad:** Alta  

### Pasos

1. Consultar health check de cada componente.
2. Simular una dependencia no disponible cuando sea posible.

### Resultado esperado

- El componente reporta salud correctamente.
- Los fallos de dependencias esenciales son detectados.

### Evidencia

- Respuestas de health check.
- Logs.

### Estado

Pendiente.

---

# 15. Casos de respaldo y recuperación

## TC-INF-004 — Backup de base

**Tipo:** Recuperación  
**Prioridad:** Alta  

### Pasos

1. Ejecutar respaldo.
2. Validar archivo.
3. Validar checksum.
4. Registrar versión.

### Resultado esperado

- Respaldo generado correctamente.
- Integridad verificable.

### Evidencia

- Archivo de backup.
- Checksum.
- Registro de ejecución.

### Estado

Pendiente.

---

## TC-INF-005 — Restauración de base

**Tipo:** Recuperación  
**Prioridad:** Alta  

### Pasos

1. Seleccionar respaldo válido.
2. Restaurar la base correspondiente.
3. Ejecutar migraciones necesarias.
4. Validar integridad.
5. Arrancar servicio.

### Resultado esperado

- Datos restaurados.
- Servicio saludable.
- Otras bases no afectadas.

### Evidencia

- Logs de restore.
- Health check.
- Conteo de registros.

### Estado

Pendiente.

---

# 16. Casos de rendimiento

## TC-PERF-001 — Carga nominal

**Tipo:** Rendimiento  
**Prioridad:** Alta  

### Objetivo

Verificar el comportamiento del sistema bajo carga esperada.

### Medidas

- latencia;
- throughput;
- errores;
- CPU;
- memoria;
- conexiones.

### Resultado esperado

Los valores deben mantenerse dentro de los umbrales definidos por los escenarios de calidad.

### Evidencia

- Prometheus.
- Grafana.
- Resultado de herramienta de carga.

### Estado

Pendiente.

---

## TC-PERF-002 — Sobrecarga

**Tipo:** Rendimiento / Resiliencia  
**Prioridad:** Media  

### Objetivo

Observar el comportamiento cuando la carga supera la capacidad nominal.

### Resultado esperado

- El sistema degrada de manera controlada.
- No existe corrupción de datos.
- Los errores son observables.
- Los componentes recuperan estabilidad al retirar la carga.

### Evidencia

- Métricas.
- Logs.
- Reporte de carga.

### Estado

Pendiente.

---

# 17. Casos de observabilidad

## TC-OBS-001 — Correlation ID

**Tipo:** Observabilidad  
**Prioridad:** Alta  

### Pasos

1. Ejecutar flujo que atraviese varios componentes.
2. Consultar logs.

### Resultado esperado

- El mismo identificador puede rastrearse durante el flujo.

### Evidencia

- Logs de gateway.
- Logs de servicios.

### Estado

Pendiente.

---

## TC-OBS-002 — Datos sensibles en logs

**Tipo:** Seguridad / Observabilidad  
**Prioridad:** Alta  

### Pasos

1. Ejecutar operaciones con información personal.
2. Revisar logs.

### Resultado esperado

No aparecen:

- tokens;
- contraseñas;
- cookies;
- información sensible innecesaria;
- cuerpos completos con datos personales.

### Evidencia

- Extracto controlado de logs.

### Estado

Pendiente.

---

# 18. Casos de despliegue

## TC-DEP-001 — Deploy a QA

**Tipo:** Infraestructura / DevOps  
**Prioridad:** Alta  

### Pasos

1. Integrar cambio permitido.
2. Ejecutar pipeline.
3. Desplegar en QA.
4. Ejecutar health checks.
5. Ejecutar smoke test.

### Resultado esperado

- Despliegue finaliza correctamente.
- Componentes quedan saludables.
- Smoke test aprobado.

### Evidencia

- Pipeline.
- Logs.
- health checks.

### Estado

Pendiente.

---

## TC-DEP-002 — Deploy a Producción sin aprobación

**Tipo:** Seguridad / Proceso  
**Prioridad:** Alta  

### Pasos

1. Intentar ejecutar despliegue sin aprobación requerida.

### Resultado esperado

- Pipeline bloqueado.
- Producción no cambia.

### Evidencia

- Resultado del workflow.

### Estado

Pendiente.

---

# 19. Casos de smoke test

Después de un despliegue deben ejecutarse pruebas rápidas sobre funciones esenciales.

Como mínimo:

- autenticación;
- consulta pública;
- consulta de datos autorizada;
- conectividad gateway → servicios;
- persistencia;
- health checks.

---

# 20. Datos de prueba

Los datos deben ser:

- sintéticos;
- controlados;
- reproducibles;
- no sensibles.

Los conjuntos deben cubrir:

- casos válidos;
- casos inválidos;
- límites;
- diferentes roles;
- diferentes jurisdicciones;
- diferentes estados.

---

# 21. Matriz de trazabilidad

Debe mantenerse una matriz entre pruebas y fuentes.

Ejemplo:

| Caso | Requisito / Fuente | Tipo | Prioridad |
|---|---|---|---|
| TC-ID-001 | Autenticación | Funcional | Alta |
| TC-SEC-001 | SAD / rutas internas | Seguridad | Alta |
| TC-INF-002 | Infraestructura | Seguridad | Alta |
| TC-PERF-001 | Escenario de rendimiento | Rendimiento | Alta |

La matriz definitiva debe utilizar los identificadores reales de requisitos y escenarios.

---

# 22. Evidencias

Las evidencias pueden almacenarse o enlazarse desde:

- GitHub Actions;
- Issues;
- reportes;
- capturas;
- métricas;
- logs;
- resultados de API;
- artefactos de prueba.

Cada evidencia debe permitir identificar:

- caso;
- fecha;
- ambiente;
- versión evaluada.

---

# 23. Gestión de fallos

Cuando un caso falla:

1. se registra el resultado;
2. se crea o relaciona un defecto;
3. se define severidad;
4. se corrige;
5. se ejecuta nuevamente;
6. se registra el nuevo resultado.

Un caso no debe marcarse como aprobado sin una ejecución exitosa.

---

# 24. Relación con el reporte de pruebas

Este documento define qué debe probarse.

Los resultados reales se registran en:

[Reporte de Pruebas](./test-report.md)

La relación es:

    Test Design
         ↓
      Ejecución
         ↓
      Test Report

---

# 25. Pendientes

- [ ] Relacionar cada caso con IDs reales del SRS.
- [ ] Relacionar casos de calidad con escenarios arquitectónicos.
- [ ] Incorporar contratos OpenAPI definitivos.
- [ ] Actualizar casos de infraestructura con la matriz final de 7 VMs.
- [ ] Incorporar pruebas específicas del proveedor OAuth.
- [ ] Definir umbrales definitivos de rendimiento.
- [ ] Completar casos según backlog vigente.
- [ ] Incorporar evidencias después de las ejecuciones.

---

# 26. Regla de mantenimiento

Este documento debe actualizarse cuando:

- se cree un requisito;
- cambie un flujo;
- cambie un contrato;
- cambie una regla de negocio;
- cambie un escenario de calidad;
- cambie infraestructura;
- aparezca un nuevo riesgo;
- se detecte un defecto que requiera cobertura permanente.

Los casos deben representar el comportamiento vigente del sistema y no una versión histórica.