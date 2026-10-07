# Diccionario de Datos — Red Vital

## 1. Propósito

Este documento define el **diccionario de datos de Red Vital**.

Su objetivo es establecer, de manera consistente, el significado de las principales entidades y atributos utilizados por el sistema.

El diccionario permite:

- mantener una definición común de los datos;
- identificar el servicio propietario de cada entidad;
- documentar el propósito de cada atributo;
- registrar obligatoriedad y restricciones;
- identificar la clasificación de la información;
- facilitar la trazabilidad entre requisitos, diseño lógico y diseño físico;
- reducir ambigüedades entre desarrollo, arquitectura, pruebas y documentación.

Este documento forma parte de la metadata de Red Vital y debe mantenerse alineado con:

- el modelo lógico;
- el modelo físico;
- los requisitos;
- los contratos;
- la implementación.

---

## 2. Convenciones

Cada atributo se documenta utilizando las siguientes columnas:

| Campo | Descripción |
|---|---|
| Atributo | Nombre lógico del dato. |
| Descripción | Significado funcional del atributo. |
| Tipo lógico | Tipo conceptual del dato. |
| Obligatorio | Indica si el dato debe existir. |
| Clasificación | Nivel de sensibilidad o uso. |
| Restricciones | Reglas que debe cumplir el dato. |

Los tipos lógicos utilizados en este documento pueden incluir:

- Identificador
- Texto
- Texto corto
- Texto largo
- Correo
- Fecha
- Fecha y hora
- Booleano
- Número entero
- Número decimal
- Estado
- Enumeración
- Referencia externa

Los tipos físicos definitivos de PostgreSQL se documentan en el modelo físico.

---

# 3. Contexto de Identidad

## 3.1 Usuario

**Servicio propietario:** Identidad

| Atributo | Descripción | Tipo lógico | Obligatorio | Clasificación | Restricciones |
|---|---|---|---|---|---|
| usuario_id | Identificador único del usuario. | Identificador | Sí | Operacional restringida | Único e inmutable. |
| nombre | Nombre del usuario. | Texto corto | Sí | Personal | No vacío. |
| identificacion | Documento o identificador personal autorizado. | Texto corto | Sí | Personal | Único dentro del alcance definido. |
| correo | Correo electrónico asociado al usuario. | Correo | Sí | Personal | Formato válido y único cuando aplique. |
| estado | Estado del usuario dentro del sistema. | Estado | Sí | Operacional restringida | Debe pertenecer al catálogo permitido. |
| jurisdiccion_id | Identificador de la jurisdicción asociada. | Referencia externa | Según aplique | Operacional restringida | Debe corresponder a una jurisdicción válida. |
| fecha_creacion | Fecha de creación del usuario. | Fecha y hora | Sí | Auditoría | Generada por el sistema. |
| fecha_actualizacion | Última modificación del registro. | Fecha y hora | Sí | Auditoría | Actualizada por el sistema. |

---

## 3.2 Rol

**Servicio propietario:** Identidad

| Atributo | Descripción | Tipo lógico | Obligatorio | Clasificación | Restricciones |
|---|---|---|---|---|---|
| rol_id | Identificador único del rol. | Identificador | Sí | Operacional restringida | Único. |
| nombre | Nombre funcional del rol. | Texto corto | Sí | Operacional restringida | Único dentro del catálogo de roles. |
| descripcion | Descripción de las responsabilidades del rol. | Texto largo | No | Operacional restringida | — |
| estado | Estado del rol. | Estado | Sí | Operacional restringida | Debe pertenecer al catálogo permitido. |

---

## 3.3 UsuarioRol

**Servicio propietario:** Identidad

| Atributo | Descripción | Tipo lógico | Obligatorio | Clasificación | Restricciones |
|---|---|---|---|---|---|
| usuario_rol_id | Identificador de la asignación. | Identificador | Sí | Operacional restringida | Único. |
| usuario_id | Usuario relacionado. | Identificador | Sí | Operacional restringida | Debe existir en Usuario. |
| rol_id | Rol asignado. | Identificador | Sí | Operacional restringida | Debe existir en Rol. |
| fecha_asignacion | Fecha de asignación del rol. | Fecha y hora | Sí | Auditoría | Generada por el sistema. |
| estado | Estado de la asignación. | Estado | Sí | Operacional restringida | Debe pertenecer al catálogo permitido. |

