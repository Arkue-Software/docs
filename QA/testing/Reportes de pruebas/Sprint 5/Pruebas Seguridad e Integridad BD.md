# Informe de pruebas - Bases de datos

## Control del documento

| Campo | Información |
|---|---|
| Código del documento | TST-BASES-DE-DATOS |
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
| 0.1 | 05/11/2026 | Tomás Silva | Consolidación de las verificaciones de disponibilidad, permisos y controles de auditoría de bases de datos. |

---

## 1. Resumen ejecutivo

Se realizaron verificaciones sobre las bases de datos del ambiente QA de RedVital, enfocadas en comprobar su disponibilidad, los permisos asignados a las cuentas de servicio y las restricciones de modificación sobre información sensible, especialmente los registros de auditoría.

Las pruebas permitieron verificar que los contenedores de bases de datos se encontraban saludables y que los permisos evaluados respetaban las restricciones esperadas. También se comprobaron controles de integridad y validaciones relacionadas con la auditoría de Identidad.

## 2. Información de ejecución

| Campo | Información |
|---|---|
| Componente | Bases de datos |
| Tipo de prueba | Pruebas de disponibilidad, permisos y restricciones |
| Ambiente | Pruebas |
| Motor | PostgreSQL 16 |
| Servidor de bases de datos | VM de datos QA |
| Fecha | 05/11/2026 |
| Ejecutor | Tomás Silva |
| Dependencias | Contenedores de bases de datos QA |
| Tipo de dependencia | Real, dentro del ambiente de pruebas |

### 2.1 Bases de datos identificadas

| Base de datos / servicio | Puerto |
|---|---:|
| Identidad | 5432 |
| Campañas | 5433 |
| Donación | 5434 |
| Institucional | 5435 |
| Notificaciones | 5436 |

Se verificó el estado de los contenedores correspondientes al ambiente QA. Los contenedores observados se encontraban saludables durante la revisión.

## 3. Ejecución de pruebas

### 3.1 Disponibilidad

**Descripción:** Se revisó el estado de los contenedores de bases de datos correspondientes al ambiente QA.

**Resultado esperado:** Los servicios de base de datos deben encontrarse activos y saludables.

**Resultado obtenido:** Los contenedores identificados para Identidad, Campañas, Donación, Institucional y Notificaciones presentaron estado saludable durante la observación.

**Veredicto:** Aprobado.

### 3.2 Permisos de cuentas de servicio

Se realizaron verificaciones sobre los permisos de la cuenta de servicio de la base de datos de Identidad.

| ID | Verificación | Resultado obtenido | Estado |
|---|---|---|---|
| BD-01 | Permiso de inserción de usuarios | Operación permitida | Aprobado |
| BD-02 | Permiso de actualización de usuarios | Operación permitida | Aprobado |
| BD-03 | Permiso de eliminación de usuarios | Operación rechazada | Aprobado |
| BD-04 | Permiso de inserción de roles | Operación rechazada | Aprobado |
| BD-05 | Modificación de registros de auditoría | Operación rechazada | Aprobado |
| BD-06 | Eliminación de registros de auditoría | Operación rechazada | Aprobado |
| BD-07 | Truncamiento de registros de auditoría | Operación rechazada | Aprobado |
| BD-08 | Creación de objetos en el esquema `public` | Operación rechazada | Aprobado |

Los resultados coincidieron con las restricciones esperadas para la cuenta de servicio.

### 3.3 Validaciones funcionales y de seguridad

También se verificaron restricciones relacionadas con la sesión, la jurisdicción y la identificación de clientes de servicio.

| ID | Verificación | Resultado obtenido | Estado |
|---|---|---|---|
| BD-09 | Rechazo de sesión superior a 8 horas | La operación fue rechazada | Aprobado |
| BD-10 | Rechazo de institución sin jurisdicción válida | La operación fue rechazada | Aprobado |
| BD-11 | Rechazo de cliente de servicio desconocido | La operación fue rechazada | Aprobado |

### 3.4 Controles de auditoría

Se verificaron las restricciones de integridad de la información de auditoría y las validaciones de sus registros.

| ID | Verificación | Resultado obtenido | Estado |
|---|---|---|---|
| BD-12 | Configuración de roles existentes | Se identificaron seis roles configurados | Aprobado |
| BD-13 | Inserción válida de registro de auditoría anónima | Inserción permitida | Aprobado |
| BD-14 | Inserción de registro anónimo con `actor_id` | Operación rechazada | Aprobado |
| BD-15 | Identificador de correlación superior a 36 caracteres | Operación rechazada | Aprobado |

Las últimas validaciones se realizaron utilizando transacciones de prueba con `BEGIN`, bloques `DO` y `ROLLBACK`, evitando conservar cambios de prueba.

## 4. Resumen de resultados

| Área | Resultado |
|---|---|
| Disponibilidad de contenedores QA | Saludable durante la revisión |
| Permisos de cuentas de servicio | Restricciones evaluadas cumplidas |
| Validaciones funcionales | Restricciones evaluadas cumplidas |
| Controles de auditoría | Restricciones evaluadas cumplidas |

No se registraron fallos en las verificaciones descritas.

## 5. Defectos encontrados

No se identificaron defectos durante las verificaciones realizadas.

## 6. Conclusiones

Las bases de datos del ambiente QA presentaron disponibilidad adecuada durante la revisión. En la base de datos de Identidad se comprobaron restricciones sobre las operaciones permitidas a la cuenta de servicio, así como controles de integridad y protección de los registros de auditoría.

Las operaciones de modificación y eliminación de auditoría fueron rechazadas correctamente, al igual que las operaciones que excedían los permisos asignados.

## 7. Pendientes y observaciones

- Las verificaciones detalladas de permisos y auditoría se concentraron en la base de datos de Identidad.
- El estado saludable de los demás contenedores no implica que se hayan probado todos sus permisos o estructuras.
- No se ejecutó la prueba `verificar-fase1.sh`, debido a que contiene operaciones de escritura y truncamiento que no corresponden al alcance seguro de esta actividad.
- No se realizaron modificaciones permanentes sobre las bases de datos.
- Las comprobaciones funcionales se realizaron con transacciones revertidas cuando correspondía.

## 8. Evidencias

Se cuenta con salidas de consola de los estados de los contenedores y resultados de las verificaciones de permisos y restricciones. Las evidencias deben excluir credenciales, datos personales y valores internos sensibles.

## 9. Revisión y aprobación

| Rol | Responsable | Estado |
|---|---|---|
| Responsable del informe | Tomás Silva | Elaborado |
| Revisado por | Pendiente | Pendiente |
| Aprobado por | Pendiente | Pendiente |