# Datos — Red Vital

Esta sección contiene la documentación relacionada con el diseño, organización, persistencia y definición de los datos de **Red Vital**.

Su propósito es centralizar los artefactos que describen:

- el modelo general de datos;
- la propiedad de la información;
- el diseño lógico;
- el diseño físico;
- la metadata;
- el diccionario de datos;
- las reglas de persistencia;
- la clasificación de información;
- la trazabilidad del modelo.

La documentación de datos se mantiene como una **fuente viva de referencia** y debe evolucionar junto con la implementación.

---

## 1. Data Design Document (DD)

El **DD** constituye el documento central de diseño de datos de Red Vital.

Describe y relaciona:

- principios de diseño de datos;
- persistencia;
- propiedad de datos;
- diseño lógico;
- diseño físico;
- metadata;
- integridad;
- consistencia;
- clasificación;
- migraciones;
- respaldo y recuperación.

[Consultar Data Design Document](./DD.md)

---

## 2. Modelo general de datos

El modelo general de datos describe la distribución conceptual de la información entre los distintos contextos funcionales.

Define:

- dominios de datos;
- servicio propietario;
- separación de responsabilidades;
- relaciones entre contextos;
- reglas de intercambio;
- principios de aislamiento.

[Consultar Modelo de Datos](./modelo-datos.md)

---

## 3. Modelo lógico

El modelo lógico representa las principales entidades, relaciones y cardinalidades del sistema sin depender todavía de detalles específicos de PostgreSQL.

Incluye:

- entidades;
- relaciones;
- cardinalidades;
- reglas de integridad lógica;
- referencias entre contextos;
- separación por dominio.

[Consultar Modelo Lógico](./logical-model.md)

---

## 4. Modelo físico

El modelo físico traduce el modelo lógico a estructuras implementables en PostgreSQL.

Incluye:

- bases de datos;
- tablas;
- columnas;
- tipos;
- claves primarias;
- claves foráneas internas;
- índices;
- constraints;
- migraciones;
- roles y permisos.

[Consultar Modelo Físico](./physical-model.md)

---

## 5. Metadata y diccionario de datos

La metadata documenta el significado y características de los datos utilizados por Red Vital.

El diccionario de datos define, para cada entidad y atributo:

- nombre;
- descripción;
- tipo lógico;
- obligatoriedad;
- clasificación;
- restricciones;
- relación con otros elementos.

[Consultar Diccionario de Datos](./metadata/data-dictionary.md)

---

## 6. Organización de la sección

La documentación de datos se organiza de la siguiente manera:

    data/
    ├── README.md
    ├── DD.md
    ├── modelo-datos.md
    ├── logical-model.md
    ├── physical-model.md
    └── metadata/
        └── data-dictionary.md

Cada archivo tiene una responsabilidad específica y debe evitar duplicar información que corresponda a otro artefacto.

---

## 7. Persistencia

Red Vital utiliza **PostgreSQL** como tecnología principal de persistencia.

La arquitectura sigue el principio de propiedad de persistencia por servicio.

Se contemplan los siguientes contextos:

| Persistencia | Servicio propietario |
|---|---|
| Identidad | Servicio de Identidad |
| Institucional | Servicio Institucional |
| Campañas | Servicio de Campañas |
| Donación | Servicio de Donación |

El Servicio de Notificaciones puede mantener persistencia técnica propia cuando sea necesario, pero no accede directamente a las bases de otros servicios.

---

## 8. Principios de datos

La documentación de datos debe respetar los siguientes principios:

1. Cada dato tiene un único propietario.
2. Cada servicio administra su propia persistencia.
3. Ningún servicio accede directamente a las tablas de otro.
4. No existen claves foráneas físicas entre bases de servicios diferentes.
5. La integración entre dominios se realiza mediante contratos o eventos.
6. Las referencias entre contextos se realizan mediante identificadores.
7. Las migraciones deben mantenerse versionadas.
8. Las credenciales deben ser independientes por servicio.
9. Los datos sensibles deben limitarse al mínimo necesario.
10. Los datos prohibidos no deben formar parte del modelo.
11. El diseño físico debe mantenerse alineado con el modelo lógico.
12. La metadata debe mantenerse sincronizada con la implementación.

---

## 9. Relación con arquitectura

La arquitectura define las reglas estructurales que condicionan el diseño de datos.

Entre ellas:

- propiedad exclusiva de datos;
- persistencia independiente;
- separación entre servicios;
- mínimo privilegio;
- aislamiento;
- consistencia local;
- integración mediante contratos.

La relación principal se mantiene con:

- [SAD](../architecture/SAD.md)
- [SDD](../architecture/SDD.md)
- [ADRs](../architecture/adrs/README.md)

---

## 10. Relación con integración

La sección de datos define cómo se organiza y persiste la información.

La sección de integración define cómo esa información se intercambia entre componentes.

Por esta razón:

- los datos se documentan aquí;
- los contratos y eventos se documentan en integración;
- las APIs no forman parte del DD;
- las referencias entre dominios deben mantenerse alineadas con los contratos vigentes.

[Consultar Integración](../integration/)

---

## 11. Relación con requisitos

Las entidades y relaciones del modelo deben poder rastrearse hasta los requisitos que justifican su existencia.

Los cambios funcionales pueden producir cambios en:

- entidades;
- atributos;
- relaciones;
- cardinalidades;
- restricciones;
- clasificación de información.

[Consultar Requisitos](../requirements/)

---

## 12. Relación con testing

Las reglas del modelo deben poder verificarse mediante pruebas.

Entre ellas:

- restricciones;
- obligatoriedad;
- unicidad;
- integridad;
- reglas de relación;
- aislamiento entre servicios;
- clasificación de información;
- respaldo y recuperación.

[Consultar Testing](../testing/)

---

## 13. Pendientes

Actualmente deben completarse o validarse:

- diagrama lógico actualizado;
- diagrama físico actualizado;
- validación final contra los esquemas reales;
- revisión definitiva de tipos PostgreSQL;
- revisión de índices según patrones reales de consulta;
- sincronización completa del diccionario de datos con la implementación.

---

## 14. Regla de mantenimiento

La documentación de datos debe actualizarse cuando:

- se cree una nueva entidad;
- se elimine una entidad;
- se modifique un atributo;
- cambie una cardinalidad;
- cambie el servicio propietario;
- cambie la estrategia de persistencia;
- cambie una restricción;
- cambie la clasificación de información;
- se modifique el diseño físico;
- una decisión arquitectónica afecte los datos.

La sección `data` debe representar en todo momento el modelo vigente de Red Vital.

---

## 15. Navegación rápida

| Artefacto | Acceso |
|---|---|
| Data Design Document | [DD](./DD.md) |
| Modelo general | [Modelo de Datos](./modelo-datos.md) |
| Modelo lógico | [Logical Model](./logical-model.md) |
| Modelo físico | [Physical Model](./physical-model.md) |
| Diccionario de datos | [Data Dictionary](./metadata/data-dictionary.md) |
| SAD | [Arquitectura](../architecture/SAD.md) |
| SDD | [Diseño](../architecture/SDD.md) |
| Integración | [Integration](../integration/) |
| Requisitos | [Requirements](../requirements/) |