---

## 3.4 Sesión

**Servicio propietario:** Identidad

| Atributo | Descripción | Tipo lógico | Obligatorio | Clasificación | Restricciones |
|---|---|---|---|---|---|
| sesion_id | Identificador único de la sesión. | Identificador | Sí | Operacional restringida | Único. |
| usuario_id | Usuario asociado a la sesión. | Identificador | Sí | Operacional restringida | Debe existir en Usuario. |
| fecha_inicio | Inicio de la sesión. | Fecha y hora | Sí | Auditoría | Generada por el sistema. |
| fecha_expiracion | Momento de expiración. | Fecha y hora | Sí | Operacional restringida | Debe ser posterior al inicio. |
| estado | Estado de la sesión. | Estado | Sí | Operacional restringida | Debe pertenecer al catálogo permitido. |

---

# 4. Contexto Institucional

## 4.1 Institución

**Servicio propietario:** Institucional

| Atributo | Descripción | Tipo lógico | Obligatorio | Clasificación | Restricciones |
|---|---|---|---|---|---|
| institucion_id | Identificador único de la institución. | Identificador | Sí | Operacional restringida | Único. |
| nombre | Nombre oficial de la institución. | Texto corto | Sí | Operacional restringida | No vacío. |
| tipo_institucion_id | Tipo de institución. | Identificador | Según aplique | Operacional restringida | Debe existir en catálogo. |
| territorio_id | Territorio asociado. | Identificador | Sí | Operacional restringida | Debe existir en Territorio. |
| estado | Estado de la institución. | Estado | Sí | Operacional restringida | Debe pertenecer al catálogo permitido. |
| fecha_creacion | Fecha de creación. | Fecha y hora | Sí | Auditoría | Generada por el sistema. |

---

## 4.2 BancoDeSangre

**Servicio propietario:** Institucional

| Atributo | Descripción | Tipo lógico | Obligatorio | Clasificación | Restricciones |
|---|---|---|---|---|---|
| banco_sangre_id | Identificador único del banco de sangre. | Identificador | Sí | Operacional restringida | Único. |
| institucion_id | Institución propietaria o asociada. | Identificador | Sí | Operacional restringida | Debe existir en Institución. |
| nombre | Nombre del banco de sangre. | Texto corto | Sí | Operacional restringida | No vacío. |
| estado | Estado operativo. | Estado | Sí | Operacional restringida | Debe pertenecer al catálogo permitido. |

---

## 4.3 Territorio

**Servicio propietario:** Institucional

| Atributo | Descripción | Tipo lógico | Obligatorio | Clasificación | Restricciones |
|---|---|---|---|---|---|
| territorio_id | Identificador único del territorio. | Identificador | Sí | Operacional restringida | Único. |
| nombre | Nombre del territorio. | Texto corto | Sí | Operacional restringida | No vacío. |
| tipo | Tipo de división territorial. | Enumeración | Sí | Operacional restringida | Debe pertenecer al catálogo definido. |
| territorio_padre_id | Territorio superior en la jerarquía. | Identificador | No | Operacional restringida | Debe existir en Territorio cuando aplique. |

---

# 5. Contexto de Campañas

## 5.1 Campaña

**Servicio propietario:** Campañas

| Atributo | Descripción | Tipo lógico | Obligatorio | Clasificación | Restricciones |
|---|---|---|---|---|---|
| campania_id | Identificador único de la campaña. | Identificador | Sí | Operacional restringida | Único. |
| institucion_id | Institución relacionada con la campaña. | Referencia externa | Sí | Operacional restringida | Debe corresponder a una institución válida. |
| nombre | Nombre de la campaña. | Texto corto | Sí | Operacional restringida | No vacío. |
| descripcion | Descripción de la campaña. | Texto largo | No | Operacional restringida | — |
| fecha_inicio | Fecha de inicio. | Fecha | Sí | Operacional restringida | Debe ser válida. |
| fecha_fin | Fecha de finalización. | Fecha | Sí | Operacional restringida | Igual o posterior a fecha_inicio. |
| estado | Estado de la campaña. | Estado | Sí | Operacional restringida | Debe pertenecer al catálogo permitido. |
| fecha_creacion | Fecha de creación. | Fecha y hora | Sí | Auditoría | Generada por el sistema. |

