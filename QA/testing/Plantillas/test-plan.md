# Plan de Pruebas — Red Vital

## 1. Propósito

Este documento define el **Plan de Pruebas de Red Vital**.

Su objetivo es establecer:

- qué se va a probar;
- qué queda fuera del alcance;
- qué tipos de prueba se ejecutarán;
- en qué ambientes;
- con qué responsabilidades;
- bajo qué criterios de entrada y salida;
- cómo se gestionarán defectos;
- qué evidencias deben generarse;
- qué riesgos pueden afectar la ejecución.

Este documento complementa:

- [Metodología de Calidad](./quality-methodology.md)
- [Diseño de Pruebas](./test-design.md)
- [Reporte de Pruebas](./test-report.md)

---

# 2. Objetivo general

Verificar que Red Vital cumpla los requisitos funcionales y no funcionales definidos, mantenga las decisiones arquitectónicas vigentes y opere correctamente en los ambientes previstos.

---

# 3. Objetivos específicos

El plan busca verificar:

1. funcionalidades principales;
2. autenticación y autorización;
3. reglas de negocio;
4. comunicación entre servicios;
5. contratos;
6. persistencia;
7. separación entre dominios;
8. seguridad;
9. infraestructura;
10. observabilidad;
11. rendimiento;
12. respaldo y recuperación;
13. despliegue;
14. trazabilidad.

---

# 4. Alcance

El plan cubre pruebas sobre:

- Identidad;
- Institucional;
- Campañas;
- Donación;
- Notificaciones;
- frontend;
- API Gateway;
- integración entre servicios;
- persistencia;
- infraestructura;
- observabilidad;
- pipelines;
- despliegues.

También cubre la validación de:

- roles;
- jurisdicción;
- rutas;
- contratos;
- eventos;
- bases de datos;
- health checks;
- logs;
- métricas;
- backups;
- restore.

---

# 5. Fuera de alcance

No forman parte del alcance de pruebas, salvo que el proyecto lo incorpore posteriormente:

- pagos;
- check-in;
- equipaje;
- selección de sillas;
- cancelaciones no contempladas;
- información clínica fuera del alcance;
- datos reales de producción no autorizados;
- integraciones externas no implementadas.

---

# 6. Estrategia de pruebas

La estrategia combina varios niveles:

- pruebas unitarias;
- pruebas de integración;
- pruebas de contrato;
- pruebas funcionales;
- pruebas end-to-end;
- pruebas de seguridad;
- pruebas de infraestructura;
- pruebas de rendimiento;
- pruebas de recuperación;
- pruebas de despliegue.

La selección de pruebas depende del riesgo y del impacto de la funcionalidad.

---

# 7. Prioridad de pruebas

Las pruebas se priorizan según criticidad.

## Prioridad alta

Incluye:

- autenticación;
- autorización;
- persistencia;
- flujos principales;
- aislamiento entre servicios;
- rutas internas;
- despliegue;
- restore;
- seguridad.

## Prioridad media

Incluye:

- funcionalidades secundarias;
- errores controlados;
- rendimiento complementario;
- degradación.

## Prioridad baja

Incluye:

- validaciones menores;
- aspectos no críticos;
- mejoras visuales.

---

# 8. Ambientes

## 8.1 Desarrollo

Se utiliza para:

- pruebas unitarias;
- validaciones locales;
- integración inicial;
- depuración.

Características:

- datos sintéticos;
- contenedores locales;
- configuración de desarrollo.

---

## 8.2 QA

Es el ambiente principal de validación.

Actualmente utiliza:

- VM 2 — Persistencia;
- VM 3 — S+I;
- VM 4 — Aplicaciones.

En QA se ejecutan:

- pruebas funcionales;
- integración;
- seguridad;
- contratos;
- observabilidad;
- rendimiento;
- backup;
- restore;
- despliegue;
- infraestructura.

---

## 8.3 Producción

Producción utiliza:

- VM 5 — Persistencia;
- VM 6 — S+I;
- VM 7 — Aplicaciones.

Las pruebas directas deben limitarse principalmente a:

- smoke tests;
- health checks;
- verificación de despliegue;
- monitoreo.

No se deben ejecutar pruebas destructivas.

---

# 9. Datos de prueba

Los datos utilizados deben ser:

- sintéticos;
- controlados;
- reproducibles;
- no sensibles.

Se deben preparar datos para:

- usuarios válidos;
- usuarios inválidos;
- diferentes roles;
- diferentes jurisdicciones;
- instituciones;
- campañas;
- donaciones;
- unidades;
- estados;
- transferencias;
- notificaciones.

---

# 10. Tipos de prueba

## 10.1 Unitarias

Responsabilidad principal de Desarrollo.

