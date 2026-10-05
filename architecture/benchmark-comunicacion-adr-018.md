# Benchmark de comunicación entre microservicios — ADR-018

- **Versión:** 1.0
- **Fecha:** 2026-10-05
- **Responsable:** Sara — Arquitecta de Software
- **Tarea:** T-319.1
- **Historia relacionada:** HU-319
- **ADR relacionado:** ADR-014, futuro ADR-018
- **Estado:** Análisis

---

## 1. Propósito

Este documento clasifica las principales interacciones entre componentes de Red Vital y compara alternativas de comunicación para soportar la elaboración de ADR-018.

Las alternativas consideradas son:

1. HTTP síncrono.
2. Patrón Transactional Outbox o bandeja de salida.
3. RabbitMQ.
4. Apache Kafka.

Este documento no adopta todavía una tecnología definitiva.

La decisión arquitectónica se documentará posteriormente en ADR-018 mediante T-319.2.

---

## 2. Contexto

Red Vital adopta una arquitectura de microservicios y requiere evitar dependencias directas entre las bases de datos privadas de los servicios.

La comunicación entre microservicios puede requerir dos comportamientos diferentes:

- solicitud-respuesta inmediata;
- propagación asíncrona de hechos ocurridos en el dominio.

La documentación vigente contempla ambos tipos de interacción.

Por ejemplo, durante el registro de una donación, el Servicio de Donación puede requerir consultar información del Servicio de Campañas y aplicar una degradación controlada cuando Campañas no se encuentre disponible.

Al mismo tiempo, existen hechos del dominio que pueden ser utilizados posteriormente por otros servicios sin necesidad de bloquear la operación original.

Entre los eventos documentados se encuentran:

- `donation.completed`;
- `blood-unit.created`;
- `blood-unit.state-changed`;
- `transfusion.completed`;
- `stock.below-threshold`.

Por esta razón, no se espera que un único mecanismo de comunicación reemplace necesariamente todos los demás.

---

## 3. Clasificación de las aristas

Las interacciones se clasifican según la necesidad de obtener una respuesta inmediata.

### 3.1 Aristas síncronas

Una arista se clasifica como síncrona cuando el servicio solicitante necesita el resultado para completar correctamente la operación actual.

#### Donación → Campañas

T-306.4 define un adaptador desde Donación hacia Campañas con:

- plazo máximo de 3 segundos;
- resultado tipado;
- degradación controlada;
- `campania_id` nulo cuando Campañas no se encuentre disponible.

T-312.3 establece que el Servicio de Campañas expone el detalle de la campaña para ser consumido mediante un token de servicio de Donación.

Esta interacción corresponde a un modelo solicitud-respuesta.

Conceptualmente:

```text
Donación ───── HTTP ─────► Campañas
   │                         │
   ▼                         ▼
db_donacion             db_campana
```

La indisponibilidad de Campañas no debe provocar acceso directo desde Donación hacia `db_campana`.

La degradación debe resolverse desde el propio Servicio de Donación.

---

### 3.2 Aristas asíncronas

Una interacción se considera candidata a comunicación asíncrona cuando el servicio productor puede completar su responsabilidad sin esperar a que todos los consumidores procesen el resultado.

La documentación vigente registra eventos como:

| Evento | Productor lógico | Posibles consumidores |
|---|---|---|
| `donation.completed` | Donación | Campañas, Notificaciones u otros consumidores |
| `blood-unit.created` | Donación | Servicios interesados en el alta de la unidad |
| `blood-unit.state-changed` | Donación | Consumidores de trazabilidad o inventario |
| `transfusion.completed` | Donación | Consumidores relacionados con inventario o trazabilidad |
| `stock.below-threshold` | Dominio de inventario | Campañas, Notificaciones |

Estas interacciones no deberían requerir una llamada síncrona obligatoria cuando el consumidor no necesita participar en la transacción original.

Conceptualmente:

```text
Donación
    │
    │ publica evento
    ▼
Bus de eventos
    │
    ├────► Campañas
    │
    └────► Notificaciones
```