---

## 5.2 Jornada

**Servicio propietario:** Campañas

| Atributo | Descripción | Tipo lógico | Obligatorio | Clasificación | Restricciones |
|---|---|---|---|---|---|
| jornada_id | Identificador único de la jornada. | Identificador | Sí | Operacional restringida | Único. |
| campania_id | Campaña a la que pertenece. | Identificador | Sí | Operacional restringida | Debe existir en Campaña. |
| fecha | Fecha de la jornada. | Fecha | Sí | Operacional restringida | Debe estar dentro del periodo permitido. |
| hora_inicio | Hora inicial. | Texto corto | Sí | Operacional restringida | Formato válido. |
| hora_fin | Hora final. | Texto corto | Sí | Operacional restringida | Posterior a hora_inicio. |
| capacidad | Capacidad máxima. | Número entero | Sí | Operacional restringida | Mayor o igual a cero. |

---

## 5.3 Reserva

**Servicio propietario:** Campañas

| Atributo | Descripción | Tipo lógico | Obligatorio | Clasificación | Restricciones |
|---|---|---|---|---|---|
| reserva_id | Identificador único de la reserva. | Identificador | Sí | Operacional restringida | Único. |
| jornada_id | Jornada asociada. | Identificador | Sí | Operacional restringida | Debe existir en Jornada. |
| usuario_id | Usuario asociado. | Referencia externa | Según aplique | Personal | Debe corresponder a un usuario válido. |
| estado | Estado de la reserva. | Estado | Sí | Operacional restringida | Debe pertenecer al catálogo permitido. |
| fecha_reserva | Fecha de creación de la reserva. | Fecha y hora | Sí | Auditoría | Generada por el sistema. |

---

# 6. Contexto de Donación

## 6.1 Donación

**Servicio propietario:** Donación

| Atributo | Descripción | Tipo lógico | Obligatorio | Clasificación | Restricciones |
|---|---|---|---|---|---|
| donacion_id | Identificador único de la donación. | Identificador | Sí | Operacional restringida | Único. |
| usuario_id | Identificador del usuario relacionado. | Referencia externa | Según aplique | Personal | Debe corresponder a un usuario válido. |
| campania_id | Campaña asociada. | Referencia externa | Según aplique | Operacional restringida | Debe corresponder a una campaña válida. |
| institucion_id | Institución relacionada. | Referencia externa | Sí | Operacional restringida | Debe corresponder a una institución válida. |
| fecha_donacion | Fecha de la donación. | Fecha y hora | Sí | Operacional restringida | Fecha válida. |
| estado | Estado de la donación. | Estado | Sí | Operacional restringida | Debe pertenecer al catálogo permitido. |
| fecha_creacion | Fecha de creación del registro. | Fecha y hora | Sí | Auditoría | Generada por el sistema. |

---

## 6.2 Unidad

**Servicio propietario:** Donación

| Atributo | Descripción | Tipo lógico | Obligatorio | Clasificación | Restricciones |
|---|---|---|---|---|---|
| unidad_id | Identificador único de la unidad. | Identificador | Sí | Operacional restringida | Único. |
| donacion_id | Donación de origen. | Identificador | Sí | Operacional restringida | Debe existir en Donación. |
| estado_unidad_id | Estado actual de la unidad. | Identificador | Sí | Operacional restringida | Debe existir en EstadoUnidad. |
| institucion_id | Institución responsable de la unidad. | Referencia externa | Sí | Operacional restringida | Debe corresponder a una institución válida. |
| fecha_creacion | Fecha de creación de la unidad. | Fecha y hora | Sí | Auditoría | Generada por el sistema. |

---

## 6.3 EstadoUnidad

**Servicio propietario:** Donación

