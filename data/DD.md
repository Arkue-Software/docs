# Data Design Document (DD) — Red Vital

**Proyecto:** Red Vital  
**Versión:** 1.0  
**Estado:** Documento vivo  

---

# 1. Propósito

El **Data Design Document (DD)** describe el diseño de datos de Red Vital y la forma en que el modelo conceptual se transforma en estructuras lógicas y físicas implementables.

Este documento funciona como punto central de navegación para la documentación de datos y relaciona:

- modelo general de datos;
- diseño lógico;
- diseño físico;
- metadata;
- diccionario de datos;
- reglas de persistencia;
- propiedad de datos;
- clasificación de información;
- respaldo y recuperación.

El DD no duplica el contenido detallado de los artefactos especializados.

Cuando existe un documento específico para un tema, este documento presenta el contexto necesario y enlaza al artefacto correspondiente.

---

# 2. Alcance

El DD cubre:

- organización de los datos por dominio;
- propiedad de la información;
- separación de persistencias;
- tecnología de persistencia;
- modelo lógico;
- modelo físico;
- metadata;
- diccionario de datos;
- integridad;
- consistencia;
- migraciones;
- clasificación de información;
- respaldo;
- recuperación;
- trazabilidad.

La integración entre servicios y los contratos de intercambio se documentan de forma independiente.

---

# 3. Relación con otros artefactos

| Artefacto | Relación con el DD |
|---|---|
| Modelo de Datos | Define la visión general de los dominios, propietarios y relaciones conceptuales. |
| Modelo Lógico | Define entidades, relaciones, cardinalidades y reglas del dominio. |
| Modelo Físico | Traduce el modelo lógico a estructuras PostgreSQL. |
| Diccionario de Datos | Define el significado, clasificación y restricciones de los atributos. |
| SAD | Define los principios arquitectónicos de persistencia y propiedad de datos. |
| SDD | Relaciona el diseño de datos con el diseño general de la solución. |
| Requisitos | Proporciona las necesidades funcionales que originan entidades y relaciones. |
| Integración | Define cómo se intercambia información entre servicios. |
| Testing | Valida reglas, restricciones e integridad del modelo implementado. |

---

# 4. Principios de diseño de datos

El diseño de datos de Red Vital se rige por los siguientes principios:

1. Cada dato tiene un único servicio propietario.
2. Cada contexto mantiene persistencia independiente.
3. Ningún servicio accede directamente a las tablas de otro servicio.
4. No existen claves foráneas físicas entre bases pertenecientes a servicios diferentes.
5. La integración se realiza mediante contratos o eventos.
6. Las referencias entre dominios se realizan mediante identificadores.
7. Las reglas de integridad deben aplicarse en el contexto propietario.
8. Las migraciones deben mantenerse versionadas.
9. Las credenciales deben ser independientes por servicio.
10. Los datos sensibles deben limitarse al mínimo necesario.
11. Los datos prohibidos no deben formar parte del modelo.
12. La documentación debe mantenerse sincronizada con la implementación.

---

# 5. Tecnología de persistencia

Red Vital utiliza **PostgreSQL** como tecnología principal de persistencia relacional.

La utilización de PostgreSQL no implica una base compartida.

La arquitectura sigue el principio de **propiedad de persistencia por servicio**.

Se contemplan los siguientes contextos principales:

| Persistencia | Servicio propietario |
|---|---|
| Identidad | Servicio de Identidad |
| Institucional | Servicio Institucional |
| Campañas | Servicio de Campañas |
| Donación | Servicio de Donación |

El Servicio de Notificaciones puede mantener persistencia técnica propia si la implementación lo requiere, pero no accede directamente a las bases de los demás servicios.

---

# 6. Modelo general de datos

El modelo general describe:

- dominios de información;
- servicios propietarios;
- reglas de relación entre contextos;
- principios de aislamiento;
- clasificación general de información.

El detalle se mantiene en:

[Consultar Modelo de Datos](./modelo-datos.md)

---

# 7. Diseño lógico

El diseño lógico representa la estructura del dominio independientemente de los detalles físicos de PostgreSQL.

Incluye:

- entidades;
- relaciones;
- cardinalidades;
- referencias entre contextos;
- reglas de integridad lógica;
- responsabilidades por dominio.

Las principales áreas del modelo lógico corresponden a:

- Identidad;
- Institucional;
- Campañas;
- Donación;
- Notificaciones.

El detalle se mantiene en:

[Consultar Modelo Lógico](./logical-model.md)

---

# 8. Diseño físico

El diseño físico traduce el modelo lógico a estructuras implementables en PostgreSQL.

Incluye:

- bases de datos;
- tablas;
- columnas;
- tipos de datos;
- claves primarias;
- claves foráneas internas;
- índices;
- constraints;
- migraciones;
- roles de acceso.

Las relaciones físicas solo existen dentro del mismo contexto de persistencia.

Las referencias entre servicios se mantienen como identificadores sin clave foránea entre bases.

El detalle se encuentra en:

[Consultar Modelo Físico](./physical-model.md)

---

# 9. Metadata

La metadata describe el significado y características de los datos utilizados por Red Vital.

Para cada atributo debe permitir identificar, cuando corresponda:

- nombre;
- descripción;
- tipo lógico;
- obligatoriedad;
- clasificación;
- restricciones;
- servicio propietario;
- relación con otras entidades.

La metadata sirve como fuente común para:

- desarrollo;
- arquitectura;
- pruebas;
- integración;
- documentación.

---

# 10. Diccionario de datos

El diccionario de datos constituye el catálogo detallado de las entidades y atributos del sistema.

Incluye:

- nombre del atributo;
- significado funcional;
- tipo lógico;
- obligatoriedad;
- clasificación;
- restricciones.

Los tipos físicos definitivos y detalles PostgreSQL se mantienen en el modelo físico.

El diccionario se encuentra en:

[Consultar Diccionario de Datos](./metadata/data-dictionary.md)

---

# 11. Propiedad de datos

La propiedad de datos determina qué servicio puede:

- crear;
- modificar;
- eliminar;
- validar;
- mantener la consistencia de un dato.

La regla general es:

> Un dato puede ser utilizado por varios servicios, pero tiene un único propietario.

Los demás servicios deben consumir la información mediante contratos o eventos.

---

# 12. Referencias entre servicios

Las relaciones entre contextos se implementan mediante identificadores.

Ejemplos:

- `usuario_id`;
- `institucion_id`;
- `campania_id`;
- `donacion_id`.

Estas referencias:

- no crean claves foráneas entre bases;
- no permiten acceso directo a otra persistencia;
- deben validarse mediante contratos cuando sea necesario;
- deben conservar únicamente la información mínima requerida.

---

# 13. Consistencia

La consistencia fuerte se mantiene dentro de cada servicio mediante transacciones locales de PostgreSQL.

Entre servicios se evita depender de transacciones distribuidas.

Según el caso, pueden utilizarse:

- consistencia local;
- consistencia eventual;
- eventos;
- procesamiento asíncrono;
- compensaciones;
- información fijada al momento de una operación.

La estrategia debe elegirse según las reglas del dominio.

---

# 14. Integridad

La integridad se garantiza mediante:

- claves primarias;
- claves foráneas internas;
- restricciones `NOT NULL`;
- restricciones `UNIQUE`;
- restricciones `CHECK`;
- reglas de negocio en el servicio propietario;
- validación de referencias externas mediante contratos.

Las reglas que involucren varios servicios no deben implementarse mediante relaciones físicas entre bases.

---

# 15. Clasificación de información

La información manejada por Red Vital puede clasificarse como:

| Clasificación | Descripción |
|---|---|
| Personal | Información asociada a una persona identificada o identificable. |
| Sensible | Información que requiere controles reforzados. |
| Operacional restringida | Información interna utilizada por los procesos del sistema. |
| Operacional agregada | Información consolidada para indicadores o reportes. |
| Auditoría | Información requerida para trazabilidad. |
| Prohibida | Información fuera del alcance autorizado del sistema. |

La clasificación detallada por atributo se mantiene en el diccionario de datos.

---

# 16. Datos prohibidos

Los datos clasificados como prohibidos:

