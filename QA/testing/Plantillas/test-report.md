# Reporte de Pruebas — Red Vital

## 1. Propósito

Este documento consolida los resultados de las pruebas ejecutadas sobre **Red Vital**.

Su objetivo es registrar:

- qué versión fue evaluada;
- qué ambiente se utilizó;
- qué casos fueron ejecutados;
- cuáles fueron aprobados;
- cuáles fallaron;
- qué defectos fueron encontrados;
- qué evidencias existen;
- qué riesgos permanecen abiertos;
- si la versión cumple los criterios de salida definidos.

Este documento no define los casos de prueba.

Los casos se mantienen en:

[Test Design](./test-design.md)

La estrategia general se mantiene en:

[Test Plan](./test-plan.md)

---

# 2. Identificación de la ejecución

| Campo | Valor |
|---|---|
| Proyecto | Red Vital |
| Ciclo / Sprint | Pendiente |
| Versión evaluada | Pendiente |
| Fecha de inicio | Pendiente |
| Fecha de cierre | Pendiente |
| Ambiente | QA / Producción / Desarrollo |
| Responsable QA | Pendiente |
| Commit / Tag | Pendiente |
| Build / Imagen | Pendiente |

---

# 3. Objetivo de la campaña

La campaña de pruebas tiene como objetivo verificar que la versión evaluada:

- cumpla los requisitos previstos;
- satisfaga los criterios de aceptación;
- mantenga las decisiones arquitectónicas vigentes;
- preserve seguridad e integridad;
- opere correctamente en infraestructura;
- pueda desplegarse y recuperarse de forma controlada.

---

# 4. Alcance ejecutado

Registrar los componentes evaluados.

| Componente | Evaluado | Observaciones |
|---|---|---|
| Frontend | Pendiente | |
| API Gateway | Pendiente | |
| Identidad | Pendiente | |
| Institucional | Pendiente | |
| Campañas | Pendiente | |
| Donación | Pendiente | |
| Notificaciones | Pendiente | |
| Persistencia | Pendiente | |
| Infraestructura | Pendiente | |
| Observabilidad | Pendiente | |
| Pipeline | Pendiente | |

---

# 5. Resumen ejecutivo

Esta sección debe presentar una visión general de la ejecución.

Ejemplo de estructura:

| Métrica | Resultado |
|---|---:|
| Casos planificados | Pendiente |
| Casos ejecutados | Pendiente |
| Casos aprobados | Pendiente |
| Casos fallidos | Pendiente |
| Casos bloqueados | Pendiente |
| Casos no ejecutados | Pendiente |
| Defectos críticos | Pendiente |
| Defectos altos | Pendiente |
| Defectos medios | Pendiente |
| Defectos bajos | Pendiente |

---

# 6. Resultado general

El resultado global debe clasificarse como uno de los siguientes:

- **Aprobado**
- **Aprobado con observaciones**
- **No aprobado**
- **Bloqueado**

**Resultado actual:** Pendiente.

La decisión debe justificarse con base en:

- casos ejecutados;
- defectos abiertos;
- criterios de salida;
- riesgos residuales.

---

# 7. Resultados por caso de prueba

