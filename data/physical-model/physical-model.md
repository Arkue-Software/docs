# Modelo Físico de Datos — Red Vital

## 1. Propósito

Este documento describe el **modelo físico de datos de Red Vital**.

Su objetivo es traducir el modelo lógico a estructuras persistentes implementables en **PostgreSQL**, manteniendo:

- separación por servicio;
- propiedad exclusiva de los datos;
- integridad referencial dentro de cada contexto;
- restricciones;
- índices;
- tipos de datos;
- convenciones de nombres;
- trazabilidad con el modelo lógico y el diccionario de datos.

El modelo físico no debe introducir relaciones que contradigan las fronteras arquitectónicas definidas entre servicios.

---

# 2. Tecnología de persistencia

Red Vital utiliza **PostgreSQL** como tecnología principal de persistencia relacional.

La arquitectura utiliza persistencias separadas por contexto funcional.

Se contemplan las siguientes bases:

| Base de datos | Servicio propietario |
|---|---|
| `identity_db` | Servicio de Identidad |
| `institutional_db` | Servicio Institucional |
| `campaigns_db` | Servicio de Campañas |
| `donation_db` | Servicio de Donación |

El Servicio de Notificaciones puede mantener una persistencia técnica propia si la implementación lo requiere, pero no puede acceder directamente a las bases de otros servicios.

---

# 3. Principios del modelo físico

El modelo físico debe respetar los siguientes principios:

1. Cada base pertenece a un único servicio.
2. Ningún servicio consulta directamente las tablas de otro servicio.
3. No existen claves foráneas físicas entre bases de servicios diferentes.
4. Las referencias entre contextos se almacenan mediante identificadores.
5. Las integraciones entre servicios se realizan mediante contratos o eventos.
6. Cada servicio utiliza credenciales independientes.
7. Las migraciones se mantienen versionadas.
8. Las restricciones deben implementarse en la base cuando sea posible.
9. Los índices deben responder a patrones reales de consulta.
10. Los datos sensibles deben mantenerse limitados al mínimo necesario.
11. Los datos prohibidos no deben existir físicamente.
12. Las tablas de auditoría deben evitar almacenar información sensible innecesaria.

---

# 4. Convenciones de nombres

## 4.1 Tablas

Los nombres de tablas deben:

- utilizar minúsculas;
- utilizar `snake_case`;
- representar entidades del dominio;
- evitar abreviaturas ambiguas.

Ejemplo:

    users
    roles
    institutions
    campaigns
    donations
    blood_units

---

## 4.2 Columnas

Las columnas deben utilizar:

- minúsculas;
- `snake_case`;
- nombres descriptivos;
- sufijo `_id` para identificadores.

Ejemplo:

    user_id
    institution_id
    created_at
    updated_at

---

## 4.3 Claves primarias

Las claves primarias deben seguir la convención:

    <entidad>_id

Ejemplo:

    user_id
    campaign_id
    donation_id

---

## 4.4 Claves foráneas

Las claves foráneas físicas solo se utilizan dentro de una misma base de datos y contexto funcional.

No se permiten claves foráneas entre servicios distintos.

---

# 5. Tipos de datos recomendados

Los tipos físicos deben seleccionarse según el significado del atributo.

| Tipo lógico | Tipo PostgreSQL recomendado |
|---|---|
| Identificador | `uuid` |
| Texto corto | `varchar(n)` |
| Texto largo | `text` |
| Correo | `varchar(n)` |
| Número entero | `integer` |
| Número grande | `bigint` |
| Número decimal | `numeric(p,s)` |
| Booleano | `boolean` |
| Fecha | `date` |
| Fecha y hora | `timestamp with time zone` |
| Estado | `varchar(n)` o catálogo relacionado |
| JSON estructurado | `jsonb` |
| Enumeración controlada | catálogo o `check constraint` |

La selección definitiva debe validarse durante la implementación.

---

# 6. Base de Identidad

## 6.1 Tabla `users`

| Columna | Tipo | Null | Restricción |
|---|---|---|---|
| `user_id` | `uuid` | No | PK |
| `name` | `varchar(150)` | No | — |
| `identification` | `varchar(50)` | No | UNIQUE |
| `email` | `varchar(254)` | No | UNIQUE |
| `status` | `varchar(30)` | No | CHECK / catálogo |
| `jurisdiction_id` | `uuid` | Sí | Referencia lógica |
| `created_at` | `timestamp with time zone` | No | DEFAULT `now()` |
| `updated_at` | `timestamp with time zone` | No | DEFAULT `now()` |

---

## 6.2 Tabla `roles`