| Atributo | Descripción | Tipo lógico | Obligatorio | Clasificación | Restricciones |
|---|---|---|---|---|---|
| estado_unidad_id | Identificador del estado. | Identificador | Sí | Operacional restringida | Único. |
| nombre | Nombre del estado. | Texto corto | Sí | Operacional restringida | Único dentro del catálogo. |
| descripcion | Significado del estado. | Texto largo | No | Operacional restringida | — |

---

## 6.4 HistorialUnidad

**Servicio propietario:** Donación

| Atributo | Descripción | Tipo lógico | Obligatorio | Clasificación | Restricciones |
|---|---|---|---|---|---|
| historial_id | Identificador del registro histórico. | Identificador | Sí | Auditoría | Único. |
| unidad_id | Unidad afectada. | Identificador | Sí | Operacional restringida | Debe existir en Unidad. |
| estado_anterior_id | Estado anterior. | Identificador | Según aplique | Operacional restringida | Debe existir en EstadoUnidad. |
| estado_nuevo_id | Nuevo estado. | Identificador | Sí | Operacional restringida | Debe existir en EstadoUnidad. |
| fecha_cambio | Momento de la transición. | Fecha y hora | Sí | Auditoría | Generada por el sistema. |
| usuario_id | Usuario responsable cuando aplique. | Referencia externa | Según aplique | Auditoría | Debe corresponder a un usuario válido. |

---

## 6.5 Inventario

**Servicio propietario:** Donación

| Atributo | Descripción | Tipo lógico | Obligatorio | Clasificación | Restricciones |
|---|---|---|---|---|---|
| inventario_id | Identificador del registro de inventario. | Identificador | Sí | Operacional restringida | Único. |
| unidad_id | Unidad administrada. | Identificador | Sí | Operacional restringida | Debe existir en Unidad. |
| institucion_id | Institución donde se encuentra. | Referencia externa | Sí | Operacional restringida | Debe corresponder a una institución válida. |
| estado | Estado del registro. | Estado | Sí | Operacional restringida | Debe pertenecer al catálogo permitido. |
| fecha_actualizacion | Última actualización. | Fecha y hora | Sí | Auditoría | Generada por el sistema. |

---

## 6.6 Transferencia

**Servicio propietario:** Donación

| Atributo | Descripción | Tipo lógico | Obligatorio | Clasificación | Restricciones |
|---|---|---|---|---|---|
| transferencia_id | Identificador de la transferencia. | Identificador | Sí | Operacional restringida | Único. |
| institucion_origen_id | Institución de origen. | Referencia externa | Sí | Operacional restringida | Debe corresponder a una institución válida. |
| institucion_destino_id | Institución de destino. | Referencia externa | Sí | Operacional restringida | Debe corresponder a una institución válida y ser distinta al origen. |
| estado | Estado de la transferencia. | Estado | Sí | Operacional restringida | Debe pertenecer al catálogo permitido. |
| fecha_solicitud | Fecha de creación de la transferencia. | Fecha y hora | Sí | Auditoría | Generada por el sistema. |
| fecha_cierre | Fecha de finalización. | Fecha y hora | No | Auditoría | Posterior a la fecha de solicitud. |

---

## 6.7 Escalamiento

**Servicio propietario:** Donación

| Atributo | Descripción | Tipo lógico | Obligatorio | Clasificación | Restricciones |
|---|---|---|---|---|---|
| escalamiento_id | Identificador único del escalamiento. | Identificador | Sí | Operacional restringida | Único. |
| institucion_id | Institución relacionada. | Referencia externa | Sí | Operacional restringida | Debe corresponder a una institución válida. |
| motivo | Motivo del escalamiento. | Texto largo | Sí | Operacional restringida | No vacío. |
| estado | Estado del escalamiento. | Estado | Sí | Operacional restringida | Debe pertenecer al catálogo permitido. |
| fecha_creacion | Fecha de creación. | Fecha y hora | Sí | Auditoría | Generada por el sistema. |

---

# 7. Contexto de Notificaciones

## 7.1 Notificación

**Servicio propietario:** Notificaciones

