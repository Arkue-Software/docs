# Modelo Lógico de Datos — Red Vital

## 1. Propósito

Este documento describe el **modelo lógico de datos de Red Vital**.

Su objetivo es representar las principales entidades del sistema, sus relaciones, responsabilidades y reglas de asociación, independientemente de los detalles físicos de implementación en PostgreSQL.

El modelo lógico sirve como puente entre:

- los requisitos funcionales;
- la arquitectura;
- el modelo general de datos;
- el diseño físico;
- el diccionario de datos.

Este documento no define todavía detalles como:

- tipos físicos de PostgreSQL;
- índices;
- tablespaces;
- parámetros de almacenamiento;
- configuración específica del motor.

---

## 2. Principios del modelo lógico

El modelo lógico de Red Vital se rige por los siguientes principios:

1. Cada entidad pertenece a un único contexto funcional.
2. Cada dato tiene un único servicio propietario.
3. Las relaciones entre contextos se representan mediante identificadores y contratos.
4. No se modelan relaciones físicas directas entre bases pertenecientes a servicios distintos.
5. Las entidades deben reflejar conceptos del dominio y no decisiones técnicas innecesarias.
6. Las relaciones deben mantener trazabilidad con los requisitos funcionales.
7. La información sensible debe mantenerse limitada al mínimo necesario.
8. Los datos prohibidos no forman parte del modelo lógico.
9. Las entidades de auditoría deben permitir trazabilidad sin duplicar información innecesaria.
10. La evolución del modelo debe conservar separación entre contextos.

---

# 3. Contextos del modelo lógico

El modelo lógico se organiza en cuatro contextos principales de persistencia:

- Identidad;
- Institucional;
- Campañas;
- Donación.

El Servicio de Notificaciones puede mantener información técnica propia para el procesamiento de mensajes, pero no es propietario de las entidades principales de negocio.

---

# 4. Contexto de Identidad

El contexto de Identidad administra la información necesaria para autenticar, identificar y autorizar a los usuarios.

## 4.1 Entidades principales

### Usuario

Representa una persona con acceso autorizado al sistema.

Puede contener información lógica como:

- identificador;
- nombre;
- identificación;
- correo;
- estado;
- fecha de creación.

### Rol

Representa un conjunto de permisos o responsabilidades dentro del sistema.

Ejemplos conceptuales:

- administrador;
- operador;
- responsable institucional;
- usuario autorizado.

### UsuarioRol

Representa la asociación entre un usuario y uno o varios roles.

Permite mantener flexibilidad cuando un usuario puede asumir diferentes responsabilidades.

### Jurisdicción

Representa el ámbito territorial o institucional dentro del cual un usuario puede ejecutar determinadas operaciones.

### Sesión

Representa información asociada al ciclo de autenticación del usuario.

### Credencial

Representa la información necesaria para gestionar autenticación de forma segura.

La representación lógica no implica almacenar contraseñas en texto plano.

---

## 4.2 Relaciones principales

Las relaciones lógicas principales son:

- un Usuario puede tener uno o varios Roles;
- un Rol puede estar asociado a múltiples Usuarios;
- un Usuario puede estar asociado a una Jurisdicción;
- un Usuario puede mantener una o varias Sesiones;
- una Credencial pertenece a un Usuario.

De forma conceptual:

    Usuario
      |
      +----< UsuarioRol >---- Rol
      |
      +---- Jurisdicción
      |
      +----< Sesión
      |
      +---- Credencial

---

# 5. Contexto Institucional

El contexto Institucional administra la estructura organizacional y territorial utilizada por Red Vital.

## 5.1 Entidades principales

### Institución

Representa una organización registrada dentro del sistema.

### BancoDeSangre

Representa un banco de sangre perteneciente o asociado a una institución.

### Territorio

Representa una división territorial utilizada para organizar jurisdicciones y cobertura.

### TipoInstitución

Clasifica las instituciones cuando sea necesario.

### RelaciónInstitucional

Representa relaciones jerárquicas o administrativas entre instituciones cuando el dominio lo requiera.

---

## 5.2 Relaciones principales

De forma conceptual:

- una Institución puede tener uno o varios Bancos de Sangre;
- una Institución pertenece a un Territorio;
- un Territorio puede contener múltiples Instituciones;
- una Institución puede tener un Tipo de Institución;
- pueden existir relaciones entre instituciones cuando el dominio lo requiera.

Representación conceptual:

    Territorio
        |
        +----< Institución
                  |
                  +----< BancoDeSangre
                  |
                  +---- TipoInstitución
                  |
                  +----< RelaciónInstitucional

---

# 6. Contexto de Campañas

El contexto de Campañas administra la planificación, publicación y disponibilidad de campañas.

## 6.1 Entidades principales