| Columna | Tipo | Null | Restricción |
|---|---|---|---|
| `role_id` | `uuid` | No | PK |
| `name` | `varchar(80)` | No | UNIQUE |
| `description` | `text` | Sí | — |
| `status` | `varchar(30)` | No | CHECK / catálogo |

---

## 6.3 Tabla `user_roles`

| Columna | Tipo | Null | Restricción |
|---|---|---|---|
| `user_role_id` | `uuid` | No | PK |
| `user_id` | `uuid` | No | FK → `users.user_id` |
| `role_id` | `uuid` | No | FK → `roles.role_id` |
| `assigned_at` | `timestamp with time zone` | No | DEFAULT `now()` |
| `status` | `varchar(30)` | No | CHECK / catálogo |

Se recomienda una restricción única sobre:

    user_id + role_id

cuando el modelo funcional no permita asignaciones duplicadas.

---

## 6.4 Tabla `sessions`

| Columna | Tipo | Null | Restricción |
|---|---|---|---|
| `session_id` | `uuid` | No | PK |
| `user_id` | `uuid` | No | FK → `users.user_id` |
| `started_at` | `timestamp with time zone` | No | DEFAULT `now()` |
| `expires_at` | `timestamp with time zone` | No | — |
| `status` | `varchar(30)` | No | CHECK / catálogo |

---

# 7. Base Institucional

## 7.1 Tabla `territories`

| Columna | Tipo | Null | Restricción |
|---|---|---|---|
| `territory_id` | `uuid` | No | PK |
| `name` | `varchar(150)` | No | — |
| `type` | `varchar(50)` | No | CHECK / catálogo |
| `parent_territory_id` | `uuid` | Sí | FK → `territories.territory_id` |

---

## 7.2 Tabla `institution_types`

| Columna | Tipo | Null | Restricción |
|---|---|---|---|
| `institution_type_id` | `uuid` | No | PK |
| `name` | `varchar(100)` | No | UNIQUE |
| `description` | `text` | Sí | — |

---

## 7.3 Tabla `institutions`

| Columna | Tipo | Null | Restricción |
|---|---|---|---|
| `institution_id` | `uuid` | No | PK |
| `name` | `varchar(180)` | No | — |
| `institution_type_id` | `uuid` | Sí | FK → `institution_types.institution_type_id` |
| `territory_id` | `uuid` | No | FK → `territories.territory_id` |
| `status` | `varchar(30)` | No | CHECK / catálogo |
| `created_at` | `timestamp with time zone` | No | DEFAULT `now()` |

---

## 7.4 Tabla `blood_banks`

| Columna | Tipo | Null | Restricción |
|---|---|---|---|
| `blood_bank_id` | `uuid` | No | PK |
| `institution_id` | `uuid` | No | FK → `institutions.institution_id` |
| `name` | `varchar(180)` | No | — |
| `status` | `varchar(30)` | No | CHECK / catálogo |

---

# 8. Base de Campañas

## 8.1 Tabla `campaigns`

| Columna | Tipo | Null | Restricción |
|---|---|---|---|
| `campaign_id` | `uuid` | No | PK |
| `institution_id` | `uuid` | No | Referencia lógica externa |
| `name` | `varchar(180)` | No | — |
| `description` | `text` | Sí | — |
| `start_date` | `date` | No | — |
| `end_date` | `date` | No | CHECK `end_date >= start_date` |
| `status` | `varchar(30)` | No | CHECK / catálogo |
| `created_at` | `timestamp with time zone` | No | DEFAULT `now()` |

`institution_id` no tiene clave foránea física porque pertenece a otra base y otro servicio.

---

## 8.2 Tabla `campaign_days`

| Columna | Tipo | Null | Restricción |
|---|---|---|---|
| `campaign_day_id` | `uuid` | No | PK |
| `campaign_id` | `uuid` | No | FK → `campaigns.campaign_id` |
| `date` | `date` | No | — |
| `start_time` | `time` | No | — |
| `end_time` | `time` | No | CHECK `end_time > start_time` |
| `capacity` | `integer` | No | CHECK `capacity >= 0` |

---

## 8.3 Tabla `reservations`

| Columna | Tipo | Null | Restricción |
|---|---|---|---|
| `reservation_id` | `uuid` | No | PK |
| `campaign_day_id` | `uuid` | No | FK → `campaign_days.campaign_day_id` |
| `user_id` | `uuid` | Sí | Referencia lógica externa |
| `status` | `varchar(30)` | No | CHECK / catálogo |
| `reserved_at` | `timestamp with time zone` | No | DEFAULT `now()` |

---

# 9. Base de Donación

## 9.1 Tabla `donations`

