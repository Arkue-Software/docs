# ADR-018: Estrategia de comunicación asíncrona y evaluación de Kafka

- **Estado:** Propuesta
- **Versión:** 1.0
- **Fecha:** 2026-10-05
- **Autor:** Sara — Arquitecta de Software
- **Responsable de implementar:** Equipo de desarrollo e infraestructura
- **Fecha de revisión:** 2026-10-05
- **Ítems relacionados:** HU-319, T-319.1, T-319.2, T-319.3
- **ADR relacionado:** ADR-014, ADR-017

## Historial de cambios

| Versión | Fecha | Descripción del cambio | Autor |
|---|---|---|---|
| 1.0 | 2026-10-05 | Creación inicial de la decisión sobre comunicación asíncrona y evaluación de Kafka | Sara |

---

## Contexto

ADR-017 formaliza una arquitectura de microservicios para Red Vital.

La separación entre servicios implica que las capacidades del sistema deben comunicarse mediante contratos explícitos y no mediante acceso directo a las bases de datos de otros microservicios.

Las interacciones identificadas en la arquitectura presentan dos necesidades diferentes.

Por una parte, existen operaciones que requieren una respuesta inmediata.

Un ejemplo vigente es la interacción entre Donación y Campañas durante el registro de una donación. T-306.4 define un adaptador hacia Campañas con un tiempo máximo de espera de 3 segundos, resultado tipado y degradación controlada.

Por otra parte, existen hechos del dominio que pueden ser procesados posteriormente por otros servicios sin bloquear la operación que los originó.

Entre los eventos identificados se encuentran:

- `donation.completed`;
- `blood-unit.created`;
- `blood-unit.state-changed`;
- `transfusion.completed`;
- `stock.below-threshold`.

T-319.1 realizó un benchmark entre HTTP síncrono, Transactional Outbox, RabbitMQ y Apache Kafka.

El análisis concluyó que estas alternativas no resuelven exactamente el mismo problema y que Red Vital requiere una combinación de mecanismos según el tipo de interacción.

Por esta razón, ADR-018 debe definir la estrategia actual de comunicación y determinar si Kafka resulta justificado para el alcance vigente.

---

## Opciones consideradas

### 1. Utilizar únicamente HTTP entre microservicios

Todas las interacciones entre servicios se realizan mediante llamadas síncronas HTTP.

#### Ventajas

- Implementación sencilla.
- Tecnologías conocidas por el equipo.
- Integración directa con los contratos OpenAPI existentes.
- Menor infraestructura adicional.
- Fácil trazabilidad de una solicitud y su respuesta.

#### Desventajas

- Introduce acoplamiento temporal.
- El consumidor depende de la disponibilidad inmediata del proveedor.
- Una cadena de llamadas puede propagar fallos entre servicios.
- Dificulta el fan-out hacia varios consumidores.
- No proporciona retención ni replay de eventos.
- No resulta adecuado para hechos que pueden procesarse posteriormente.

Esta alternativa se considera adecuada únicamente para las interacciones que realmente requieren una respuesta inmediata.

---

### 2. HTTP + Transactional Outbox sin broker externo

Las operaciones síncronas se mantienen mediante HTTP y los eventos se almacenan en una bandeja de salida dentro de la base de datos del microservicio productor.

Un proceso posterior sería responsable de procesarlos directamente.

#### Ventajas

- Reduce el riesgo de perder eventos.
- Mantiene consistencia entre el cambio de negocio y el registro del evento.
- Requiere menos infraestructura externa.

#### Desventajas

- La bandeja de salida no constituye por sí misma un sistema completo de distribución de mensajes.
- Obliga a construir mecanismos adicionales de entrega.
- Complica la incorporación de varios consumidores independientes.
- Traslada al equipo responsabilidades que normalmente resuelve un broker.

Esta alternativa no resuelve completamente las necesidades de comunicación asíncrona de la arquitectura.

