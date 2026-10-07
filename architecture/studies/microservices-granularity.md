# Granularidad final de microservicios — Red Vital

- **Versión:** 1.2
- **Fecha:** 2026-10-05
- **Responsable:** Sara — Arquitecta de Software
- **Tarea:** T-318.2
- **Historia relacionada:** HU-318
- **ADR relacionado:** ADR-017 — Adopción de una arquitectura de microservicios
- **Estado:** Propuesta para aprobación en Mesa de Arquitectura

---

# 1. Propósito

Este documento complementa ADR-017 y define la granularidad arquitectónica objetivo de Red Vital.

Su propósito es establecer:

1. cuáles componentes constituyen microservicios;
2. cuáles son los límites de responsabilidad de cada microservicio;
3. qué datos pertenecen a cada servicio;
4. cuáles componentes forman parte de la solución sin ser microservicios;
5. cómo deben comunicarse los microservicios sin introducir dependencias innecesarias;
6. la diferencia entre la arquitectura objetivo y el incremento implementado en el Sprint 3;
7. qué documentos y entradas del Tech Radar deben actualizarse como consecuencia de ADR-017.

La aprobación formal de esta definición se realizará en T-318.3 mediante la Mesa de Arquitectura.

---

# 2. Antecedentes

Las primeras versiones de la arquitectura de Red Vital evolucionaron desde un monolito modular hacia una arquitectura distribuida.

ADR-003 estableció la necesidad de abandonar el monolito modular, pero dejó abierta la selección del estilo arquitectónico distribuido concreto.

Posteriormente se definió una arquitectura compuesta por cinco servicios independientes:

- Identidad;
- Institucional;
- Campañas;
- Donación;
- Notificaciones.

También se estableció la separación de los datos de negocio mediante cuatro bases principales con propiedad exclusiva.

ADR-017 propone formalizar que esta estructura corresponde a una **arquitectura de microservicios** y eliminar la ambigüedad existente entre las expresiones “arquitectura distribuida”, “servicios independientes” y “microservicios”.

La arquitectura objetivo no debe confundirse con el alcance técnico de cada sprint.
El incremento integrado contempla servicios y contratos por separado. Para la
persistencia se aprovisionan cuatro bases aisladas: `db_identidad`,
`db_institucional`, `db_campana` y `db_donacion`. En el estado actual de la
integracion, Identidad y Campañas tienen migraciones SQL versionadas
disponibles; las bases de Institucional y Donación se crean vacias hasta que
sus responsables entreguen esquemas aprobados. Esta limitacion de esquema no
comparte ni fusiona sus bases.

---

# 3. Granularidad arquitectónica objetivo propuesta

Red Vital propone una arquitectura objetivo compuesta por **cinco microservicios**:

| Microservicio | Responsabilidad principal | Persistencia principal |
|---|---|---|
| **Identidad** | Autenticación, sesiones, tokens, roles y capacidades relacionadas con identidad y autorización | `db_identidad` |
| **Institucional** | Instituciones, bancos de sangre, estructura territorial y datos institucionales necesarios para operar la red | `db_institucional` |
| **Campañas** | Creación, consulta, publicación, cierre y gestión de campañas de donación | `db_campana` |
| **Donación** | Ciclo de vida de la donación y de las unidades: registro, tamizaje, inventario, despacho y trazabilidad | `db_donacion` |
| **Notificaciones** | Procesamiento desacoplado de comunicaciones y avisos generados por otros servicios | Sin base de negocio propia obligatoria |

---

# 4. Microservicio de Identidad

El Servicio de Identidad administra las capacidades relacionadas con autenticación, sesión e identidad de los usuarios de Red Vital.

Entre sus responsabilidades se encuentran:

- inicio de sesión;
- renovación de sesión;
- cierre de sesión;
- emisión y administración de tokens;
- publicación de claves necesarias para verificar tokens;
- gestión de roles;
- información relacionada con autorización cuya propiedad haya sido asignada a Identidad;
- suministro de información de identidad a otros componentes mediante contratos explícitos.

## Propiedad de datos

Identidad es propietario exclusivo de:

`db_identidad`

Ningún otro microservicio debe consultar directamente las tablas de esta base.

---

# 5. Microservicio Institucional

El Servicio Institucional administra la información correspondiente a las instituciones que hacen parte de Red Vital y a la estructura territorial utilizada por el sistema.

Entre sus responsabilidades se encuentran:

- instituciones participantes;
- bancos de sangre;
- relaciones institucionales;
- territorios y jurisdicciones;
- jerarquías territoriales;
- información institucional utilizada por las demás capacidades del sistema.

Institucional mantiene su autonomía respecto de Donación, Campañas e Identidad.

Los demás microservicios no deben acceder directamente a su base de datos.

Cuando sea necesario intercambiar información institucional, la interacción deberá realizarse mediante contratos de servicio o mecanismos de mensajería aprobados.

## Propiedad de datos

Institucional es propietario exclusivo de:

`db_institucional`

---

# 6. Microservicio de Campañas

El Servicio de Campañas administra el ciclo de vida de las campañas de donación.

Entre sus responsabilidades se encuentran:

- creación de campañas;
- consulta de campañas;
- consulta de detalle;
- publicación;
- cierre;
- cambios de estado;
- restricciones asociadas a la institución o jurisdicción correspondiente.

Otros microservicios pueden utilizar información de las campañas mediante contratos explícitos o mediante eventos.

No pueden acceder directamente a `db_campana`.

## Propiedad de datos

Campañas es propietario exclusivo de:

`db_campana`

---

# 7. Microservicio de Donación

El Servicio de Donación administra el proceso operativo relacionado con una donación y el ciclo de vida de las unidades resultantes.

Entre sus responsabilidades se encuentran:

- registro de donaciones;
- asociación de una donación con una campaña cuando corresponda;
- ingreso a tamizaje;
- veredicto apto/no apto;
- control de transiciones de estado;
- gestión de unidades;
- consulta de existencias;
- vencimiento de unidades;
- despacho;
- disposición final;
- trazabilidad del ciclo de vida.

Estas capacidades permanecen dentro de un mismo microservicio porque corresponden a un mismo ciclo funcional y comparten reglas operativas estrechamente relacionadas.

## Propiedad de datos

Donación es propietario exclusivo de:

`db_donacion`

---

# 8. Microservicio de Notificaciones

El Servicio de Notificaciones procesa de manera desacoplada las comunicaciones que deben enviarse a los usuarios.

Entre los posibles casos se encuentran:

- notificaciones relacionadas con campañas;
- recuperación de elegibilidad;
- recordatorios;
- avisos generados por procesos de negocio;
- otras comunicaciones definidas funcionalmente.

Notificaciones no debe convertirse en una dependencia síncrona obligatoria para completar procesos principales.

Por ejemplo, la indisponibilidad temporal del servicio de Notificaciones no debería impedir completar una operación de negocio cuyo resultado no dependa funcionalmente de la entrega inmediata de la notificación.

## Persistencia

Notificaciones no requiere una base de datos de negocio propia dentro de la línea base arquitectónica.

Puede utilizar almacenamiento técnico, colas o mecanismos de persistencia operacional si una decisión arquitectónica posterior lo requiere.

---

# 9. Arquitectura objetivo frente al incremento del Sprint 3

Es necesario distinguir entre arquitectura objetivo y arquitectura implementada durante un incremento particular.

## 9.1 Arquitectura objetivo

Red Vital se estructura mediante:

**5 microservicios**

```text
Identidad
Institucional
Campañas
Donación
Notificaciones
```

y **4 bases de datos principales**:

```text
Identidad
    │
    └── db_identidad

Institucional
    │
    └── db_institucional

Campañas
    │
    └── db_campana

Donación
    │
    └── db_donacion

Notificaciones
    │
    └── sin base de negocio propia obligatoria
```

---

## 9.2 Incremento del Sprint 3

El Sprint 3 implementa únicamente:

```text
Identidad
Donación
Campañas
```

y sus bases:

```text
db_identidad
db_donacion
db_campana
```

El alcance de los contratos y servicios desplegados en cada ambiente puede
variar, pero no cambia la propiedad de datos. El Compose local del repositorio
`databases` aprovisiona cuatro instancias: `db_identidad`, `db_campana`,
`db_donacion` y `db_institucional`. Identidad y Campañas tienen migraciones
versionadas; Donación e Institucional quedan vacías hasta recibir sus
esquemas. Aprovisionar `db_institucional` no afirma que su servicio ya esté
integrado ni desplegado. Notificaciones no tiene base de negocio propia.

---

# 10. Propiedad y aislamiento de datos

Red Vital adopta el principio de **propiedad exclusiva de los datos por microservicio**.

Se establecen las siguientes reglas:

