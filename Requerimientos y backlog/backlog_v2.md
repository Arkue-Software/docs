# RedVital — Backlog V2 (final)
**Arkhé Software S.A.S.** | Product Owner
Issue: "Reescribir el backlog desde cero: HU de negocio y HU técnicas, con prioridad de dos valores y clase" · Entregable: Backlog V2

---

## 0. Qué cambió frente a la versión anterior

Este backlog se realinea con el catálogo de módulos vigente: siete módulos de negocio (M1–M7) más tres servicios transversales (ST1 Notificaciones, ST2 Auditoría, ST3 Autenticación y autorización). Cambios de contenido, no solo de numeración:

- **HU-13 y HU-14 se reescriben.** Su alcance anterior (restringir visibilidad, denegar accesos) ya no corresponde al requerimiento vigente de administración territorial — ese requerimiento quedó acotado a "mantener la jerarquía territorial y el registro de instituciones". La parte de control de acceso se traslada a una **historia técnica nueva** (AR-08).
- **HU-27 se agrega.** Se formalizó un rol de auditoría con su propia preocupación y su propio requerimiento (consulta de bitácora), que no existía en el backlog anterior.
- **Todas las historias de donantes, trazabilidad, inventario y transferencias cambian de número de módulo** (no de contenido) para seguir la numeración vigente.
- **No hay historia de "extensión a donación de órganos" como módulo propio** — ese alcance quedó como atributo de calidad y restricción de diseño interna, cubierto por HU-26 como historia técnica transversal.

---

## 1. Resumen — Historias de negocio

| ID | Módulo | Historia | Clase | Prioridad (cliente/arq.) | Estimación | Requerimiento que implementa |
|---|---|---|---|---|---|---|
| HU-01 | M1 | Registro anónimo de donante | Historia | Alta / Baja | 2 | RF-01 |
| HU-02 | M1 | Registro con perfil completo | Historia | Alta / Media | 3 | RF-02 |
| HU-03 | M1 | Cálculo de elegibilidad por intervalo | Historia | Alta / Media | 3 | RF-02 |
| HU-04 | M1 | Consulta de historial propio | Historia | Alta / Baja | 2 | RF-17 |
| HU-05 | M3 | Registro de donación asociada a banco/campaña | Historia | Alta / Media | 3 | RF-03 |
| HU-06 | M3 | Trazabilidad de origen de la unidad | Historia | Alta / Media | 3 | RF-04 |
| HU-07 | M3 | Marcado de unidad no apta sin exponer causa clínica | Historia | Alta / Alta | 5 | RF-05 |
| HU-08 | M3 | Protocolo de disposición final, con bloqueo de despacho | Historia | Alta / Media | 3 | RF-06 |
| HU-09 | M4 | Consulta de existencias por componente y tipo | Historia | Alta / Media | 3 | RF-07 |
| HU-10 | M4 | Alerta de escasez por umbral configurable | Historia | Alta / Media | 3 | RF-19 |
| HU-11 | M4 | Alerta de vencimiento próximo | Historia | Alta / Media | 3 | RF-20 |
| HU-12 | M4 | Exclusión automática de unidades vencidas | Historia | Alta / Alta | 5 | RF-18 |
| HU-13 | M6 | Registro de instituciones por nivel territorial *(reescrita)* | Historia | Alta / Media | 3 | RF-09 |
| HU-14 | M6 | Consulta de la jerarquía territorial *(reescrita)* | Historia | Alta / Baja | 2 | RF-09 |
| HU-15 | M2 | Creación y publicación de campaña | Historia | Alta / Baja | 2 | RF-08 |
| HU-16 | M2 | Seguimiento de resultados de campaña | Historia | Alta / Media | 3 | RF-08 |
| HU-17 | M5 | Sugerencia de redistribución entre bancos | Historia | Media / Alta | 8 | RF-10 |
| HU-18 | M5 | Flujo de aprobación de transferencia | Historia | Media / Media | 5 | RF-10 |
| HU-19 | M1 (Característica) | Otorgamiento de insignias por recurrencia | Característica | Media / Media | 3 | RF-11 |
| HU-20 | M1 (Característica) | Validación de catálogo de recompensas no monetarias | Tarea | Media / Baja | 2 | RF-11 |
| HU-21 | ST1 | Notificación de elegibilidad recuperada | Historia | Media / Alta | 5 | RF-12 |
| HU-22 | ST1 | Notificación de campañas cercanas | Historia | Media / Media | 3 | RF-12 |
| HU-23 | M7 | Tablero de indicadores territoriales (solo lectura) | Historia | Media / Alta | 8 | RF-13 |
| HU-24 | M5 | Movilización de donantes compatibles | Historia | Baja / Alta | 8 | RF-14 |
| HU-25 | M1 | Reserva de cupo con expiración automática | Historia | Baja / Media | 5 | RF-15 |
| HU-26 | Transversal | Modelo de dominio genérico (habilitador técnico) | Spike | Alta / Alta | 3 | RNF-05 / RI-01 |
| HU-27 | ST2 | Consulta de bitácora de auditoría *(nueva)* | Historia | Alta / Media | 3 | RF-21 |