Verifican:

- reglas de negocio;
- validadores;
- funciones;
- clases;
- transformaciones.

---

## 10.2 Integración

Verifican:

- gateway → servicio;
- servicio → servicio;
- servicio → base propia;
- eventos;
- contratos;
- autenticación interna.

---

## 10.3 Contratos

Verifican compatibilidad con:

- OpenAPI;
- schemas;
- payloads;
- eventos;
- códigos de respuesta.

---

## 10.4 Funcionales

Validan requisitos y criterios de aceptación.

---

## 10.5 End-to-end

Validan flujos completos.

Ejemplo:

    Usuario
       ↓
    Frontend
       ↓
    Gateway
       ↓
    Servicio
       ↓
    Persistencia

---

## 10.6 Seguridad

Verifican:

- autenticación;
- autorización;
- roles;
- jurisdicción;
- acceso a rutas;
- acceso a bases;
- secretos;
- exposición de puertos;
- rate limiting;
- aislamiento.

---

## 10.7 Infraestructura

Validan:

- redes;
- firewalls;
- puertos;
- VMs;
- contenedores;
- health checks;
- aislamiento QA / Producción;
- conectividad;
- observabilidad.

---

## 10.8 Rendimiento

Validan:

- latencia;
- throughput;
- errores;
- CPU;
- memoria;
- conexiones;
- degradación bajo carga.

---

## 10.9 Recuperación

Validan:

- backups;
- restore;
- recuperación de base;
- reinicio de servicios;
- reversión de despliegue.

---

# 11. Criterios de entrada

Una campaña de pruebas puede comenzar cuando:

- existe una versión desplegable;
- el ambiente está disponible;
- los servicios requeridos están activos;
- los health checks responden;
- los datos de prueba están preparados;
- los casos están definidos;
- las dependencias necesarias están disponibles;
- las migraciones fueron ejecutadas correctamente.

---

# 12. Criterios de salida

Una campaña puede considerarse terminada cuando:

- se ejecutaron los casos planificados;
- las pruebas críticas están aprobadas;
- no existen defectos críticos abiertos;
- los defectos restantes están registrados;
- existe evidencia;
- los resultados están documentados;
- los riesgos residuales están identificados.

---

# 13. Criterios de suspensión

La ejecución puede suspenderse cuando:

- el ambiente está inestable;
- existe una falla general de infraestructura;
- las bases no están disponibles;
- el build no es ejecutable;
- las migraciones fallan;
- existen defectos críticos que impiden continuar;
- la versión evaluada cambia durante la campaña.

---

# 14. Criterios de reanudación

Las pruebas se reanudan cuando:

- se corrige el bloqueo;
- el ambiente vuelve a estar estable;
- se despliega una versión válida;
- las dependencias están disponibles;
- se confirma la integridad de los datos de prueba.

---

# 15. Responsabilidades

## QA / Tester

Responsable de:

- plan de pruebas;
- diseño de pruebas;
- ejecución;
- evidencias;
- registro de defectos;
- reporte.

## Desarrollo

Responsable de:

- pruebas unitarias;
- corrección de defectos;
- soporte a integración;
- revisión de errores.

## Arquitectura

Responsable de:

- escenarios de calidad;
- restricciones;
- riesgos;
- decisiones verificables.

## DevOps

Responsable de:

- ambientes;
- pipelines;
- despliegues;
- monitoreo;
- infraestructura;
- soporte a backups y restore.

---

# 16. Gestión de defectos

Los defectos deben registrarse mediante GitHub Issues.

Cada defecto debe incluir:

- título;
- descripción;
- caso asociado;
- resultado esperado;
- resultado obtenido;
- severidad;
- evidencia;
- ambiente;
- versión;
- responsable;
- estado.

---

# 17. Severidad

| Severidad | Descripción |
|---|---|
| Crítica | Impide continuar o compromete seguridad/integridad. |
| Alta | Afecta funcionalidad principal. |
| Media | Existe afectación con alternativa temporal. |
| Baja | Impacto menor o visual. |

---

# 18. Evidencia

Las pruebas deben producir evidencia verificable.

Puede incluir:

- logs;
- capturas;
- respuestas de API;
- pipeline;
- métricas;
- dashboards;
- resultados de scripts;
- archivos;
- reportes automatizados.

La evidencia debe permitir identificar:

- caso;
- fecha;
- ambiente;
- versión.

---

# 19. Automatización

Las pruebas repetibles deben automatizarse progresivamente.

Se consideran candidatas:

- unitarias;
- integración;
- contratos;
- smoke tests;
- infraestructura;
- detección de secretos;
- escaneo de vulnerabilidades;
- health checks.

---

# 20. Integración continua