1. Cada base de datos tiene un único microservicio propietario.
2. Ningún microservicio puede utilizar las credenciales de la base de otro.
3. Ningún microservicio consulta directamente las tablas de otro.
4. No se permiten `joins` entre bases de microservicios diferentes.
5. Las referencias entre dominios se realizan mediante identificadores.
6. El intercambio de información se realiza mediante contratos de servicio o eventos.
7. Las migraciones pertenecen exclusivamente al servicio propietario de la base.
8. Una base de datos no constituye un mecanismo de comunicación entre microservicios.

Por ejemplo, esta relación no está permitida:

```text
Donación ─────► db_institucional
```

Tampoco:

```text
Donación ─► db_donacion ─► Institucional
```

La persistencia debe permanecer encapsulada dentro de cada microservicio.

---

# 11. Comunicación entre microservicios

La arquitectura busca minimizar el acoplamiento entre los cinco microservicios.

Los microservicios pueden requerir intercambiar información, pero dicha comunicación no implica compartir implementación ni persistencia.

Red Vital distingue dos mecanismos generales de comunicación.

## 11.1 Comunicación síncrona

La comunicación síncrona puede utilizarse cuando una operación necesita una respuesta inmediata para continuar.

Debe reservarse para interacciones en las que exista una necesidad real de solicitud-respuesta.

Las llamadas síncronas entre microservicios no deben utilizarse innecesariamente, porque generan acoplamiento temporal: el servicio consumidor depende de que el servicio proveedor se encuentre disponible en ese momento.

---

## 11.2 Comunicación asíncrona

Cuando una operación no requiere una respuesta inmediata, se debe favorecer la comunicación mediante eventos o mensajería.

Conceptualmente:

```text
Microservicio productor
          │
          │ publica evento
          ▼
 Bus de eventos / mensajería
          │
          ▼
Microservicio consumidor
```

Este mecanismo permite reducir el acoplamiento temporal entre los servicios.

Un servicio productor puede completar su responsabilidad sin requerir que todos los consumidores se encuentren disponibles en ese instante.

---

# 12. Bus de eventos

La arquitectura contempla un mecanismo de mensajería para soportar la comunicación asíncrona entre microservicios.

Conceptualmente:

```text
               ┌────────────────────────────┐
               │                            │
               │  Bus de eventos /          │
               │  mensajería                │
               │                            │
               └─────────────┬──────────────┘
                             │
        ┌────────────────────┼─────────────────────┐
        │                    │                     │
        ▼                    ▼                     ▼
    Donación             Campañas           Institucional
        │                    │                     │
        └────────────────────┼─────────────────────┘
                             │
                             ▼
                       Notificaciones
```

Un microservicio puede actuar como:

- productor de eventos;
- consumidor de eventos;
- o ambas cosas.

La existencia del bus permite que las relaciones entre microservicios no tengan que representarse como dependencias directas de servicio a servicio.

---

# 13. Tecnología de mensajería

Este documento no define todavía la tecnología concreta que implementará el bus de eventos.

La selección debe formalizarse mediante ADR-018.

Entre las alternativas evaluadas pueden encontrarse:

- HTTP síncrono;
- mecanismo de bandeja;
- RabbitMQ;
- Kafka.

Por tanto, mientras ADR-018 no haya sido aprobado, los diagramas asociados a ADR-017 deben utilizar una denominación tecnológica neutral:

**Bus de eventos / mensajería**

Una vez se apruebe ADR-018, el nombre podrá sustituirse por la tecnología seleccionada.

---

# 14. Relación con el API Gateway

El API Gateway constituye el punto de entrada controlado para las operaciones que deben ser consumidas de manera síncrona desde la aplicación Web u otros clientes autorizados.

Conceptualmente:

```text
                         Usuarios
                            │
                            ▼
                     Aplicación Web
                            │
                            ▼
                       API Gateway
                            │
             ┌──────────────┼──────────────┐
             │              │              │
             ▼              ▼              ▼
        Identidad        Donación       Campañas
```

Esta representación corresponde principalmente al incremento del Sprint 3.

No significa que únicamente estos tres servicios puedan existir detrás del Gateway.

Si en una evolución posterior existen operaciones de Institucional que deban ser consumidas directamente por la Web u otro cliente, el Gateway podrá enrutar dichas operaciones hacia Institucional.

Notificaciones, por su naturaleza, se espera principalmente como consumidor de eventos y no necesita exponer necesariamente operaciones al usuario mediante el Gateway.

---

# 15. Vista arquitectónica simplificada

La arquitectura objetivo debe interpretarse como dos planos de interacción:

- interacción síncrona de entrada;
- comunicación asíncrona entre capacidades.