**Nota sobre HU-19/HU-20:** conservan su contenido de la versión anterior, pero su épica asociada ya no es una épica propia — son **Característica** de M1, Gestión de Donantes, conforme a la reestructuración vigente.

---

## 2. Historias reescritas (detalle completo)

### HU-13 — Registro de instituciones por nivel territorial
| Campo | Contenido |
|---|---|
| Identificador | HU-13 |
| Épica asociada | M6 — Administración Institucional y Territorial |
| Clase | Historia |
| Enunciado | Como administrador nacional o coordinador territorial, quiero registrar y mantener las instituciones adscritas a cada nivel de la jerarquía territorial, para tener un registro único y actualizado de la red de bancos de sangre. |
| Criterios de aceptación | `DADO QUE` tengo el rol de administrador nacional o coordinador territorial `CUANDO` registro una institución y la asocio a un departamento y municipio `ENTONCES` la institución queda visible en la jerarquía territorial correspondiente. |
| Prioridad | Importancia cliente: **Alta** · Dificultad arquitecto: **Media** |
| Estimación | 3 puntos |
| Módulo | M6 — Administración Institucional y Territorial (Servicio Institucional, .NET) |

### HU-14 — Consulta de la jerarquía territorial
| Campo | Contenido |
|---|---|
| Identificador | HU-14 |
| Épica asociada | M6 — Administración Institucional y Territorial |
| Clase | Historia |
| Enunciado | Como coordinador territorial, quiero consultar la jerarquía territorial y las instituciones adscritas a mi nivel, para conocer la composición de la red que superviso. |
| Criterios de aceptación | `DADO QUE` consulto la jerarquía territorial `CUANDO` se carga la vista `ENTONCES` veo los departamentos, municipios e instituciones correspondientes a mi jurisdicción. |
| Prioridad | Importancia cliente: **Alta** · Dificultad arquitecto: **Baja** |
| Estimación | 2 puntos |
| Módulo | M6 — Administración Institucional y Territorial (Servicio Institucional, .NET) |

### HU-27 — Consulta de bitácora de auditoría *(nueva)*
| Campo | Contenido |
|---|---|
| Identificador | HU-27 |
| Épica asociada | ST2 — Auditoría |
| Clase | Historia |
| Enunciado | Como usuario con rol de auditoría, quiero consultar la bitácora de auditoría en modo de solo lectura, para verificar el cumplimiento de la trazabilidad y del control de acceso sin poder alterar los registros. |
| Criterios de aceptación | `DADO QUE` tengo el rol de auditor `CUANDO` consulto la bitácora de auditoría `ENTONCES` veo los registros de operaciones y de intentos de acceso denegado, con autor, operación y marca temporal, sin ninguna opción de edición o eliminación. |
| Prioridad | Importancia cliente: **Alta** · Dificultad arquitecto: **Media** |
| Estimación | 3 puntos |
| Módulo | ST2 — Auditoría |

---

## 3. Historia técnica nueva (no deriva de una historia de negocio)

### AR-08 — Control de acceso por jurisdicción en el API Gateway
| Campo | Contenido |
|---|---|
| Identificador | AR-08 |
| Épica asociada | Épica A — Arquitectura y Decisiones Estructurales |
| Tipo | Técnica |
| Clase | Tarea |
| Enunciado | Como Arquitecta de Software, quiero implementar la verificación de jurisdicción en el punto de entrada de la API, para que ningún usuario pueda consultar información fuera de su alcance territorial, sin importar el módulo consultado. |
| Criterios de aceptación | `DADO QUE` un usuario adscrito a una entidad territorial departamental `CUANDO` solicita información de un banco fuera de su jurisdicción `ENTONCES` el sistema deniega el acceso, no confirma la existencia del recurso ni nombra la jurisdicción ajena, y registra el intento en la bitácora de auditoría. |
| Prioridad | Importancia cliente: **Alta** · Dificultad arquitecto: **Alta** |
| Estimación | 5 puntos |
| Responsable | Sara (Arquitecta) |

Esta historia reemplaza, en su función de control de acceso, lo que antes vivía incorrectamente dentro de HU-13/HU-14. Se agrega a la Épica A del backlog técnico junto a los ítems AR-01 a AR-07 ya existentes.

---

## 4. Trazabilidad

Todo requerimiento funcional tiene al menos una historia en este backlog, y toda historia se puede rastrear hasta un requerimiento, un atributo de calidad o una restricción de diseño. No quedan elementos huérfanos en ningún sentido, salvo el ajuste de AR-08, que se rastrea hasta un atributo de calidad de control de acceso y no hasta un requerimiento funcional — consistente con el criterio de que todo el trabajo, técnico y funcional, va al backlog.