| Columna | Tipo | Null | Restricción |
|---|---|---|---|
| `donation_id` | `uuid` | No | PK |
| `user_id` | `uuid` | Sí | Referencia lógica externa |
| `campaign_id` | `uuid` | Sí | Referencia lógica externa |
| `institution_id` | `uuid` | No | Referencia lógica externa |
| `donation_date` | `timestamp with time zone` | No | — |
| `status` | `varchar(30)` | No | CHECK / catálogo |
| `created_at` | `timestamp with time zone` | No | DEFAULT `now()` |

---

## 9.2 Tabla `unit_states`

| Columna | Tipo | Null | Restricción |
|---|---|---|---|
| `unit_state_id` | `uuid` | No | PK |
| `name` | `varchar(80)` | No | UNIQUE |
| `description` | `text` | Sí | — |

---

## 9.3 Tabla `blood_units`

| Columna | Tipo | Null | Restricción |
|---|---|---|---|
| `unit_id` | `uuid` | No | PK |
| `donation_id` | `uuid` | No | FK → `donations.donation_id` |
| `unit_state_id` | `uuid` | No | FK → `unit_states.unit_state_id` |
| `institution_id` | `uuid` | No | Referencia lógica externa |
| `created_at` | `timestamp with time zone` | No | DEFAULT `now()` |

---

## 9.4 Tabla `unit_history`

| Columna | Tipo | Null | Restricción |
|---|---|---|---|
| `unit_history_id` | `uuid` | No | PK |
| `unit_id` | `uuid` | No | FK → `blood_units.unit_id` |
| `previous_state_id` | `uuid` | Sí | FK → `unit_states.unit_state_id` |
| `new_state_id` | `uuid` | No | FK → `unit_states.unit_state_id` |
| `changed_at` | `timestamp with time zone` | No | DEFAULT `now()` |
| `user_id` | `uuid` | Sí | Referencia lógica externa |

---

## 9.5 Tabla `inventory`

| Columna | Tipo | Null | Restricción |
|---|---|---|---|
| `inventory_id` | `uuid` | No | PK |
| `unit_id` | `uuid` | No | FK → `blood_units.unit_id` |
| `institution_id` | `uuid` | No | Referencia lógica externa |
| `status` | `varchar(30)` | No | CHECK / catálogo |
| `updated_at` | `timestamp with time zone` | No | DEFAULT `now()` |

---

## 9.6 Tabla `transfers`

| Columna | Tipo | Null | Restricción |
|---|---|---|---|
| `transfer_id` | `uuid` | No | PK |
| `origin_institution_id` | `uuid` | No | Referencia lógica externa |
| `destination_institution_id` | `uuid` | No | Referencia lógica externa |
| `status` | `varchar(30)` | No | CHECK / catálogo |
| `requested_at` | `timestamp with time zone` | No | DEFAULT `now()` |
| `closed_at` | `timestamp with time zone` | Sí | — |

Debe existir una restricción que impida que origen y destino sean iguales.

---

## 9.7 Tabla `transfer_units`

| Columna | Tipo | Null | Restricción |
|---|---|---|---|
| `transfer_unit_id` | `uuid` | No | PK |
| `transfer_id` | `uuid` | No | FK → `transfers.transfer_id` |
| `unit_id` | `uuid` | No | FK → `blood_units.unit_id` |

Se recomienda restricción única sobre:

    transfer_id + unit_id

---

## 9.8 Tabla `escalations`

| Columna | Tipo | Null | Restricción |
|---|---|---|---|
| `escalation_id` | `uuid` | No | PK |
| `institution_id` | `uuid` | No | Referencia lógica externa |
| `reason` | `text` | No | — |
| `status` | `varchar(30)` | No | CHECK / catálogo |
| `created_at` | `timestamp with time zone` | No | DEFAULT `now()` |

---

# 10. Persistencia de Notificaciones

Si el Servicio de Notificaciones requiere persistencia propia, debe mantenerse separada del resto de contextos.

Una estructura posible incluye las siguientes tablas.

---

## 10.1 Tabla `notifications`

| Columna | Tipo | Null | Restricción |
|---|---|---|---|
| `notification_id` | `uuid` | No | PK |
| `event_id` | `uuid` | No | UNIQUE |
| `type` | `varchar(50)` | No | CHECK / catálogo |
| `recipient_reference` | `varchar(255)` | No | — |
| `status` | `varchar(30)` | No | CHECK / catálogo |
| `created_at` | `timestamp with time zone` | No | DEFAULT `now()` |

---

## 10.2 Tabla `delivery_attempts`