---

## 4. HTTP síncrono

HTTP utiliza un modelo solicitud-respuesta.

El consumidor envía una solicitud y espera una respuesta del servicio proveedor.

### Ventajas

- Modelo sencillo y ampliamente conocido.
- Amplio soporte en frameworks y herramientas.
- Fácil de probar y observar.
- Adecuado cuando se requiere una respuesta inmediata.
- Compatible con los contratos OpenAPI ya definidos.
- Requiere poca infraestructura adicional.

### Desventajas

- Produce acoplamiento temporal entre servicios.
- El consumidor depende de que el proveedor esté disponible durante la operación.
- Las cadenas de llamadas pueden propagar fallos.
- Los reintentos deben diseñarse explícitamente.
- No proporciona por sí solo persistencia ni reproducción de eventos.
- El fan-out hacia múltiples consumidores requiere llamadas adicionales.

### Adecuación para Red Vital

HTTP es adecuado para operaciones que requieren respuesta inmediata.

Ejemplo:

```text
Donación ──HTTP──► Campañas
```

cuando Donación necesita consultar el detalle de una campaña antes de completar determinada operación.

No debería utilizarse por defecto para propagar hechos que pueden procesarse posteriormente.

---

## 5. Transactional Outbox

El patrón Transactional Outbox resuelve el problema de consistencia entre una operación de negocio y la publicación posterior de un evento.

Puede ocurrir el siguiente escenario:

1. el microservicio modifica correctamente sus datos;
2. la transacción de base de datos se confirma;
3. falla la publicación del evento.

El resultado sería una operación confirmada sin el evento correspondiente.

Transactional Outbox evita este problema almacenando el evento dentro de la misma transacción local utilizada para modificar los datos de negocio.

Posteriormente, un proceso independiente publica los eventos pendientes.

Conceptualmente:

```text
                  ┌── Datos de negocio
Microservicio ────┤
                  └── Outbox
                         │
                         ▼
                    Publicador
                         │
                         ▼
                  Broker de eventos
```

### Ventajas

- Reduce el riesgo de perder eventos.
- Mantiene consistencia entre el cambio de negocio y el evento generado.
- No requiere transacciones distribuidas entre base de datos y broker.
- Permite reintentar publicaciones pendientes.
- Es compatible con la propiedad privada de datos por microservicio.

### Desventajas

- Requiere una tabla o estructura Outbox.
- Requiere un proceso de publicación.
- Puede producir eventos duplicados.
- Los consumidores deben ser idempotentes.
- No reemplaza por sí solo un broker de mensajería.

### Adecuación para Red Vital

El patrón resulta especialmente relevante para operaciones críticas del Servicio de Donación.

Ejemplo:

```text
Registrar donación
       │
       ├── guardar Donación
       ├── guardar Unidad
       └── guardar Evento en Outbox
                    │
                    ▼
               Publicador
                    │
                    ▼
             Broker de eventos
```

De esta manera se evita confirmar una donación y perder posteriormente el evento asociado.

---

## 6. RabbitMQ

RabbitMQ es un broker de mensajería orientado al intercambio y entrega de mensajes mediante colas.

Permite utilizar:

- exchanges;
- colas;
- bindings;
- acknowledgements;
- publisher confirms;
- reintentos;
- dead-letter queues;
- estrategias de enrutamiento.

### Ventajas

- Modelo de colas relativamente sencillo.
- Permite comunicación asíncrona.
- Routing flexible.
- Soporta confirmaciones de productor y consumidor.
- Permite dead-letter queues.
- Adecuado para colas de trabajo.
- Menor complejidad conceptual que una plataforma completa de event streaming.

### Desventajas

- El modelo principal se orienta al procesamiento y entrega de mensajes.
- La reproducción histórica de eventos no es su fortaleza principal.
- Varios consumidores independientes pueden requerir configuraciones de colas diferentes.
- Añade infraestructura operativa.
- Requiere manejo de redelivery e idempotencia.

### Adecuación para Red Vital

RabbitMQ es una alternativa adecuada cuando la prioridad principal es:

- desacoplar servicios;
- entregar mensajes de manera confiable;
- manejar reintentos;
- procesar trabajos asíncronos;
- implementar notificaciones;
- enrutar mensajes hacia consumidores específicos.

Ejemplo:

```text
Donación
   │
   ▼
RabbitMQ
   │
   ▼
Notificaciones
```

---

## 7. Apache Kafka

Apache Kafka utiliza un modelo basado en topics, particiones y consumidores.

Los productores escriben eventos en topics y los consumidores procesan dichos eventos manteniendo su posición mediante offsets.

Los eventos pueden conservarse durante un periodo configurable incluso después de haber sido consumidos.

### Ventajas

- Persistencia de eventos.
- Posibilidad de replay.
- Modelo natural para arquitecturas orientadas a eventos.
- Permite múltiples consumidores independientes.
- Facilita agregar nuevos consumidores en el futuro.
- Escalabilidad mediante particiones.
- Mantiene orden dentro de una partición.
- Puede soportar posteriormente analítica y nuevas proyecciones.

### Desventajas

- Mayor complejidad operacional.
- Mayor complejidad conceptual para el equipo.
- Requiere diseñar correctamente topics, particiones y claves.
- El orden global entre diferentes particiones no está garantizado.
- Los consumidores deben gestionar offsets e idempotencia.
- Puede resultar excesivo si el volumen de eventos y consumidores es reducido.
- Añade infraestructura adicional al prototipo académico.

### Adecuación para Red Vital

Kafka es especialmente atractivo cuando un mismo evento debe ser utilizado por varios consumidores independientes.

Ejemplo:

```text
                         ┌──► Campañas
                         │
Donación ──► Kafka ──────┼──► Notificaciones
                         │
                         └──► Analítica
```

El productor no necesita conocer todos los consumidores actuales o futuros.

La retención de eventos también permite que un consumidor temporalmente indisponible continúe procesando eventos posteriormente.

---

## 8. Comparación cualitativa

La siguiente escala se utiliza únicamente como apoyo al análisis arquitectónico:

- **1:** muy bajo.
- **2:** bajo.
- **3:** medio.
- **4:** alto.
- **5:** muy alto.

> Las puntuaciones corresponden a una evaluación cualitativa para Red Vital y no a resultados de pruebas de rendimiento.

| Criterio | HTTP | Outbox | RabbitMQ | Kafka |
|---|---:|---:|---:|---:|
| Simplicidad de implementación | 5 | 3 | 4 | 2 |
| Solicitud-respuesta inmediata | 5 | 1 | 2 | 1 |
| Bajo acoplamiento temporal | 1 | 4 | 5 | 5 |
| Entrega confiable de eventos | 2 | 5 | 5 | 5 |
| Fan-out a múltiples consumidores | 2 | 3 | 4 | 5 |
| Replay de eventos | 1 | 3 | 2 | 5 |
| Escalabilidad de eventos | 2 | 3 | 4 | 5 |
| Routing de mensajes | 2 | 2 | 5 | 4 |
| Facilidad operacional | 5 | 3 | 4 | 2 |
| Adecuación a event streaming | 1 | 3 | 3 | 5 |

---

## 9. Interpretación del benchmark

### HTTP

Es la alternativa más adecuada para operaciones que requieren una respuesta inmediata.

Su principal desventaja es el acoplamiento temporal entre servicios.

### Transactional Outbox

No debe interpretarse como sustituto directo de Kafka o RabbitMQ.

Su función es garantizar que una operación de negocio y el evento correspondiente queden registrados de manera consistente.

Puede utilizarse conjuntamente con cualquiera de los brokers.

Ejemplo:

```text
Donación
   │
   ▼
Transactional Outbox
   │
   ▼
Kafka / RabbitMQ
```

### RabbitMQ

Es adecuado cuando la necesidad principal consiste en:

- mensajería confiable;
- procesamiento asíncrono;
- routing;
- reintentos;
- colas de trabajo;
- notificaciones.