---

### 3. HTTP + Transactional Outbox + RabbitMQ

Las operaciones que requieren respuesta inmediata utilizan HTTP.

Los hechos del dominio que no requieren respuesta inmediata se registran mediante Transactional Outbox y posteriormente se publican a RabbitMQ.

Conceptualmente:

```text
Operación de negocio
        │
        ├── cambio de datos
        │
        └── evento en Outbox
                  │
                  ▼
             Publicador
                  │
                  ▼
              RabbitMQ
                  │
          ┌───────┼────────┐
          ▼       ▼        ▼
      Servicio Servicio Servicio
```

#### Ventajas

- Reduce el acoplamiento temporal.
- Proporciona mensajería asíncrona.
- Permite acknowledgements y reintentos.
- Permite dead-letter queues.
- Ofrece mecanismos flexibles de routing.
- Es adecuado para notificaciones y procesamiento de trabajos.
- Presenta una complejidad operacional moderada.
- Transactional Outbox reduce el riesgo de perder eventos después de confirmar una transacción de negocio.

#### Desventajas

- Añade infraestructura adicional.
- Requiere diseñar exchanges, colas y bindings.
- Los consumidores deben ser idempotentes.
- La reproducción histórica de eventos no es su caso de uso principal.
- Un evento destinado a varios consumidores puede requerir diferentes colas.

Esta alternativa cubre las necesidades actuales de comunicación asíncrona sin introducir la complejidad completa de una plataforma de event streaming.

---

### 4. HTTP + Transactional Outbox + Apache Kafka

Las operaciones síncronas continúan utilizando HTTP.

Los eventos se almacenan mediante Transactional Outbox y posteriormente se publican en topics de Kafka.

Conceptualmente:

```text
Operación de negocio
        │
        ├── cambio de datos
        │
        └── evento en Outbox
                  │
                  ▼
             Publicador
                  │
                  ▼
                Kafka
                  │
          ┌───────┼────────┐
          ▼       ▼        ▼
      Consumer Consumer Consumer
       Group    Group    Group
```

#### Ventajas

- Retención configurable de eventos.
- Permite replay.
- Soporta múltiples consumidores independientes.
- Facilita incorporar nuevos consumidores en el futuro.
- Escala mediante particiones.
- Resulta apropiado para arquitecturas fuertemente orientadas a eventos.
- Puede soportar necesidades futuras de analítica o generación de proyecciones.

#### Desventajas

- Mayor complejidad operacional.
- Mayor curva de aprendizaje.
- Requiere administrar topics, particiones, claves y consumer groups.
- Requiere mayor esfuerzo de observabilidad.
- El orden debe analizarse por partición.
- Los consumidores necesitan estrategias de idempotencia.
- Introduce capacidades que actualmente no son indispensables para el incremento implementado.
- Incrementa el costo de operación y mantenimiento de la plataforma.

Para el alcance actual de Red Vital, estas capacidades adicionales no justifican todavía el costo operacional asociado.

---

## Trade-off evaluado

El benchmark realizado en T-319.1 muestra que RabbitMQ y Kafka permiten reducir el acoplamiento temporal entre microservicios, pero ofrecen capacidades distintas.

Kafka proporciona ventajas importantes cuando los eventos requieren:

- retención prolongada;
- replay;
- múltiples consumidores independientes;
- incorporación frecuente de nuevos consumidores;
- procesamiento de grandes flujos de eventos;
- reconstrucción de proyecciones a partir del historial.

Sin embargo, estas capacidades aumentan la complejidad operacional y de desarrollo.

El alcance actual de Red Vital requiere principalmente:

- desacoplar procesos que no necesitan respuesta inmediata;
- soportar notificaciones;
- permitir reintentos;
- evitar pérdida de mensajes;
- distribuir eventos entre servicios;
- manejar fallos de consumidores sin bloquear al productor.