| Atributo | Descripción | Tipo lógico | Obligatorio | Clasificación | Restricciones |
|---|---|---|---|---|---|
| notificacion_id | Identificador de la notificación. | Identificador | Sí | Operacional restringida | Único. |
| evento_id | Identificador del evento de origen. | Identificador | Sí | Operacional restringida | Debe permitir idempotencia. |
| tipo | Tipo de notificación. | Enumeración | Sí | Operacional restringida | Debe pertenecer al catálogo permitido. |
| destinatario | Referencia al destinatario autorizado. | Referencia externa | Sí | Personal | No debe contener información innecesaria. |
| estado | Estado de la notificación. | Estado | Sí | Operacional restringida | Debe pertenecer al catálogo permitido. |
| fecha_creacion | Fecha de creación. | Fecha y hora | Sí | Auditoría | Generada por el sistema. |

---

## 7.2 IntentoEntrega

**Servicio propietario:** Notificaciones

| Atributo | Descripción | Tipo lógico | Obligatorio | Clasificación | Restricciones |
|---|---|---|---|---|---|
| intento_id | Identificador del intento de entrega. | Identificador | Sí | Auditoría | Único. |
| notificacion_id | Notificación relacionada. | Identificador | Sí | Operacional restringida | Debe existir en Notificación. |
| numero_intento | Número consecutivo del intento. | Número entero | Sí | Operacional restringida | Mayor que cero. |
| fecha_intento | Fecha y hora del intento. | Fecha y hora | Sí | Auditoría | Generada por el sistema. |
| resultado | Resultado del intento. | Estado | Sí | Operacional restringida | Debe pertenecer al catálogo permitido. |

---

# 8. Referencias externas

Los atributos que hacen referencia a entidades pertenecientes a otros servicios se clasifican como **Referencia externa**.

Ejemplos:

- `usuario_id`;
- `institucion_id`;
- `campania_id`.

Estas referencias:

- no constituyen claves foráneas entre bases independientes;
- no autorizan acceso directo a otra persistencia;
- deben validarse mediante contratos o reglas del dominio cuando corresponda;
- deben conservar únicamente el identificador necesario.

---

# 9. Clasificación de información

Se utilizan las siguientes categorías:

| Clasificación | Descripción |
|---|---|
| Personal | Información asociada a una persona identificada o identificable. |
| Sensible | Información que requiere controles reforzados. |
| Operacional restringida | Información interna necesaria para la operación. |
| Operacional agregada | Información consolidada utilizada en indicadores o reportes. |
| Auditoría | Información necesaria para trazabilidad. |
| Prohibida | Información fuera del alcance autorizado del sistema. |

La clasificación debe revisarse cuando cambie el alcance o los requisitos del sistema.

---

# 10. Datos prohibidos

Los datos clasificados como prohibidos no deben:

- existir como atributos del modelo;
- persistirse;
- aparecer en logs;
- transportarse en eventos;
- incluirse en métricas;
- utilizarse en contratos.

Cualquier propuesta de incorporación de este tipo de información requiere revisión de requisitos, arquitectura y alcance.

---

# 11. Relación con el diseño físico

El diccionario de datos define el significado funcional de los atributos.

El diseño físico debe posteriormente especificar:

- nombre físico;
- tipo PostgreSQL;
- tamaño;
- valor por defecto;
- nullabilidad;
- clave primaria;
- clave foránea interna;
- índices;
- restricciones;
- esquema;
- reglas de almacenamiento.

El diseño físico no debe cambiar el significado funcional definido en este documento sin actualizar el diccionario.

---

# 12. Relación con otros artefactos

- [Modelo General de Datos](../modelo-datos.md)
- [Modelo Lógico](../logical-model.md)
- [Modelo Físico](../physical-model.md)
- [Diseño de Datos](../DD.md)
- [SAD](../../architecture/SAD.md)
- [SDD](../../architecture/SDD.md)
- [Requisitos](../../requirements/)
- [Integración](../../integration/)

---

# 13. Regla de mantenimiento

Este diccionario debe actualizarse cuando:

- se cree un atributo;
- se elimine un atributo;
- cambie su significado;
- cambie su obligatoriedad;
- cambie su clasificación;
- cambie su servicio propietario;
- cambie una restricción funcional;
- se modifique el modelo lógico o físico.

No deben existir atributos implementados que carezcan de definición dentro de la metadata cuando formen parte del modelo persistente oficial.