| Columna | Tipo | Null | Restricción |
|---|---|---|---|
| `delivery_attempt_id` | `uuid` | No | PK |
| `notification_id` | `uuid` | No | FK → `notifications.notification_id` |
| `attempt_number` | `integer` | No | CHECK `attempt_number > 0` |
| `attempted_at` | `timestamp with time zone` | No | DEFAULT `now()` |
| `result` | `varchar(30)` | No | CHECK / catálogo |

---

# 11. Referencias entre servicios

Las referencias entre contextos se almacenan como identificadores sin clave foránea física.

Ejemplos:

    campaigns.institution_id
    reservations.user_id
    donations.user_id
    donations.campaign_id
    donations.institution_id
    blood_units.institution_id
    transfers.origin_institution_id
    transfers.destination_institution_id

Estas columnas:

- no crean dependencia física entre bases;
- no permiten acceso directo al servicio propietario;
- deben validarse mediante contratos cuando corresponda;
- deben conservar el identificador original sin duplicar datos innecesarios.

---

# 12. Índices

Los índices deben diseñarse de acuerdo con los patrones de consulta reales.

Como mínimo deben evaluarse índices para:

- claves primarias;
- claves foráneas internas;
- campos utilizados frecuentemente como filtros;
- estados;
- fechas;
- identificadores externos;
- combinaciones utilizadas en reglas de unicidad.

Ejemplos potenciales:

    users(email)
    users(identification)
    campaigns(institution_id)
    campaigns(status)
    reservations(user_id)
    donations(campaign_id)
    donations(institution_id)
    blood_units(unit_state_id)
    inventory(institution_id)
    transfers(status)

No deben crearse índices sin una necesidad de consulta identificada.

---

# 13. Constraints

Las reglas que puedan ser garantizadas directamente por PostgreSQL deben utilizar constraints.

Entre ellas:

- `PRIMARY KEY`;
- `FOREIGN KEY` internas;
- `UNIQUE`;
- `NOT NULL`;
- `CHECK`.

Ejemplos:

    end_date >= start_date

    end_time > start_time

    capacity >= 0

    origin_institution_id <> destination_institution_id

Las reglas que involucren múltiples servicios no deben implementarse mediante claves foráneas físicas.

---

# 14. Auditoría

Las tablas que representen operaciones relevantes deben incluir, cuando corresponda:

- `created_at`;
- `updated_at`;
- identificador del actor;
- información de transición;
- resultado.

La auditoría debe permitir trazabilidad sin almacenar:

- contraseñas;
- tokens;
- secretos;
- información sensible innecesaria.

---

# 15. Migraciones

Todos los cambios al modelo físico deben ejecutarse mediante migraciones versionadas.

Cada migración debe permitir identificar:

- servicio;
- versión;
- cambio realizado;
- fecha;
- dependencia con la aplicación;
- estrategia de reversión cuando aplique.

No deben realizarse cambios manuales en producción sin trazabilidad.

---

# 16. Roles y permisos de PostgreSQL

Cada persistencia debe separar, como mínimo:

### Rol de administración

Utilizado para tareas excepcionales de administración.

### Rol de migraciones

Utilizado para modificar el esquema.

### Rol de aplicación

Utilizado por el servicio en operación normal.

El rol de aplicación debe aplicar mínimo privilegio.

---

# 17. Respaldo y recuperación

Cada base de datos debe poder respaldarse de forma independiente.

El procedimiento debe contemplar:

- backup;
- restauración;
- validación;
- pruebas periódicas;
- documentación.

La restauración de una base no debe obligar a restaurar las demás.

---

# 18. Diagrama físico

El diagrama físico debe representar:

- bases de datos;
- tablas;
- columnas principales;
- claves primarias;
- claves foráneas internas;
- relaciones;
- separación por servicio.

Las referencias entre servicios deben representarse de forma diferenciada para evitar interpretarlas como relaciones físicas directas.

> **Pendiente:** incorporar el diagrama físico actualizado de Red Vital.

---

# 19. Relación con otros artefactos

- [Modelo General de Datos](./modelo-datos.md)
- [Modelo Lógico](./logical-model.md)
- [Diccionario de Datos](./metadata/data-dictionary.md)
- [Diseño de Datos](./DD.md)
- [SAD](../architecture/SAD.md)
- [SDD](../architecture/SDD.md)
- [Integración](../integration/)
- [Requisitos](../requirements/)

---

# 20. Regla de mantenimiento

Este documento debe actualizarse cuando:

- se cree una tabla;
- se elimine una tabla;
- se modifique una columna;
- cambie un tipo de dato;
- cambie una restricción;
- se cree o elimine un índice;
- cambie una relación interna;
- cambie la estrategia de persistencia;
- una decisión arquitectónica afecte el diseño físico.

El modelo físico debe mantenerse alineado con el modelo lógico, el diccionario de datos y la implementación real de PostgreSQL.