RabbitMQ cubre estas necesidades con una complejidad menor.

Por esta razón, el equipo acepta renunciar temporalmente a las capacidades avanzadas de replay y event streaming de Kafka a cambio de una solución más sencilla de implementar, operar y probar dentro del alcance actual del proyecto.

Transactional Outbox complementa esta decisión al reducir el riesgo de inconsistencia entre las transacciones locales y la publicación posterior de eventos.

---

## Decisión

Red Vital adopta una estrategia híbrida de comunicación.

### Comunicación síncrona

HTTP se utilizará cuando una operación requiera una respuesta inmediata para continuar.

Las llamadas síncronas entre microservicios deberán utilizarse únicamente cuando exista una necesidad funcional de solicitud-respuesta.

Estas interacciones deberán definir, según corresponda:

- timeout;
- manejo de errores;
- degradación controlada;
- trazabilidad;
- reintentos limitados.

El uso de HTTP no autoriza acceso directo entre bases de datos.

---

### Comunicación asíncrona

Para hechos del dominio cuyo procesamiento no requiere respuesta inmediata se utilizará mensajería asíncrona.

La línea base adoptada será:

**Transactional Outbox + RabbitMQ.**

Transactional Outbox se utilizará cuando una operación de negocio deba producir un evento de manera confiable.

El cambio de negocio y el evento pendiente deberán almacenarse dentro de la misma transacción local.

Posteriormente, un publicador enviará el evento hacia RabbitMQ.

RabbitMQ será responsable de transportar los mensajes hacia los consumidores correspondientes.

---

### Apache Kafka

Apache Kafka **no se adopta en la línea base actual**.

Kafka se incorpora al Tech Radar con estado:

**Evaluar.**

La decisión no implica que Kafka haya sido descartado.

Se considera una alternativa válida para una evolución futura de Red Vital cuando las necesidades de la arquitectura justifiquen sus capacidades adicionales.

---

## Disparador para reconsiderar Kafka

Kafka deberá volver a evaluarse cuando se presente **al menos una** de las siguientes condiciones:

1. un evento de dominio necesite ser reproducido posteriormente para reconstruir el estado o una proyección;
2. se requiera conservar eventos durante periodos prolongados independientemente de que hayan sido consumidos;
3. un mismo flujo de eventos necesite ser procesado de manera independiente por múltiples grupos de consumidores;
4. se incorporen capacidades de analítica que requieran consumir el historial de eventos;
5. el número o diversidad de consumidores haga difícil mantener la topología de colas y bindings mediante RabbitMQ;
6. el volumen de eventos requiera una estrategia de particionamiento y procesamiento distribuido que exceda razonablemente la solución vigente;
7. se adopte una arquitectura donde el log de eventos se convierta en un elemento central para reconstrucción, auditoría o generación de proyecciones.

La aparición de cualquiera de estas condiciones no implica automáticamente adoptar Kafka.

Implica volver a realizar una evaluación arquitectónica considerando las métricas y necesidades reales del sistema en ese momento.

---

## Justificación

La decisión busca mantener un equilibrio entre desacoplamiento, confiabilidad y complejidad operacional.

Red Vital necesita comunicación asíncrona para evitar que todos sus procesos dependan de la disponibilidad inmediata de otros microservicios.

RabbitMQ proporciona las capacidades requeridas actualmente para:

- entrega de mensajes;
- desacoplamiento temporal;
- routing;
- acknowledgements;
- reintentos;
- tratamiento de mensajes fallidos;
- procesamiento asíncrono;
- notificaciones.

Transactional Outbox complementa RabbitMQ al permitir que la intención de publicar un evento se registre junto con la transacción de negocio correspondiente.

Kafka proporciona capacidades superiores para retención, replay y event streaming, pero estas ventajas todavía no corresponden a necesidades verificadas del incremento actual.