Su costo operativo y conceptual puede ser menor que el de una plataforma orientada a event streaming.

### Kafka

Ofrece mayores ventajas cuando los eventos representan información durable que:

- debe conservarse;
- puede necesitar replay;
- tiene varios consumidores independientes;
- puede ser consumida posteriormente por nuevos componentes;
- puede alimentar analítica o nuevas proyecciones.

Su principal costo es la mayor complejidad tecnológica y operacional.

---

## 10. Clasificación recomendada por tipo de interacción

| Tipo de interacción | Mecanismo recomendado para evaluación |
|---|---|
| Usuario o Web → microservicio mediante Gateway | HTTP |
| Consulta inmediata entre microservicios | HTTP con timeout y degradación |
| Cambio de dominio que debe generar evento | Transactional Outbox + broker |
| Notificaciones | Mensajería asíncrona |
| Evento con múltiples consumidores | RabbitMQ o Kafka según necesidades |
| Procesamiento de trabajo único | RabbitMQ |
| Eventos que requieren retención y replay | Kafka |
| Integración directa mediante bases de datos | No permitida |

---

## 11. Aplicación a Red Vital

No se recomienda reemplazar todas las comunicaciones por un único mecanismo.

La arquitectura puede combinar diferentes estrategias según el tipo de interacción.

### Operaciones síncronas

```text
Web
 │
 ▼
Gateway
 │
 ▼
Microservicio
```

Cuando exista una necesidad explícita de respuesta inmediata:

```text
Microservicio A ──HTTP──► Microservicio B
```

Estas interacciones deben incluir:

- timeout;
- manejo de errores;
- degradación cuando aplique;
- trazabilidad;
- límites claros para evitar cadenas de dependencias síncronas.

### Operaciones asíncronas

Para hechos del dominio que no requieren respuesta inmediata:

```text
Microservicio
      │
      ▼
Transactional Outbox
      │
      ▼
Broker de eventos
      │
 ┌────┼─────┐
 ▼    ▼     ▼
S1    S2    S3
```

El broker concreto se determinará mediante ADR-018.

---

## 12. Criterios para ADR-018

ADR-018 deberá considerar, como mínimo:

- necesidad real de replay;
- cantidad de productores;
- cantidad de consumidores;
- necesidad de varios consumidores independientes;
- volumen esperado de eventos;
- necesidad de routing;
- necesidad de retención;
- complejidad operacional;
- costo de infraestructura;
- experiencia del equipo;
- capacidad de observabilidad;
- impacto sobre pruebas;
- facilidad de despliegue;
- crecimiento esperado de la arquitectura.

---

## 13. Conclusión

HTTP, Transactional Outbox, RabbitMQ y Kafka no resuelven exactamente el mismo problema.

HTTP resulta adecuado para las interacciones en las que se necesita una respuesta inmediata.

Transactional Outbox permite mantener consistencia entre una transacción de negocio y la publicación posterior de un evento, pero requiere complementarse con un mecanismo de mensajería.

RabbitMQ ofrece un modelo de mensajería confiable y relativamente sencillo, especialmente adecuado para colas de trabajo, notificaciones, routing y procesamiento asíncrono.

Kafka ofrece ventajas adicionales cuando se requiere retención de eventos, replay, múltiples consumidores independientes y evolución futura hacia una arquitectura más orientada a eventos.

Por tanto, ADR-018 deberá determinar si las necesidades actuales y futuras de Red Vital justifican la adopción de Kafka o si RabbitMQ cubre suficientemente el alcance esperado con menor complejidad operacional.

---

## 14. Insumo para T-319.2

T-319.2 deberá utilizar este benchmark para redactar ADR-018 y establecer:

1. la alternativa seleccionada;
2. los costos y trade-offs aceptados;
3. qué interacciones permanecerán síncronas;
4. cuáles utilizarán mensajería asíncrona;
5. si se adopta Transactional Outbox;
6. el disparador que justificaría adoptar Kafka si no se adopta inmediatamente;
7. el impacto sobre el Tech Radar;
8. la relación de ADR-018 con ADR-014.