# RedVital — Contratos de API y Eventos

Contratos REST y de eventos por épica, siguiendo el flujo: **E1 → E2 → E3 → E5**, con **E4** como middleware transversal de autorización consultado por E2 y E3.

---

## E1 — Gestión de Donantes

### REST

```http
POST /donors
GET  /donors/{id}
GET  /donors/{id}/eligibility           → evalúa CriterioElegibilidad en vivo
GET  /donors/{id}/history                → agrega datos de E2 y E5 (composición en gateway/BFF)
PATCH /donors/{id}/status                body: {estado, motivo}
```

### Eventos que consume

```json
// de E2: donation.completed
// → actualiza fecha_ultima_donacion para el cálculo de intervalo de elegibilidad
{
  "donacion_id": "uuid",
  "donante_id": "uuid",
  "fecha": "2026-08-31T10:00:00Z"
}
```

---

## E2 — Trazabilidad y Ciclo de Vida

### REST

```http
POST /donations
POST /donations/{id}/screening
POST /donations/{id}/lab-results
GET  /donations/{id}

GET  /blood-units/{id}
GET  /blood-units/{id}/audit-trail       → historial completo de estados (trazabilidad)

POST /transfusion-requests
GET  /transfusion-requests/{id}/compatible-units
     → internamente llama a E3: GET /blood-units?tipo_sangre=X&estado=disponible
       y aplica la tabla de compatibilidad ABO/Rh
POST /transfusion-requests/{id}/assign   body: {unidad_ids: []}
     → dispara POST /blood-units/{id}/reserve en E3 por cada unidad
POST /transfusions                       body: {solicitud_id, unidad_id, paciente_id}
PATCH /transfusions/{id}                 body: {estado, signos_vitales_post, reaccion_adversa}
```

### Eventos que emite

```json
// donation.completed        → consumido por E1 y E5
// blood-unit.created         → consumido por E3
{
  "unidad_id": "uuid",
  "tipo_sangre": "O-",
  "componente": "globulos_rojos",
  "fecha_extraccion": "2026-08-31",
  "fecha_vencimiento": "2026-10-12",
  "banco_id": "uuid"
}

// blood-unit.state-changed   → consumido por E3 (actualiza vistas de inventario)
{
  "unidad_id": "uuid",
  "estado_anterior": "disponible",
  "estado_nuevo": "reservada",
  "fecha_hora": "2026-08-31T14:00:00Z"
}

// transfusion.completed      → consumido por E3 (cierra la unidad)
{
  "transfusion_id": "uuid",
  "unidad_id": "uuid",
  "paciente_id": "uuid",
  "fecha": "2026-08-31T15:30:00Z"
}
```

### Autorización (consulta a E4)
Toda request a `/transfusions...` valida el `hospital_id` contra `AccesoTerritorial` del usuario autenticado antes de procesar.

---

## E3 — Inventario y Alertas

### REST

```http
GET   /blood-units?tipo_sangre=O-&componente=globulos_rojos&estado=disponible&banco_id=
GET   /blood-units/expiring?dias=7
GET   /inventory/stock-summary?banco_id=        → existencias agregadas por tipo/componente
GET   /inventory/shortage-alerts?banco_id=
POST  /blood-units/{id}/reserve                 body: {solicitud_id}
POST  /blood-units/{id}/release
```

### Jobs programados

| Job | Frecuencia | Acción |
|---|---|---|
| Vencimiento | diario | `fecha_vencimiento < hoy AND estado=disponible` → `vencida`, dispara `blood-unit.expired`, genera `DescarteUnidad` |
| Escasez | cada X horas | compara stock actual vs `UmbralMinimoStock` por banco/tipo/componente → genera `AlertaEscasez` |

### Eventos que emite

```json
// blood-unit.expired
{ "unidad_id": "uuid", "fecha_vencimiento": "2026-10-12" }

// blood-unit.discarded
{ "unidad_id": "uuid", "motivo": "vencida", "fecha": "2026-10-13" }

// stock.below-threshold      → consumido por E5 (sugerir campaña dirigida)
{ "banco_id": "uuid", "tipo_sangre": "O-", "componente": "globulos_rojos", "cantidad_actual": 3, "cantidad_minima": 15 }
```

### Autorización (consulta a E4)
Toda request a `/blood-units...` filtra por los `banco_id` a los que el usuario tiene `AccesoTerritorial`.

---

## E4 — Administración Territorial

### REST

```http
POST /territories
GET  /territories/{id}/hospitals
GET  /territories/{id}/blood-banks
POST /users/{id}/territorial-access      body: {territorio_id, rol}
GET  /users/{id}/territorial-access      → usado como chequeo por E2 y E3
```

### Middleware transversal
No expone un flujo de negocio propio: E2 y E3 lo consultan como autorización antes de resolver sus propias requests. Resuelve `territorio_id` del `banco_id`/`hospital_id` involucrado y valida contra `AccesoTerritorial` del usuario autenticado (incluyendo jerarquía de territorios si aplica).

---

## E5 — Gestión de Campañas (incluye gamificación)

### REST

```http
POST /campaigns
POST /campaigns/{id}/join                 body: {donante_id}
GET  /donors/{id}/points-summary           → {puntos_totales, nivel, insignias[]}
POST /rewards/{id}/redeem                  body: {donante_id}
```

### Eventos que consume

```json
// de E2: donation.completed
// → calcula puntos, evalúa insignias, incrementa unidades_recolectadas de la campaña

// de E3: stock.below-threshold
// → sugiere/crea campaña dirigida a ese tipo de sangre (automático o solo notificación a admin)
```

---

## Bus de eventos — resumen

| Evento | Emisor | Consumidores |
|---|---|---|
| `donation.completed` | E2 | E1, E5 |
| `blood-unit.created` | E2 | E3 |
| `blood-unit.state-changed` | E2 | E3 |
| `blood-unit.expired` | E3 | — (interno, genera descarte) |
| `blood-unit.discarded` | E3 | — |
| `transfusion.completed` | E2 | E3 |
| `stock.below-threshold` | E3 | E5 |

## Notas de implementación

- **Un microservicio por épica**, con su propia base de datos; las referencias cruzadas se resuelven vía API o eventos, no joins directos.
- **E4 es transversal**: middleware de autorización consultado por E2 y E3, no un paso más del flujo.
- El `RegistroAuditoria` de E2 nunca se actualiza por un UPDATE directo — todo cambio de estado de una unidad pasa por el evento `blood-unit.state-changed`, que es lo que alimenta la tabla de auditoría.