Adoptarlo en este momento introduciría complejidad operacional sin evidencia suficiente de que dicha complejidad produzca un beneficio proporcional.

Mantener Kafka en evaluación permite conservarlo como alternativa futura sin incorporarlo prematuramente.

---

## Costos de la decisión

La adopción de esta estrategia implica costos técnicos y operacionales.

### RabbitMQ

Se requiere:

- desplegar y configurar el broker;
- administrar exchanges y colas;
- definir bindings;
- establecer mecanismos de retry;
- configurar dead-letter queues cuando corresponda;
- monitorizar profundidad de colas y consumidores;
- gestionar conexiones y credenciales.

---

### Transactional Outbox

Se requiere:

- definir la estructura de la bandeja de salida;
- registrar los eventos dentro de las transacciones locales;
- implementar un publicador;
- controlar eventos publicados y pendientes;
- manejar posibles publicaciones duplicadas;
- diseñar consumidores idempotentes.

---

### Comunicación HTTP

Las llamadas síncronas que permanezcan deberán implementar:

- timeout;
- tratamiento de indisponibilidad;
- degradación cuando corresponda;
- trazabilidad de llamadas;
- manejo controlado de reintentos.

---

### Costo de no adoptar Kafka

Mientras Kafka no se adopte:

- no se contará con un log distribuido de eventos como pieza central de la arquitectura;
- el replay histórico de eventos no será una capacidad nativa del mecanismo seleccionado;
- agregar numerosos consumidores independientes puede aumentar la configuración requerida en RabbitMQ;
- futuras necesidades de streaming pueden exigir migración o coexistencia con Kafka.

Estos costos se consideran aceptables para el alcance actual.

---

## Consecuencias

A partir de esta decisión:

- HTTP continuará utilizándose para interacciones que requieran respuesta inmediata.
- Las interacciones síncronas deberán mantenerse limitadas para evitar cadenas de dependencias entre microservicios.
- Los hechos del dominio que puedan procesarse posteriormente deberán favorecer comunicación asíncrona.
- RabbitMQ será el broker de mensajería de la línea base actual.
- Transactional Outbox deberá utilizarse cuando sea necesario mantener consistencia entre una transacción de negocio y la publicación de un evento.
- Los consumidores deberán diseñarse para tolerar mensajes duplicados cuando corresponda.
- Ningún mecanismo de mensajería autoriza compartir bases de datos entre microservicios.
- Kafka permanecerá en evaluación.
- El Tech Radar deberá registrar el disparador definido en este ADR para Kafka.
- La entrada correspondiente a RabbitMQ deberá actualizarse para reflejar su papel dentro de la estrategia aprobada.
- El SAD, SDD, diagramas C4 y documentación de infraestructura deberán mantenerse coherentes con esta estrategia.
- La topología concreta de exchanges, colas, eventos y consumidores deberá documentarse en el diseño de integración correspondiente.

---

## Relación con ADR-014

ADR-018 desarrolla y actualiza las decisiones de comunicación que previamente aparecían asociadas a ADR-014.

ADR-014 mantiene vigencia para las decisiones que no entren en conflicto con ADR-018.

Para las interacciones entre microservicios, ADR-018 prevalece específicamente en:

- clasificación entre comunicación síncrona y asíncrona;
- utilización de HTTP para solicitud-respuesta;
- utilización de mensajería para eventos;
- adopción de RabbitMQ en la línea base;
- utilización de Transactional Outbox;
- evaluación futura de Kafka;
- criterios que disparan una nueva evaluación de Kafka.

---

## Estado de la decisión

Este ADR permanece en estado **Propuesta** hasta su revisión y aprobación mediante T-319.3 en la Mesa de Arquitectura.

Una vez aprobado:

- su estado deberá cambiar a `Aceptada`;
- deberá registrarse la aprobación en el historial de cambios;
- podrán actualizarse las entradas correspondientes del Tech Radar mediante T-326.2.