Los pipelines deben apoyar la calidad mediante:

- build;
- lint;
- pruebas;
- análisis estático;
- detección de secretos;
- validación de contratos;
- construcción de imágenes;
- escaneo de vulnerabilidades.

Un fallo crítico debe bloquear la integración cuando corresponda.

---

# 21. Smoke testing

Después de cada despliegue se deben verificar funciones mínimas.

Como mínimo:

- health checks;
- autenticación;
- consulta pública;
- acceso autorizado;
- gateway;
- persistencia;
- conectividad básica.

---

# 22. Pruebas de infraestructura

La infraestructura debe validarse contra la distribución vigente de siete VMs.

Se deben verificar:

- VM Tools;
- VM 2 / 3 / 4 de QA;
- VM 5 / 6 / 7 de Producción;
- reglas de red;
- puertos;
- firewall;
- conectividad;
- separación de ambientes.

---

# 23. Pruebas de backup

Deben ejecutarse pruebas para comprobar:

- creación del backup;
- integridad;
- checksum;
- disponibilidad;
- asociación con versión de esquema.

---

# 24. Pruebas de restore

Debe comprobarse que:

- una base pueda restaurarse de forma independiente;
- el servicio pueda arrancar nuevamente;
- los datos mantengan integridad;
- otras bases no se vean afectadas.

---

# 25. Pruebas de rendimiento

Las pruebas de carga deben ejecutarse desde un equipo o nodo distinto al sistema medido cuando sea posible.

Las métricas mínimas incluyen:

- latencia;
- throughput;
- errores;
- CPU;
- memoria;
- conexiones.

Los umbrales deben relacionarse con los escenarios de calidad.

---

# 26. Herramientas

Las herramientas concretas pueden variar según el componente.

Se pueden utilizar:

- frameworks de pruebas propios de cada lenguaje;
- herramientas HTTP;
- OpenAPI;
- GitHub Actions;
- Prometheus;
- Grafana;
- herramientas de carga;
- herramientas de análisis de seguridad.

La herramienta elegida debe ser reproducible y versionable.

---

# 27. Cronograma de pruebas

La ejecución debe alinearse con los sprints.

La secuencia recomendada es:

    Desarrollo
        ↓
    Pruebas locales
        ↓
    Pull Request
        ↓
    CI
        ↓
    QA
        ↓
    Pruebas funcionales e integración
        ↓
    Seguridad / rendimiento / infraestructura
        ↓
    Reporte
        ↓
    Review

Las pruebas no deben concentrarse únicamente al final del sprint.

---

# 28. Riesgos del plan

Los principales riesgos incluyen:

- ambiente no disponible;
- servicios incompletos;
- datos de prueba insuficientes;
- contratos aún cambiantes;
- infraestructura no cerrada;
- dependencia de otras tareas;
- poco tiempo de ejecución;
- automatización incompleta;
- falta de evidencias.

---

# 29. Mitigación de riesgos

Se aplican medidas como:

- priorizar casos críticos;
- ejecutar pruebas tempranas;
- automatizar regresión;
- mantener datos reproducibles;
- utilizar smoke tests;
- registrar bloqueos;
- documentar riesgos residuales.

---

# 30. Entregables

Los principales entregables de QA son:

- metodología de calidad;
- plan de pruebas;
- diseño de pruebas;
- casos;
- evidencias;
- defectos;
- reporte de pruebas.

---

# 31. Relación con Test Design

El detalle de los casos se mantiene en:

[Test Design](./test-design.md)

El plan define **cómo y cuándo probar**.

El diseño define **qué casos ejecutar**.

---

# 32. Relación con Test Report

Los resultados reales deben registrarse en:

[Test Report](./test-report.md)

La relación general es:

    Test Plan
        ↓
    Test Design
        ↓
    Execution
        ↓
    Test Report

---

# 33. Pendientes

- [ ] Relacionar campaña con backlog vigente.
- [ ] Definir calendario exacto de ejecución.
- [ ] Confirmar herramientas de automatización por servicio.
- [ ] Definir umbrales definitivos de rendimiento.
- [ ] Incorporar matriz final de 7 VMs.
- [ ] Definir aprobación formal de salida a Producción.
- [ ] Relacionar pruebas con IDs definitivos del SRS.
- [ ] Definir ubicación final de evidencias.

---

# 34. Regla de mantenimiento

Este plan debe actualizarse cuando:

- cambie el alcance;
- cambien los ambientes;
- cambien los tipos de prueba;
- cambien los criterios de entrada o salida;
- cambie la infraestructura;
- cambie la estrategia de calidad;
- cambien los responsables;
- cambien los riesgos.

El plan debe representar la estrategia real de pruebas utilizada por el equipo.