| ID | Caso | Tipo | Resultado | Evidencia | Defecto asociado |
|---|---|---|---|---|---|
| TC-ID-001 | Inicio de sesión válido | Funcional | Pendiente | Pendiente | — |
| TC-ID-002 | Credenciales inválidas | Seguridad | Pendiente | Pendiente | — |
| TC-ID-003 | Token expirado | Seguridad | Pendiente | Pendiente | — |
| TC-INS-001 | Consulta de institución | Funcional | Pendiente | Pendiente | — |
| TC-INS-002 | Acceso fuera de jurisdicción | Seguridad | Pendiente | Pendiente | — |
| TC-CAM-001 | Consulta de campañas | Funcional | Pendiente | Pendiente | — |
| TC-CAM-002 | Crear campaña autorizada | Funcional | Pendiente | Pendiente | — |
| TC-CAM-003 | Crear campaña sin permisos | Seguridad | Pendiente | Pendiente | — |
| TC-DON-001 | Registrar donación | Funcional | Pendiente | Pendiente | — |
| TC-DON-002 | Transición válida de unidad | Funcional | Pendiente | Pendiente | — |
| TC-DON-003 | Transición inválida | Negativa | Pendiente | Pendiente | — |
| TC-NOT-001 | Consumo de evento | Integración | Pendiente | Pendiente | — |
| TC-NOT-002 | Evento duplicado | Integración | Pendiente | Pendiente | — |
| TC-INT-001 | Gateway hacia servicio | Integración | Pendiente | Pendiente | — |
| TC-INT-002 | Acceso a base ajena | Seguridad | Pendiente | Pendiente | — |
| TC-SEC-001 | Ruta interna desde gateway | Seguridad | Pendiente | Pendiente | — |
| TC-SEC-002 | Token de servicio en ruta de usuario | Seguridad | Pendiente | Pendiente | — |
| TC-SEC-003 | Usuario sin rol | Seguridad | Pendiente | Pendiente | — |
| TC-INF-001 | Puertos expuestos | Infraestructura | Pendiente | Pendiente | — |
| TC-INF-002 | Separación QA / Producción | Infraestructura | Pendiente | Pendiente | — |
| TC-INF-003 | Health checks | Infraestructura | Pendiente | Pendiente | — |
| TC-INF-004 | Backup | Recuperación | Pendiente | Pendiente | — |
| TC-INF-005 | Restore | Recuperación | Pendiente | Pendiente | — |
| TC-PERF-001 | Carga nominal | Rendimiento | Pendiente | Pendiente | — |
| TC-PERF-002 | Sobrecarga | Rendimiento | Pendiente | Pendiente | — |
| TC-OBS-001 | Correlation ID | Observabilidad | Pendiente | Pendiente | — |
| TC-OBS-002 | Datos sensibles en logs | Seguridad | Pendiente | Pendiente | — |
| TC-DEP-001 | Deploy a QA | DevOps | Pendiente | Pendiente | — |
| TC-DEP-002 | Deploy sin aprobación | DevOps | Pendiente | Pendiente | — |

---

# 8. Resultados por tipo de prueba

## 8.1 Funcionales

Registrar:

- casos ejecutados;
- aprobados;
- fallidos;
- bloqueados;
- observaciones relevantes.

**Estado:** Pendiente.

---

## 8.2 Integración

Registrar resultados de:

- gateway → servicios;
- servicio → servicio;
- servicio → base propia;
- contratos;
- eventos;
- autenticación interna.

**Estado:** Pendiente.

---

## 8.3 Seguridad

Registrar resultados relacionados con:

- autenticación;
- autorización;
- roles;
- jurisdicción;
- rutas internas;
- aislamiento;
- secretos;
- exposición de puertos;
- rate limiting.

**Estado:** Pendiente.

---

## 8.4 Infraestructura

Registrar resultados de:

- redes;
- puertos;
- firewall;
- VMs;
- contenedores;
- health checks;
- separación QA / Producción;
- accesos.

**Estado:** Pendiente.

---

## 8.5 Rendimiento

Registrar:

- latencia;
- throughput;
- tasa de error;
- CPU;
- memoria;
- conexiones;
- comportamiento bajo sobrecarga.

**Estado:** Pendiente.

---

## 8.6 Recuperación

Registrar:

- backup;
- restore;
- tiempo de recuperación;
- pérdida máxima de datos;
- independencia entre bases.

**Estado:** Pendiente.

---

# 9. Defectos encontrados

| ID | Descripción | Severidad | Caso asociado | Estado | Responsable |
|---|---|---|---|---|---|
| Pendiente | Pendiente | Pendiente | Pendiente | Pendiente | Pendiente |

Los defectos deben mantenerse sincronizados con GitHub Issues.

---

# 10. Defectos críticos

Esta sección debe listar cualquier defecto crítico encontrado durante la campaña.

