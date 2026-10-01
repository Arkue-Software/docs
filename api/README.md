# Contratos API — Sprint 3

Este directorio contiene los contratos API publicados para el incremento del Sprint 3 de RedVital.

## Contratos disponibles

### Operaciones del incremento

- [Operaciones del Sprint 3](./increment-operations-sprint-3.md)

Documento base que identifica las operaciones del incremento y sus reglas principales de errores y degradación.

---

### Servicio de Identidad

- [OpenAPI de Identidad](./identidad-openapi.yaml)

Incluye:

- `POST /v1/sesiones`
- `POST /v1/sesiones/renovacion`
- `DELETE /v1/sesiones/actual`
- `GET /.well-known/jwks.json`

Relacionado con:

- RF-23 — Inicio de sesión
- RF-24 — Término de sesión
- ADR-008 — Token de acceso y sesión de renovación

---

### Servicio de Donación

- [OpenAPI de Donación](./donacion-openapi.yaml)

Incluye:

- `POST /v1/donantes/busqueda`
- `POST /v1/donaciones`
- `POST /v1/unidades/{id}/ingreso-tamizaje`
- `POST /v1/unidades/{id}/tamizaje`
- `GET /v1/inventario`
- `POST /v1/unidades/{id}/despacho`
- `POST /v1/unidades/{id}/disposicion-final`
- `GET /v1/unidades/{id}/eventos`

---

### Servicio de Campañas

- [OpenAPI de Campañas](./campanias-openapi.yaml)

Incluye:

- `GET /v1/campanias`
- `GET /v1/campanias/{id}`
- `POST /v1/campanias/{id}/publicacion`
- `POST /v1/campanias/{id}/cierre`

---

## Uso por desarrollo

Los contratos publicados en este directorio son la referencia del incremento para la implementación de los servicios.

Los cambios en rutas, cuerpos, respuestas, códigos de error o reglas de seguridad deben mantenerse coherentes con:

- SRS V3.0;
- SAD V2.0;
- DD V2.0;
- ADR vigentes.

Las implementaciones no deben introducir campos o comportamientos incompatibles con estos contratos sin actualizar previamente la documentación correspondiente.

---

## Uso por QA

QA puede utilizar estos contratos para construir pruebas antes de que la implementación esté completa.

Como mínimo deben verificarse:

- esquemas de entrada;
- esquemas de respuesta;
- códigos HTTP;
- autenticación y autorización;
- jurisdicción;
- idempotencia;
- transiciones de estado;
- restricciones de campos;
- errores definidos por RFC 9457;
- comportamientos de degradación.

En particular:

### Identidad

Verificar:

- inicio de sesión válido e inválido;
- renovación;
- reutilización del secreto;
- cierre de sesión;
- token vencido;
- firma, emisor y audiencia;
- consulta JWKS.

### Donación

Verificar:

- búsqueda de donante;
- registro idempotente de donación;
- transición a tamizaje;
- esquema estricto de `apta`;
- inventario por jurisdicción;
- despacho con confirmación;
- disposición final;
- trazabilidad de eventos.

### Campañas

Verificar:

- visibilidad pública;
- visibilidad por jurisdicción;
- detalle;
- publicación;
- cierre;
- conflictos de estado.

---

## Trazabilidad

Estos contratos corresponden a:

- T-301.1 — Extracción de operaciones del incremento
- T-301.2 — OpenAPI de Identidad
- T-301.3 — OpenAPI de Donación
- T-301.4 — OpenAPI de Campañas
- T-301.5 — Publicación de contratos y habilitación para QA