- no deben modelarse;
- no deben persistirse;
- no deben incluirse en contratos;
- no deben aparecer en logs;
- no deben utilizarse en métricas;
- no deben transportarse mediante eventos.

Cualquier modificación de esta regla requiere revisión formal del alcance y de la arquitectura.

---

# 17. Migraciones

Los cambios al modelo físico deben ejecutarse mediante migraciones versionadas.

Cada migración debe permitir identificar:

- servicio;
- versión;
- cambio;
- impacto;
- dependencia con la aplicación;
- estrategia de reversión cuando aplique.

Los cambios manuales sin trazabilidad deben evitarse.

---

# 18. Roles y permisos

Cada persistencia debe aplicar mínimo privilegio.

Como mínimo se contemplan:

### Rol de administración

Utilizado para tareas excepcionales de administración.

### Rol de migraciones

Utilizado para aplicar cambios de esquema.

### Rol de aplicación

Utilizado por el servicio durante la operación normal.

El rol de aplicación debe tener únicamente los permisos necesarios.

---

# 19. Respaldo y recuperación

Cada persistencia debe poder respaldarse y restaurarse de manera independiente.

La estrategia debe contemplar:

- generación de backups;
- restauración;
- validación de integridad;
- pruebas periódicas;
- documentación del procedimiento.

La restauración de una base no debe requerir restaurar todas las demás.

---

# 20. Trazabilidad

El diseño de datos debe mantener trazabilidad con:

- requisitos;
- servicios;
- entidades;
- atributos;
- decisiones arquitectónicas;
- contratos;
- pruebas.

La relación general es:

    Requisito
        ↓
    Entidad lógica
        ↓
    Modelo físico
        ↓
    Metadata
        ↓
    Implementación
        ↓
    Prueba

---

# 21. Artefactos del DD

La documentación de datos se organiza de la siguiente manera:

    data/
    ├── DD.md
    ├── modelo-datos.md
    ├── logical-model.md
    ├── physical-model.md
    └── metadata/
        └── data-dictionary.md

Cada artefacto tiene una responsabilidad específica:

| Artefacto | Responsabilidad |
|---|---|
| `DD.md` | Documento central e índice del diseño de datos. |
| `modelo-datos.md` | Visión general de dominios y propiedad de datos. |
| `logical-model.md` | Entidades, relaciones y cardinalidades. |
| `physical-model.md` | Implementación física en PostgreSQL. |
| `metadata/data-dictionary.md` | Significado y características de los atributos. |

---

# 22. Pendientes

Se encuentran pendientes de completar o validar:

- diagrama lógico actualizado;
- diagrama físico actualizado;
- validación final del modelo contra la implementación real;
- revisión de tipos físicos definitivos;
- revisión de índices según patrones reales de consulta;
- sincronización del diccionario con los esquemas implementados.

---

# 23. Relación con integración

El DD define cómo se almacenan y organizan los datos.

La documentación de integración define cómo esos datos se intercambian entre servicios.

Por esta razón, los contratos, APIs y eventos no se mantienen dentro del DD.

La documentación correspondiente se encuentra en:

[Consultar Integración](../integration/)

---

# 24. Navegación

- [Modelo de Datos](./modelo-datos.md)
- [Modelo Lógico](./logical-model.md)
- [Modelo Físico](./physical-model.md)
- [Diccionario de Datos](./metadata/data-dictionary.md)
- [SAD](../architecture/SAD.md)
- [SDD](../architecture/SDD.md)
- [Integración](../integration/)
- [Requisitos](../requirements/)

---

# 25. Regla de mantenimiento

El DD debe actualizarse cuando:

- cambie la estrategia de persistencia;
- se incorpore un nuevo contexto de datos;
- cambie el propietario de una entidad;
- se modifique el modelo lógico;
- se modifique el modelo físico;
- cambie la clasificación de información;
- se modifique una regla de integridad;
- cambien los mecanismos de respaldo o recuperación;
- una decisión arquitectónica afecte el diseño de datos.

La documentación debe representar el estado vigente del modelo y evitar duplicar información que ya se mantenga en un artefacto especializado.