Un defecto crítico puede incluir:

- pérdida de datos;
- acceso no autorizado;
- fallo de autenticación general;
- exposición de información sensible;
- indisponibilidad total;
- corrupción de persistencia;
- imposibilidad de desplegar;
- imposibilidad de restaurar.

**Estado actual:** Pendiente.

---

# 11. Evidencias

Las evidencias deben permitir reproducir o verificar el resultado.

Pueden incluir:

- logs;
- capturas;
- métricas;
- dashboards;
- respuestas de API;
- artefactos de GitHub Actions;
- resultados de scripts;
- archivos de backup;
- checksums;
- reportes de carga.

Cada evidencia debe asociarse con:

- caso;
- versión;
- ambiente;
- fecha.

---

# 12. Resultados de infraestructura

Registrar la validación de la topología vigente.

| Elemento | Resultado | Evidencia |
|---|---|---|
| VM 1 — Tools | Pendiente | |
| VM 2 — QA Datos | Pendiente | |
| VM 3 — QA S+I | Pendiente | |
| VM 4 — QA Apps | Pendiente | |
| VM 5 — Prod Datos | Pendiente | |
| VM 6 — Prod S+I | Pendiente | |
| VM 7 — Prod Apps | Pendiente | |
| Separación QA / Prod | Pendiente | |
| Firewall | Pendiente | |
| Puertos | Pendiente | |
| Backups | Pendiente | |
| Monitoreo | Pendiente | |

---

# 13. Resultados de observabilidad

Registrar evidencia de:

- métricas disponibles;
- dashboards;
- logs;
- correlation IDs;
- alertas;
- ausencia de datos sensibles.

| Control | Resultado | Evidencia |
|---|---|---|
| Prometheus recibe métricas | Pendiente | |
| Grafana presenta dashboards | Pendiente | |
| Correlation ID propagado | Pendiente | |
| Logs estructurados | Pendiente | |
| Sin tokens en logs | Pendiente | |
| Sin datos sensibles innecesarios | Pendiente | |

---

# 14. Resultados de rendimiento

| Métrica | Resultado | Umbral | Cumple |
|---|---:|---:|---|
| Latencia p50 | Pendiente | Pendiente | Pendiente |
| Latencia p95 | Pendiente | Pendiente | Pendiente |
| Latencia p99 | Pendiente | Pendiente | Pendiente |
| Throughput | Pendiente | Pendiente | Pendiente |
| Error rate | Pendiente | Pendiente | Pendiente |
| CPU máxima | Pendiente | Pendiente | Pendiente |
| Memoria máxima | Pendiente | Pendiente | Pendiente |

Los umbrales deben corresponder a los escenarios de calidad vigentes.

---

# 15. Resultados de backup

Registrar por base:

| Base | Backup generado | Checksum válido | Evidencia |
|---|---|---|---|
| Identidad | Pendiente | Pendiente | |
| Institucional | Pendiente | Pendiente | |
| Campañas | Pendiente | Pendiente | |
| Donación | Pendiente | Pendiente | |

---

# 16. Resultados de restore

| Base | Restore ejecutado | Integridad validada | Servicio saludable | Tiempo |
|---|---|---|---|---|
| Identidad | Pendiente | Pendiente | Pendiente | Pendiente |
| Institucional | Pendiente | Pendiente | Pendiente | Pendiente |
| Campañas | Pendiente | Pendiente | Pendiente | Pendiente |
| Donación | Pendiente | Pendiente | Pendiente | Pendiente |

---

# 17. Resultados de despliegue

Registrar:

- versión desplegada;
- pipeline;
- ambiente;
- migraciones;
- health checks;
- smoke test;
- reversión cuando aplique.

| Ambiente | Deploy | Health Checks | Smoke Test | Resultado |
|---|---|---|---|---|
| QA | Pendiente | Pendiente | Pendiente | Pendiente |
| Producción | Pendiente | Pendiente | Pendiente | Pendiente |

---

# 18. Pruebas bloqueadas