```text
                              USUARIOS
                                 │
                                 ▼
                         Aplicación Web
                                 │
                                 ▼
                           API Gateway
                                 │
                ┌────────────────┼────────────────┐
                │                │                │
                ▼                ▼                ▼
           Identidad         Donación         Campañas
                │                │                │
                ▼                ▼                ▼
         db_identidad      db_donacion      db_campana


                          Institucional
                               │
                               ▼
                        db_institucional


             ┌───────────────────────────────────┐
             │                                   │
             │       Bus de eventos /            │
             │       mensajería                  │
             │                                   │
             └────────────────┬──────────────────┘
                              │
        ┌─────────────────────┼──────────────────────┐
        │                     │                      │
        ▼                     ▼                      ▼
    Donación              Campañas             Institucional
        │                     │                      │
        └─────────────────────┼──────────────────────┘
                              │
                              ▼
                       Notificaciones
```

El diagrama no implica que únicamente Donación, Campañas e Institucional puedan usar el bus.

Identidad también podrá publicar o consumir eventos cuando una necesidad arquitectónica lo justifique.

La definición exacta de productores, consumidores, tópicos y eventos pertenece al diseño de integración y deberá mantenerse coherente con ADR-018.

---

# 16. Componentes que no constituyen microservicios

## 16.1 API Gateway

El API Gateway es un componente transversal.

Entre sus responsabilidades se encuentran:

- recepción de solicitudes;
- enrutamiento;
- validaciones transversales;
- aplicación de controles asociados a rutas y roles.

No representa por sí mismo una capacidad de negocio.

Por tanto, no forma parte del conteo de cinco microservicios.

---

## 16.2 Aplicación Web

La aplicación Web representa la capa de presentación.

Consume las capacidades del sistema a través del Gateway.

No constituye un microservicio.

---

## 16.3 Proxy de borde

El proxy de borde pertenece a la infraestructura de exposición y comunicación.

No constituye un microservicio de negocio.

---

## 16.4 Trabajos de migración

Los trabajos de migración administran la creación y evolución de los esquemas de las bases.

No constituyen microservicios.

Cada migración debe pertenecer al microservicio propietario de la base correspondiente.

---

# 17. Criterios para considerar un componente como microservicio

Dentro de Red Vital, un componente puede considerarse microservicio cuando cumple, como mínimo, con los siguientes criterios:

- representa una capacidad funcional delimitada;
- tiene responsabilidades identificables;
- puede construirse independientemente;
- puede desplegarse independientemente;
- posee contratos explícitos;
- no necesita acceder directamente a la persistencia interna de otro servicio;
- administra sus propios datos cuando requiere persistencia;
- puede evolucionar internamente conservando la compatibilidad de sus contratos;
- su fallo no debe obligar al reinicio completo de la solución.

La existencia de un repositorio, proceso o contenedor independiente no es suficiente por sí sola para considerar un componente como microservicio.

---

# 18. Principios de desacoplamiento

La granularidad definida por ADR-017 establece los siguientes principios:

### Independencia funcional

Cada microservicio representa una responsabilidad funcional clara.

### Independencia de datos

Cada servicio controla exclusivamente sus datos.

### Independencia de despliegue

Un microservicio debe poder evolucionar y desplegarse sin requerir el despliegue completo del sistema.

### Contratos explícitos

Los detalles internos de un servicio no constituyen su interfaz.

La interacción se realiza mediante contratos definidos y versionados.

### Preferencia por comunicación desacoplada

Cuando no exista una necesidad de respuesta inmediata, debe favorecerse la comunicación basada en eventos.

### Tolerancia a fallos

El fallo de un microservicio no debe provocar automáticamente la indisponibilidad completa del sistema.

---

# 19. Documentos afectados por ADR-017

## 19.1 SDD V2 y Modelo C4

**Afectado directamente.**

Debe:

- representar los cinco microservicios de la arquitectura objetivo;
- diferenciar arquitectura objetivo e incremento implementado;
- representar cada microservicio junto con su persistencia;
- distinguir Gateway, Web, mensajería y componentes de infraestructura;
- evitar representar bases de datos compartidas;
- mostrar los mecanismos de integración sin sugerir acceso directo entre persistencias.

Tareas relacionadas:

- T-320.1;
- T-320.2;
- T-320.3;
- T-320.4;
- T-320.5;
- T-320.6.

---

## 19.2 SAD V3

**Afectado directamente.**

Debe:

- declarar formalmente microservicios como estilo arquitectónico;
- registrar ADR-017;
- describir los cinco microservicios;
- diferenciar arquitectura objetivo del incremento del Sprint 3;
- eliminar terminología contradictoria;
- explicar los principios de independencia y desacoplamiento.

Tarea relacionada:

- T-321.1.

---

## 19.3 DD V3

**Afectado directamente.**

Debe conservar las cuatro bases principales:

- `db_identidad`;
- `db_institucional`;
- `db_campana`;
- `db_donacion`.

Además debe:

- identificar el propietario de cada base;
- impedir accesos directos entre esquemas de diferentes microservicios;
- representar las relaciones mediante contratos o identificadores;
- mantener coherencia con ADR-017.

Tarea relacionada:

- T-322.2.

---

## 19.4 Documento de Infraestructura V2

**Afectado directamente.**

Debe:

- utilizar la terminología de microservicios;
- distinguir arquitectura objetivo y despliegue actual;
- representar servicios, bases, Gateway, Web, borde y mensajería como componentes distintos;
- evitar confundir el número de microservicios objetivo con el número desplegado durante Sprint 3.

Tarea relacionada:

- T-323.2.

---

## 19.5 SRS V4

**Afectado en terminología.**

Debe utilizar la terminología arquitectónica aprobada cuando relacione capacidades funcionales con servicios.

Tarea relacionada:

- T-324.3.

---

# 20. Tech Radar afectado

## Entrada 62 — Monolito modular

**Estado:** Evitar.

Debe conservar su estado y actualizar su justificación para señalar que ADR-017 formalizó la adopción de microservicios.

---

## Entrada 64 — Microservicios

**Estado:** Adoptar.

Debe actualizarse para referenciar ADR-017 y declarar la granularidad objetivo:

- Identidad;
- Institucional;
- Campañas;
- Donación;
- Notificaciones.

---

# 21. Entradas relacionadas del Tech Radar

También deben revisarse por consistencia, sin que ADR-017 implique necesariamente un cambio de categoría:

- Docker;
- Prometheus + Grafana;
- Modelo C4;
- ADR;
- tecnologías relacionadas con integración y mensajería.

La tecnología definitiva utilizada para mensajería deberá permanecer alineada con ADR-018.

---

# 22. Artefactos técnicos afectados

La granularidad definida debe reflejarse también en:

- contratos OpenAPI;
- configuración del API Gateway;
- definición de eventos;
- configuración del mecanismo de mensajería;
- Docker/Compose;
- trabajos de migración;
- pipelines de CI/CD;
- pruebas de integración;
- pruebas de resiliencia;
- diagramas de despliegue.

---

# 23. Decisiones fuera del alcance

Este documento no decide:

- Kafka frente a RabbitMQ;
- la definición final de tópicos;
- la estructura de cada evento;
- qué operaciones específicas serán síncronas;
- Kubernetes;
- una service mesh;
- particionar Donación en servicios adicionales;
- fusionar Institucional con Donación;
- convertir Gateway en microservicio;
- convertir la aplicación Web en microservicio.

Estas decisiones deben justificarse de manera independiente cuando corresponda.

---

# 24. Resultado de T-318.2

La granularidad objetivo de Red Vital queda definida como:

> **La propuesta de arquitectura objetivo se compone de cinco microservicios: Identidad, Institucional, Campañas, Donación y Notificaciones. Los cuatro primeros poseen dominios de datos persistentes diferenciados y propiedad exclusiva de sus respectivas bases. Notificaciones constituye un microservicio independiente orientado principalmente al procesamiento desacoplado de comunicaciones y no requiere una base de negocio propia dentro de la línea base.**

Además:

> **Los microservicios no utilizarán las bases de datos de otros servicios como mecanismo de integración. Las interacciones se realizarán mediante contratos explícitos y se favorecerá el intercambio asíncrono mediante eventos cuando una operación no requiera respuesta inmediata, reduciendo así el acoplamiento temporal entre servicios.**

Para el incremento de bases de datos integrado:

> **Se aprovisionan cuatro bases independientes, una para cada servicio con datos persistentes. En esta entrega solo `db_identidad` y `db_campana` reciben las migraciones versionadas disponibles; `db_donacion` y `db_institucional` permanecen vacias. Notificaciones no requiere una base de negocio propia.**

La tecnología concreta utilizada para la comunicación asíncrona se formalizará mediante ADR-018.

La aprobación definitiva de esta granularidad y la actualización correspondiente de ADR-003 y ADR-014 se realizará mediante T-318.3 en la Mesa de Arquitectura.