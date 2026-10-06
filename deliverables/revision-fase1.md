# Fase 1 — revisión de entregas y validación

## Estado al 5 de octubre de 2026

Las entregas de Fase 1 están publicadas en Pull Requests abiertos. Ninguno se ha integrado en `main`.

| Tarea/entrega | Evidencia de validación |
|---|---|
| T-303.1 — utilidad de llaves RSA de prueba | El build de Identidad pasó en CI; la llave privada se conserva fuera del repositorio. |
| T-303.2 — configuración de llave Development y rechazo en QA | Build y pruebas de Identidad (20/20) y Campañas (18/18) aprobados en CI. |
| T-304.1 — esquema y roles de Identidad | El CI aplicó las migraciones a PostgreSQL 16 y aprobó las verificaciones de esquema y permisos. |
| T-312.1 — esquema y roles de Campañas | El CI aplicó las migraciones a PostgreSQL 16 y aprobó las verificaciones de esquema y permisos. |
| T-330.4 — auditoría de denegaciones en Identidad | CI verificó inserción de auditoría y restricciones de actualización, borrado y vaciado. |
| Frontend — autorización U7 | Alineado con ADR-011; build y SonarCloud aprobados. |
| Gateway — APISIX | CI aprobó lint, arranque con la configuración/plugin y gitleaks. Se añadió una prueba de integración de 401 y entrega a auditoría, pendiente de su primera ejecución. |

Una ejecución local previa registró **29/29 verificaciones** de bases y comprobó el mapeo de **88 columnas** de los `DbContext`. El CI vigente además aplica las migraciones y ejecuta las verificaciones dentro de clientes efímeros, cada uno conectado solo a la red interna de una base.

## Decisiones que requieren confirmación del equipo

### Migraciones SQL con Flyway

Las tareas T-304.1 y T-312.1 se describían originalmente como migraciones EF Core. La implementación usa Flyway en el repositorio `databases`, porque ahí se mantienen los esquemas SQL y no los `DbContext`. El CI ya valida esta implementación. Falta registrar la decisión formal en el Tech Radar (entrada 30) y actualizar el texto del backlog a “migraciones versionadas (Flyway)”.

### Ramas destino de los Pull Requests

La guía de Sprint 3 propone el flujo `main` ← `release/sprint-N` ← `development` ← `feature/*`. Los PR actuales apuntan a `main`. `identity-service` y `databases` todavía no tienen rama `development`, mientras que algunos repositorios existentes usan `develop`. El equipo debe confirmar el flujo y los nombres de rama antes de retargetear PR o crear ramas nuevas. No se deben cambiar automáticamente.

## Seguimiento técnico

- Los workflows de GitHub Actions usan tags de versión, no SHA inmutables; confirmar con DevOps si se debe fijar cada acción a un SHA, según la sección 7.5 del Tech Radar.
- Campaign aún usa `gitleaks/gitleaks-action@v2` y `GITLEAKS_LICENSE`. El check pasa, pero la entrada 77 del Tech Radar recomienda el binario de Gitleaks para repositorios de organización.
- Los proyectos .NET conservan referencias de paquetes `10.*`; fijar versiones exactas requiere acordar y revisar el restore de cada servicio.
- La prueba de integración del Gateway debe confirmar que un 401 de OpenID Connect alcanza el hook `log` del plugin y que la denegación llega al endpoint de auditoría de Identidad.