Registrar pruebas que no pudieron ejecutarse.

| Caso | Motivo | Dependencia | Acción |
|---|---|---|---|
| Pendiente | Pendiente | Pendiente | Pendiente |

Un caso bloqueado no debe considerarse aprobado.

---

# 19. Pruebas no ejecutadas

Registrar explícitamente cualquier prueba planificada que no haya sido ejecutada.

Esto permite diferenciar:

- aprobado;
- fallido;
- bloqueado;
- no ejecutado.

---

# 20. Riesgos residuales

Después de la ejecución deben registrarse los riesgos que permanecen abiertos.

Ejemplos:

- funcionalidad no probada;
- componente incompleto;
- prueba manual pendiente;
- falta de automatización;
- dependencia externa;
- riesgo de infraestructura;
- backup sin copia externa;
- VM Tools como punto único de falla.

| Riesgo | Impacto | Mitigación | Estado |
|---|---|---|---|
| Pendiente | Pendiente | Pendiente | Pendiente |

---

# 21. Cumplimiento de criterios de salida

| Criterio | Cumple |
|---|---|
| Casos planificados ejecutados | Pendiente |
| Casos críticos aprobados | Pendiente |
| Sin defectos críticos abiertos | Pendiente |
| Defectos restantes registrados | Pendiente |
| Evidencias disponibles | Pendiente |
| Riesgos residuales documentados | Pendiente |

---

# 22. Decisión de liberación

Con base en los resultados, QA debe emitir una recomendación.

Opciones:

### Aprobado para continuar

La versión cumple los criterios establecidos.

### Aprobado con observaciones

Puede continuar, pero existen riesgos o defectos conocidos aceptados.

### No aprobado

Existen defectos o incumplimientos que impiden continuar.

### Bloqueado

No existe evidencia suficiente para emitir una decisión.

**Decisión actual:** Pendiente.

---

# 23. Conclusiones

Esta sección debe resumir:

- nivel general de calidad observado;
- principales fortalezas;
- principales defectos;
- riesgos;
- acciones recomendadas;
- decisión final.

**Conclusión actual:** Pendiente de ejecución.

---

# 24. Recomendaciones

Las recomendaciones pueden incluir:

- corregir defectos;
- aumentar cobertura;
- automatizar pruebas;
- mejorar observabilidad;
- ajustar infraestructura;
- revisar contratos;
- repetir pruebas de carga;
- repetir restore;
- mejorar documentación.

---

# 25. Trazabilidad

El reporte debe permitir seguir la cadena:

    Requisito / Escenario
             ↓
         Caso de prueba
             ↓
          Ejecución
             ↓
          Evidencia
             ↓
          Defecto
             ↓
          Corrección
             ↓
          Reejecución

---

# 26. Relación con otros artefactos

- [Metodología de Calidad](./quality-methodology.md)
- [Plan de Pruebas](./test-plan.md)
- [Diseño de Pruebas](./test-design.md)
- [Quality Attributes](../architecture/quality-attributes/README.md)
- [Infrastructure](../infrastructure/README.md)
- [Requirements](../requirements/)
- [Integration](../integration/)

---

# 27. Pendientes

- [ ] Ejecutar campaña de pruebas.
- [ ] Registrar versión evaluada.
- [ ] Completar resultados por caso.
- [ ] Adjuntar evidencias.
- [ ] Registrar defectos.
- [ ] Completar métricas de rendimiento.
- [ ] Registrar pruebas de backup.
- [ ] Registrar pruebas de restore.
- [ ] Registrar resultados de despliegue.
- [ ] Documentar riesgos residuales.
- [ ] Emitir decisión final de QA.

---

# 28. Regla de mantenimiento

Este reporte debe actualizarse durante y después de cada campaña de pruebas.

No deben registrarse resultados como aprobados sin evidencia.

Cada versión relevante puede generar un nuevo ciclo de reporte o una nueva sección identificada por:

- sprint;
- versión;
- fecha;
- ambiente.

El reporte debe reflejar resultados reales y no resultados esperados.