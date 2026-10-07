# Modelo de Datos — Red Vital

## 1. Propósito

Este documento describe el modelo general de datos de **Red Vital** y la distribución de la información entre los distintos servicios del sistema.

Su objetivo es establecer:

- qué tipos de información existen;
- qué servicio es propietario de cada conjunto de datos;
- cómo se relacionan los distintos contextos;
- qué información puede compartirse entre servicios;
- qué información debe permanecer aislada;
- qué principios de persistencia deben respetarse.

Este documento presenta una visión conceptual del modelo de datos.

El detalle lógico, físico y de metadata se mantiene en los artefactos especializados del diseño de datos.

---

## 2. Principios del modelo de datos

El modelo de datos de Red Vital se rige por los siguientes principios:

1. Cada dato tiene un único servicio propietario.
2. Ningún servicio accede directamente a las tablas de otro servicio.
3. Los servicios se comunican mediante contratos explícitos.
4. Las bases de datos mantienen credenciales independientes.
5. La persistencia se organiza de acuerdo con las fronteras funcionales del sistema.
6. Las reglas de negocio se aplican en el servicio propietario del dato.
7. Los cambios de esquema deben mantenerse versionados.
8. Los datos sensibles deben utilizarse únicamente cuando sean necesarios.
9. Los datos prohibidos por el alcance del sistema no deben almacenarse.
10. Las relaciones entre dominios se realizan mediante identificadores y contratos, no mediante joins entre bases de datos.

---

## 3. Tecnología de persistencia

Red Vital utiliza **PostgreSQL** como tecnología principal de persistencia relacional.

El uso de PostgreSQL no implica una base de datos única compartida por toda la solución.

La arquitectura utiliza persistencias separadas de acuerdo con la responsabilidad de cada servicio.

Actualmente se contemplan cuatro contextos principales de persistencia:

| Persistencia | Servicio propietario |
|---|---|
| Identidad | Servicio de Identidad |
| Institucional | Servicio Institucional |
| Campañas | Servicio de Campañas |
| Donación | Servicio de Donación |

El Servicio de Notificaciones no accede directamente a estas bases de datos.

---

## 4. Contexto de Identidad

El Servicio de Identidad es propietario de la información necesaria para identificar y autenticar a los usuarios del sistema.

Entre los datos pertenecientes a este contexto se encuentran:

- usuarios;
- credenciales;
- roles;
- información de autenticación;
- sesiones;
- jurisdicción asociada al usuario;
- información necesaria para autorización;
- credenciales utilizadas por servicios cuando corresponda.

Otros servicios no almacenan contraseñas ni administran directamente las credenciales de usuario.

Cuando un servicio requiere información relacionada con identidad, debe utilizar los contratos definidos por el Servicio de Identidad o información transportada de forma controlada mediante mecanismos autorizados.

---

## 5. Contexto Institucional

El Servicio Institucional es propietario de la información relacionada con la estructura institucional y territorial utilizada por Red Vital.

Entre los datos pertenecientes a este contexto se encuentran:

- instituciones;
- bancos de sangre;
- ubicaciones;
- estructura territorial;
- jerarquías territoriales;
- relaciones institucionales;
- información administrativa asociada a las instituciones.

Otros servicios pueden almacenar identificadores institucionales cuando necesiten relacionar un registro propio con una institución.

Sin embargo, no deben replicar innecesariamente el registro institucional completo ni modificar información institucional.

---

## 6. Contexto de Campañas

El Servicio de Campañas es propietario de la información asociada a las campañas gestionadas dentro de Red Vital.

Entre los datos pertenecientes a este contexto se encuentran:

- campañas;
- programación;
- fechas;
- horarios;
- cupos;
- disponibilidad;
- reservas;
- estados de campaña;
- relaciones entre campañas e instituciones.

Cuando otro servicio necesita relacionar una operación con una campaña, utiliza el identificador correspondiente y el contrato definido.

---

## 7. Contexto de Donación

El Servicio de Donación es propietario de los datos relacionados con el ciclo operativo de la donación.

Entre los datos pertenecientes a este contexto se encuentran:

- donaciones;
- unidades;
- estados de las unidades;
- trazabilidad;
- inventario;
- transferencias;
- movimientos;
- procesos de escalamiento;
- información asociada al ciclo de vida de la unidad.

El Servicio de Donación constituye el propietario principal de la información operativa relacionada con el proceso de donación.

Otros servicios no deben modificar directamente esta información.

---

## 8. Servicio de Notificaciones

El Servicio de Notificaciones no es propietario de los datos principales de los dominios funcionales.

Su función consiste en consumir eventos o información autorizada para determinar y ejecutar una notificación.

Puede mantener información propia necesaria para su operación, como:

- estado de entrega;
- intentos de envío;
- identificador del evento procesado;
- resultado de la entrega;
- información técnica necesaria para evitar duplicados.

No debe utilizarse como repositorio alternativo de información perteneciente a Identidad, Institucional, Campañas o Donación.

---

## 9. Relaciones entre contextos

Las relaciones entre dominios se realizan mediante identificadores y contratos.

Por ejemplo:

- una campaña puede estar asociada a una institución mediante un identificador institucional;
- una donación puede relacionarse con una campaña mediante el identificador de la campaña;
- una operación puede registrar el identificador del usuario responsable;
- una transferencia puede relacionar instituciones mediante sus identificadores.

Estas relaciones no implican acceso directo entre bases de datos.

La comunicación debe realizarse mediante:

- APIs;
- contratos internos;
- eventos;
- información transportada de forma controlada.

---

## 10. Propiedad de los datos

La propiedad de un dato determina qué servicio puede:

- crearlo;
- modificarlo;
- eliminarlo;
- validar sus reglas;
- garantizar su consistencia.

Los demás servicios pueden consumir información del dato mediante los mecanismos de integración definidos, pero no deben modificar directamente su representación persistida.

La regla general es:

> Un dato tiene un único propietario, aunque pueda ser utilizado por múltiples servicios.

---

## 11. Consistencia

La consistencia fuerte se mantiene dentro de cada contexto de persistencia.

Las operaciones que afectan datos pertenecientes a un mismo servicio pueden utilizar transacciones locales de PostgreSQL.

Entre servicios se evita depender de transacciones distribuidas.

Cuando una operación involucra más de un contexto pueden utilizarse:

- llamadas síncronas;
- eventos;
- consistencia eventual;
- información fijada al momento de una operación;
- mecanismos de compensación cuando sean necesarios.

La estrategia seleccionada depende de la naturaleza del proceso.

---

## 12. Datos de referencia

Cuando un servicio necesita información administrada por otro dominio, debe almacenar únicamente lo necesario.

Por ejemplo, un servicio puede conservar:

- identificador de usuario;
- identificador de institución;
- identificador de campaña.

No debe replicar automáticamente:

- información completa de la institución;
- credenciales del usuario;
- datos internos del dominio propietario.

Esto reduce duplicación y evita inconsistencias.

---

## 13. Clasificación de información

La información se clasifica según su sensibilidad y uso.

| Clasificación | Descripción |
|---|---|
| Personal | Información asociada a una persona identificada o identificable. |
| Sensible | Información que requiere controles reforzados de acceso y tratamiento. |
| Operacional restringida | Información interna necesaria para la operación del sistema. |
| Operacional agregada | Información consolidada utilizada para indicadores o reportes. |
| Auditoría | Información utilizada para trazabilidad de operaciones. |
| Prohibida | Información que se encuentra fuera del alcance autorizado del sistema. |

La clasificación específica de cada atributo debe mantenerse en la metadata y el diccionario de datos.

---

## 14. Datos prohibidos

El modelo de Red Vital no debe incorporar información que se encuentre fuera del alcance definido para el sistema.

Los datos clasificados como prohibidos:

- no deben capturarse;
- no deben almacenarse;
- no deben transmitirse;
- no deben registrarse en logs;
- no deben utilizarse en métricas.

La identificación específica de estos datos debe mantenerse alineada con los requisitos y restricciones del proyecto.

---

## 15. Integridad y trazabilidad

Los datos críticos deben conservar trazabilidad suficiente para identificar:

- actor;
- operación;
- recurso afectado;
- resultado;
- fecha y hora;
- contexto de la operación.

La trazabilidad no debe introducir exposición innecesaria de datos personales o sensibles.

Los logs y registros técnicos deben contener únicamente la información requerida para cumplir su función.

---

## 16. Evolución del modelo

Los cambios al modelo de datos deben realizarse mediante migraciones versionadas.

Cada cambio relevante debe permitir identificar:

- versión;
- contexto afectado;
- cambio realizado;
- impacto;
- dependencia con la aplicación;
- procedimiento de reversión cuando corresponda.

Los cambios estructurales que afecten decisiones arquitectónicas deben mantener trazabilidad con el ADR correspondiente.

---

## 17. Respaldo y recuperación

Cada persistencia debe poder respaldarse y recuperarse de manera independiente.

La estrategia de respaldo debe contemplar:

- generación de copias de seguridad;
- restauración;
- validación de integridad;
- pruebas periódicas;
- documentación del procedimiento.

La recuperación de una persistencia no debe requerir restaurar las demás bases cuando no sea necesario.

---

## 18. Relación con otros artefactos

El modelo general de datos se complementa con los siguientes artefactos:

- [Diseño de Datos](./DD.md)
- [Modelo Lógico](./logical-model/)
- [Modelo Físico](./physical-model/)
- [Metadata y Diccionario de Datos](./metadata/)
- [SAD](../architecture/SAD.md)
- [SDD](../architecture/SDD.md)
- [Integración](../integration/)
- [Requisitos](../requirements/)

---

## 19. Regla de mantenimiento

Este documento debe actualizarse cuando:

- se incorpore un nuevo dominio de datos;
- cambie el propietario de un dato;
- cambien las fronteras de persistencia;
- se agregue o elimine una entidad relevante;
- cambien las reglas de intercambio de información;
- cambie la clasificación de información;
- se modifique la estrategia de consistencia;
- una decisión arquitectónica afecte el modelo de datos.

El detalle físico debe mantenerse en los documentos especializados y no duplicarse innecesariamente en este archivo.