### Campaña

Representa una campaña de donación organizada dentro del sistema.

Puede incluir información lógica como:

- identificador;
- nombre;
- descripción;
- fecha de inicio;
- fecha de finalización;
- estado;
- institución responsable.

### Jornada

Representa una fecha, franja o actividad específica dentro de una campaña.

### Cupo

Representa capacidad disponible dentro de una jornada o campaña.

### Reserva

Representa la asignación de un cupo a una persona o participante.

### EstadoCampaña

Representa el estado dentro del ciclo de vida de una campaña.

Ejemplos conceptuales:

- borrador;
- programada;
- publicada;
- activa;
- cerrada;
- cancelada.

---

## 6.2 Relaciones principales

Las principales relaciones son:

- una Institución puede organizar múltiples Campañas;
- una Campaña puede tener una o varias Jornadas;
- una Jornada puede tener múltiples Cupos;
- un Cupo puede generar una Reserva;
- una Campaña posee un Estado.

El identificador de Institución se mantiene como referencia lógica al contexto Institucional.

Representación conceptual:

    Institución (referencia)
            |
            +----< Campaña
                      |
                      +---- EstadoCampaña
                      |
                      +----< Jornada
                                |
                                +----< Cupo
                                          |
                                          +---- Reserva

---

# 7. Contexto de Donación

El contexto de Donación administra el ciclo operativo de las donaciones y unidades.

## 7.1 Entidades principales

### Donación

Representa el registro de una donación realizada dentro del sistema.

Puede relacionarse con:

- donante;
- campaña;
- institución;
- fecha;
- estado.

Las referencias externas se mantienen mediante identificadores y no mediante relaciones físicas entre bases.

### Unidad

Representa una unidad generada o administrada dentro del ciclo de donación.

### EstadoUnidad

Representa el estado actual de una unidad dentro de su ciclo de vida.

### HistorialUnidad

Registra las transiciones de estado y permite mantener trazabilidad.

### Inventario

Representa la disponibilidad de unidades dentro de un contexto institucional.

### MovimientoInventario

Representa entradas, salidas o cambios relevantes del inventario.

### Transferencia

Representa el traslado de unidades entre instituciones autorizadas.

### Escalamiento

Representa un proceso activado cuando las condiciones de disponibilidad o necesidad requieren una acción adicional.

---

## 7.2 Relaciones principales

De forma conceptual:

- una Donación puede generar una o varias Unidades;
- una Unidad tiene un Estado actual;
- una Unidad puede tener múltiples registros de Historial;
- una Unidad puede formar parte del Inventario;
- una Unidad puede participar en Movimientos;
- una Transferencia puede involucrar una o varias Unidades;
- un Escalamiento puede generarse a partir de condiciones del Inventario.

Representación conceptual:

    Donación
       |
       +----< Unidad
               |
               +---- EstadoUnidad
               |
               +----< HistorialUnidad
               |
               +----< MovimientoInventario
               |
               +---- Inventario
               |
               +----< Transferencia

    Inventario
       |
       +----< Escalamiento

---

# 8. Contexto de Notificaciones

El Servicio de Notificaciones puede mantener información técnica necesaria para procesar y entregar mensajes.

## 8.1 Entidades lógicas posibles

### Notificación

Representa una notificación que debe ser procesada o entregada.

### EventoProcesado

Permite registrar eventos ya consumidos y reducir el riesgo de procesamiento duplicado.

### IntentoEntrega

Representa cada intento de envío.

### ResultadoEntrega

Representa el resultado técnico del proceso de entrega.

---

## 8.2 Relaciones principales

De forma conceptual:

    Notificación
        |
        +----< IntentoEntrega
        |         |
        |         +---- ResultadoEntrega
        |
        +---- EventoProcesado

Estas entidades son técnicas y no convierten al Servicio de Notificaciones en propietario de información perteneciente a otros dominios.

---

# 9. Relaciones entre contextos

Las relaciones entre contextos deben mantenerse como referencias lógicas mediante identificadores.

Por ejemplo:

- una Campaña puede referenciar una Institución;
- una Donación puede referenciar una Campaña;
- una Donación puede referenciar un Usuario;
- una Transferencia puede referenciar instituciones de origen y destino.

Estas relaciones no deben implementarse como claves foráneas entre bases pertenecientes a servicios diferentes.

La relación conceptual puede existir, pero la integración física debe realizarse mediante contratos.

---

# 10. Identificadores entre contextos

Los identificadores utilizados para relacionar información entre dominios deben:

- ser estables;
- ser únicos dentro de su contexto;
- no exponer información sensible;
- mantenerse independientes de detalles internos de almacenamiento;
- poder transportarse mediante contratos o eventos.

Ejemplos:

- `usuario_id`;
- `institucion_id`;
- `campania_id`;
- `donacion_id`;
- `unidad_id`.

La nomenclatura física definitiva se define en el diseño físico y en el diccionario de datos.

---

# 11. Cardinalidades

Las cardinalidades deben definirse de acuerdo con las reglas del dominio.

Ejemplos conceptuales:

| Relación | Cardinalidad |
|---|---|
| Usuario — Rol | Muchos a muchos |
| Usuario — Sesión | Uno a muchos |
| Territorio — Institución | Uno a muchos |
| Institución — Banco de Sangre | Uno a muchos |
| Institución — Campaña | Uno a muchos |
| Campaña — Jornada | Uno a muchos |
| Jornada — Cupo | Uno a muchos |
| Donación — Unidad | Uno a muchos |
| Unidad — Historial | Uno a muchos |
| Unidad — Estado actual | Muchos a uno |
| Unidad — Movimiento de inventario | Uno a muchos |
| Notificación — Intento de entrega | Uno a muchos |

Estas cardinalidades deben validarse contra los requisitos funcionales y las reglas de negocio antes de considerarse definitivas.

---

# 12. Reglas de integridad lógica

El modelo lógico debe garantizar, como mínimo:

1. una entidad no debe existir fuera del contexto funcional que la administra;
2. los estados deben respetar transiciones válidas;
3. las referencias externas deben corresponder a identificadores válidos;
4. las relaciones obligatorias deben definirse explícitamente;
5. la eliminación de información debe respetar requisitos de auditoría y trazabilidad;
6. los datos sensibles deben limitarse al mínimo requerido;
7. los datos prohibidos no deben formar parte de ninguna entidad;
8. los identificadores externos no deben utilizarse como mecanismo para modificar datos ajenos al contexto.

---

# 13. Auditoría

Las operaciones relevantes deben permitir mantener trazabilidad.

La auditoría puede registrar, según corresponda:

- actor;
- operación;
- entidad afectada;
- identificador;
- resultado;
- fecha y hora;
- contexto.

La auditoría no debe replicar información sensible innecesaria.

---

# 14. Relación con el modelo físico

El modelo lógico define:

- entidades;
- responsabilidades;
- relaciones;
- cardinalidades;
- reglas conceptuales.

El modelo físico define posteriormente:

- tablas;
- columnas;
- tipos;
- claves primarias;
- claves foráneas internas;
- índices;
- restricciones;
- esquemas;
- detalles específicos de PostgreSQL.

El modelo físico debe derivarse del modelo lógico y no introducir relaciones que contradigan las fronteras definidas entre servicios.

---

# 15. Relación con metadata

Cada entidad y atributo lógico debe posteriormente documentarse en la metadata y el diccionario de datos.

La metadata debe permitir identificar:

- nombre;
- descripción;
- tipo;
- obligatoriedad;
- clasificación;
- restricciones;
- dominio propietario;
- reglas relevantes.

---

# 16. Diagrama lógico

El diagrama lógico debe representar visualmente:

- las principales entidades;
- sus relaciones;
- cardinalidades;
- agrupación por contexto;
- referencias entre dominios.

El diagrama debe diferenciar claramente:

- relaciones internas de un contexto;
- referencias externas entre servicios.

Cuando exista una relación conceptual entre dominios, esta no debe interpretarse automáticamente como una relación física entre bases de datos.

> **Pendiente:** incorporar el diagrama lógico actualizado de Red Vital.

---

# 17. Trazabilidad

Las entidades y relaciones del modelo lógico deben poder relacionarse con:

- requisitos funcionales;
- módulos;
- procesos de negocio;
- servicios;
- decisiones arquitectónicas;
- diseño físico;
- pruebas.

La cadena esperada es:

    Requisito
        ↓
    Entidad / relación lógica
        ↓
    Diseño físico
        ↓
    Implementación
        ↓
    Prueba

---

# 18. Relación con otros artefactos

- [Modelo General de Datos](./modelo-datos.md)
- [Diseño de Datos](./DD.md)
- [Modelo Físico](./physical-model.md)
- [Metadata y Diccionario de Datos](./metadata/)
- [SAD](../architecture/SAD.md)
- [SDD](../architecture/SDD.md)
- [Integración](../integration/)
- [Requisitos](../requirements/)

---

# 19. Regla de mantenimiento

Este documento debe actualizarse cuando:

- se agregue una entidad;
- se elimine una entidad;
- cambie la responsabilidad de un dominio;
- cambie una cardinalidad;
- cambie una relación entre contextos;
- se modifique una regla de integridad;
- cambie la clasificación de información;
- se incorpore un nuevo servicio propietario de datos.

El modelo lógico debe mantenerse alineado con el modelo general de datos, el diseño físico y el diccionario de datos.