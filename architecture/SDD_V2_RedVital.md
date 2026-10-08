---
title: "Software Design Description (SDD) - RedVital"
subtitle: "Version 2.0 - Sprint 3 / Semana 10"
author: "Arkhé Software S.A.S."
date: "7 de octubre de 2026"
lang: es
---

# Control de versiones

| Versión | Fecha | Descripción |
|---|---|---|
| 1.0 | 22/09/2026 | Diseño inicial de la solución, línea de diseño, servicios, C4 y relación con datos e integración. |
| 2.0 | 07/10/2026 | Alineación con Sprint 3: cinco microservicios, Caddy + APISIX, Kafka + Transactional Outbox, frontend React, vistas C4 actualizadas, despliegue en siete VMs y trazabilidad con DD V3 e Infraestructura V2. |

# 1. Propósito

El **Software Design Description (SDD) V2** describe cómo se materializan en el diseño de RedVital las decisiones definidas en el SAD V3.

El SDD documenta:

- la línea de diseño de la solución;
- la estructura del frontend;
- los servicios y sus responsabilidades;
- las vistas C4;
- la integración entre componentes;
- la relación con los datos;
- el despliegue desde la perspectiva de diseño;
- los contratos;
- la trazabilidad entre arquitectura, diseño e implementación.

El SDD no reemplaza el SAD, el DD ni el Documento de Infraestructura.

# 2. Alcance

El documento cubre:

1. principios de diseño;
2. diseño de interfaz;
3. diseño de servicios;
4. C4 Nivel 1 - Contexto;
5. C4 Nivel 2 - Contenedores;
6. C4 Nivel 3 - Componentes;
7. vista de despliegue;
8. diseño de integración;
9. relación con datos;
10. contratos;
11. trazabilidad;
12. estado de diseño e implementación.

# 3. Relación con otros artefactos

| Artefacto | Relación |
|---|---|
| SAD V3 | Define estructura, drivers, restricciones y decisiones arquitectónicas. |
| SRS V4 | Define usuarios, módulos, requisitos y flujos funcionales. |
| ADRs | Justifican decisiones de diseño estructural. |
| DD V3 | Define propiedad de datos, modelo lógico/físico y diccionario. |
| Infraestructura V2 | Define VMs, redes, puertos, contenedores y operación. |
| OpenAPI | Define contratos HTTP expuestos por la capa de aplicación. |
| AsyncAPI | Define eventos asíncronos. |
| Frontend | Materializa navegación, componentes visuales y flujos de usuario. |
| Testing | Valida el diseño implementado. |

# 4. Principios de diseño

RedVital adopta los siguientes principios:

1. separación de responsabilidades;
2. servicios de grano grueso;
3. contratos explícitos;
4. persistencia propia por servicio;
5. cero acceso cruzado a bases;
6. cero llamadas HTTP directas entre microservicios de negocio;
7. integración asíncrona mediante Kafka;
8. autorización en gateway y validación en servicio;
9. diseño de interfaz consistente y accesible;
10. configuración externa;
11. observabilidad;
12. trazabilidad de decisiones.

# 5. Línea de diseño del frontend

El frontend se implementa con:

- React 19;
- TypeScript;
- Vite;
- Tailwind CSS 4;
- primitivas de Radix UI.

## 5.1 Sistema visual

La línea visual mantiene:

- tipografía de alta legibilidad;
- paleta institucional;
- separación entre color de identidad y color de error;
- foco visible;
- objetivos táctiles mínimos;
- iconografía SVG;
- respeto por `prefers-reduced-motion`;
- animaciones limitadas a `transform` y `opacity`;
- componentes reutilizables.

## 5.2 Organización del frontend

La estructura actual separa:

```text
src/
├── modules/
│   ├── donantes/
│   ├── ciclovida/
│   └── inventario/
├── shared/
│   ├── ui/
│   ├── layout/
│   ├── paginas/
│   ├── sesion/
│   └── tipos/
└── styles/
```

El diseño favorece:

- modularidad;
- reutilización;
- navegación según perfil;
- estados de carga/error;
- separación entre presentación y reglas de negocio.

## 5.3 Responsabilidad del frontend

El frontend puede:

- presentar información;
- capturar datos;
- validar formato;
- gestionar navegación;
- adaptar visibilidad por rol;
- manejar estados de interacción.

El frontend **no es una frontera de seguridad**. La autorización definitiva se aplica en gateway y servicio.

## 5.4 Mockups y evidencia visual

Para esta versión no se mantiene un set independiente de mockups como fuente normativa.

La evidencia de diseño se encuentra en:

- la implementación del frontend;
- el inventario de pantallas;
- los componentes reutilizables;
- la línea visual implementada.

Esto evita duplicar un mockup que pueda quedar desactualizado frente al código.

# 6. Diseño de servicios

RedVital se estructura en cinco servicios principales:

| Servicio | Responsabilidad |
|---|---|
| Identidad | autenticación, sesiones, roles, jurisdicción, tokens y claves públicas |
| Institucional | instituciones, bancos de sangre y estructura territorial |
| Campañas | campañas, cupos, publicación y reservas |
| Donación | donantes, donaciones, unidades, inventario, alertas y ciclo de vida |
| Notificaciones | consumo de eventos y envío desacoplado de notificaciones |

## 6.1 Identidad

Diseño predominante:

```text
API
  ↓
Aplicación
  ↓
Dominio
  ↓
Infraestructura
```

Responsabilidades de diseño:

- emisión y renovación de sesión;
- firma asimétrica RS256;
- JWKS;
- autorización de identidad;
- auditoría de operaciones sensibles.

## 6.2 Campañas

Mantiene separación por capas y persistencia propia.

Campañas no debe leer bases ajenas.

Cuando otro servicio requiere cambios de campaña, la integración se realiza por eventos.

## 6.3 Donación

Utiliza arquitectura hexagonal:

```text
web
  ↓
aplicacion
  ↓
dominio
  ↑
infraestructura
```

Incluye:

- persistencia;
- Kafka;
- Outbox;
- consumidores;
- reglas de ciclo de vida;
- inventario;
- alertas.

## 6.4 Institucional

Permanece como servicio definido en arquitectura y diseño. Su implementación completa continúa pendiente.

## 6.5 Notificaciones

Consume eventos notificables.

No accede directamente a las bases de otros servicios y no posee base de negocio propia.

# 7. C4 Nivel 1 - Contexto

El C1 representa RedVital como sistema y sus actores externos.

**Archivo del repositorio:**  
[`architecture/diagrams/Red Vital C4-c1.png`](./diagrams/Red%20Vital%20C4-c1.png)

![C4 Nivel 1 - Contexto](./diagrams/Red%20Vital%20C4-c1.png)

El diagrama debe permitir identificar:

- usuarios;
- RedVital;
- interacciones externas;
- límites del sistema.

# 8. C4 Nivel 2 - Contenedores

**Archivo del repositorio:**  
[`architecture/diagrams/Red Vital C4-c2- Container Diagram.png`](./diagrams/Red%20Vital%20C4-c2-%20Container%20Diagram.png)

![C4 Nivel 2 - Contenedores](./diagrams/Red%20Vital%20C4-c2-%20Container%20Diagram.png)

El C2 representa:

- frontend;
- Caddy;
- APISIX;
- microservicios;
- Kafka;
- persistencias;
- relaciones principales.

Reglas de lectura:

- frontend entra por borde/gateway;
- cada servicio usa su propia persistencia;
- Kafka media la comunicación interservicio;
- no se representan accesos cruzados a bases.

# 9. C4 Nivel 3 - Componentes

Los C3 describen componentes lógicos. No deben interpretarse como un espejo literal de las carpetas del código.

## 9.1 Identidad

**Archivo:**  
[`Red Vital C4-c3- Identidad.png`](./diagrams/Red%20Vital%20C4-c3-%20Identidad.png)

![C3 Identidad](./diagrams/Red%20Vital%20C4-c3-%20Identidad.png)

## 9.2 Campañas

**Archivo:**  
[`Red Vital C4-c3- campañas.png`](./diagrams/Red%20Vital%20C4-c3-%20campa%C3%B1as.png)

![C3 Campañas](./diagrams/Red%20Vital%20C4-c3-%20campa%C3%B1as.png)

## 9.3 Donación

**Archivo:**  
[`Red Vital C4-c3- donacion.png`](./diagrams/Red%20Vital%20C4-c3-%20donacion.png)

![C3 Donación](./diagrams/Red%20Vital%20C4-c3-%20donacion.png)

## 9.4 Institucional

**Archivo:**  
[`Red Vital C4-c3-institucional.png`](./diagrams/Red%20Vital%20C4-c3-institucional.png)

![C3 Institucional](./diagrams/Red%20Vital%20C4-c3-institucional.png)

## 9.5 Notificaciones

**Archivo:**  
[`Red Vital C4-c3- Notificaciones.png`](./diagrams/Red%20Vital%20C4-c3-%20Notificaciones.png)

![C3 Notificaciones](./diagrams/Red%20Vital%20C4-c3-%20Notificaciones.png)

## 9.6 Regla de consistencia entre C3 y código

La vista C3 expresa **responsabilidades y componentes lógicos**.

La implementación puede utilizar:

- capas;
- arquitectura hexagonal;
- módulos;
- adapters;
- ports;
- controllers;
- handlers.

No es obligatorio que cada caja del C3 exista con el mismo nombre como carpeta o clase.

Sí es obligatorio que:

- la responsabilidad exista;
- las dependencias respeten la arquitectura;
- no aparezcan conexiones prohibidas;
- no exista acceso a persistencias ajenas;
- no se introduzcan llamadas directas servicio-servicio.

# 10. C4 - Despliegue

**Archivo:**  
[`Red Vital C4-c4- Despligue.png`](./diagrams/Red%20Vital%20C4-c4-%20Despligue.png)

![C4 - Despliegue](./diagrams/Red%20Vital%20C4-c4-%20Despligue.png)

La vista representa la topología física de referencia.

La distribución vigente se interpreta como:

| Ambiente | Datos | Servicios | Borde |
|---|---|---|---|
| QA | VM2 | VM3 | VM4 |
| Producción | VM5 | VM6 | VM7 |
| Compartido | VM1 Tools | - | - |

La especificación detallada de:

- redes;
- puertos;
- firewall;
- secretos;
- TLS;
- Docker Compose;
- backup;
- observabilidad;

se mantiene en **Infraestructura V2**.

# 11. Diseño de integración

## 11.1 Entrada síncrona

El tráfico de usuario sigue:

```text
Frontend
   ↓
Caddy
   ↓
APISIX
   ↓
Servicio propietario
   ↓
Persistencia propia
```

## 11.2 Comunicación interservicio

La comunicación entre microservicios de negocio es asíncrona:

```text
Servicio productor
      ↓
Transactional Outbox
      ↓
     Kafka
      ↓
Servicio consumidor
```

No se utiliza:

```text
Servicio A ──HTTP directo──> Servicio B
```

como mecanismo normal de integración entre microservicios de negocio.

## 11.3 Eventos

Los eventos deben incluir:

- identificador;
- tipo;
- versión;
- productor;
- momento de emisión;
- correlación;
- payload mínimo;
- reglas de idempotencia.

Los contratos asíncronos se versionan mediante AsyncAPI cuando corresponde.

# 12. Transactional Outbox

El diseño utiliza Transactional Outbox para operaciones que requieren publicar un evento como consecuencia de un cambio persistente.

```text
BEGIN TX
 ├── cambio de negocio
 └── insert evento_outbox
COMMIT
       ↓
 publicador
       ↓
     Kafka
```

Beneficios:

- evita pérdida de eventos después del commit;
- no requiere transacción distribuida;
- permite reintento;
- mejora trazabilidad.

# 13. API Gateway y borde

## 13.1 Caddy

Responsabilidades:

- TLS;
- HTTPS;
- cabeceras;
- tamaño de solicitudes;
- rate limiting;
- acceso a frontend;
- forwarding hacia APISIX.

## 13.2 APISIX

Responsabilidades:

- routing;
- validación inicial;
- JWKS/RS256;
- autorización por ruta y rol;
- correlación;
- bloqueo de rutas internas.

El gateway no contiene lógica de negocio de dominio.

# 14. Diseño de datos

El SDD no duplica el DD V3.

Persistencia:

| Servicio | Base |
|---|---|
| Identidad | `db_identidad` |
| Institucional | `db_institucional` |
| Campañas | `db_campana` |
| Donación | `db_donacion` |
| Notificaciones | sin base de negocio |

Reglas:

- una base por servicio;
- sin FK entre bases;
- sin joins distribuidos;
- referencias externas mediante identificadores;
- consistencia local + eventos.

**Referencia:** [`../data/DD.md`](../data/DD.md)

# 15. Contratos

## 15.1 OpenAPI

OpenAPI documenta las rutas HTTP expuestas hacia gateway.

Debe permanecer alineado con:

- implementación;
- códigos de respuesta;
- esquemas;
- seguridad;
- versionado.

## 15.2 AsyncAPI

AsyncAPI documenta:

- tópicos;
- productores;
- consumidores;
- eventos;
- payloads;
- versiones.

# 16. Seguridad por diseño

El diseño utiliza defensa en profundidad:

```text
Caddy
  ↓
APISIX
  ↓
Servicio
  ↓
Persistencia
```

Controles:

- TLS;
- tokens firmados;
- autorización;
- rol;
- jurisdicción;
- rutas internas bloqueadas;
- mínimo privilegio;
- secretos externos;
- auditoría;
- redes de datos aisladas.

# 17. Observabilidad

Los componentes deben propagar identificadores de correlación.

La observabilidad contempla:

- logs estructurados;
- métricas;
- health checks;
- Prometheus;
- Grafana;
- métricas Kafka;
- Kafbat para inspección controlada.

# 18. Manejo de errores

Las respuestas deben:

- ser consistentes;
- no revelar información sensible;
- diferenciar errores funcionales y técnicos;
- conservar correlación;
- permitir observabilidad.

Cuando aplique, las APIs utilizan respuestas compatibles con Problem Details / RFC 9457.

# 19. Diseño de estados de interfaz

Las pantallas deben contemplar:

- carga;
- vacío;
- éxito;
- error;
- denegación;
- sesión expirada;
- no encontrado;
- confirmación.

Las operaciones irreversibles o sensibles deben presentar confirmación clara.

# 20. Acceso por perfil

La navegación puede cambiar según U1-U7.

La UI:

- oculta opciones no aplicables;
- adapta módulos;
- muestra contexto;
- comunica denegaciones.

Esto mejora usabilidad pero **no reemplaza autorización backend**.

# 21. Dependencias permitidas

Permitido:

```text
Frontend -> Caddy -> APISIX -> Servicio
Servicio -> su Base
Servicio -> Kafka
Kafka -> Servicio consumidor
Tools -> métricas
```

No permitido:

```text
Frontend -> Servicio directo
Servicio -> Base ajena
Servicio -> Servicio directo por HTTP
Internet -> Tools
Internet -> administración de APISIX
```

# 22. Correspondencia diseño - implementación

| Diseño | Implementación / estado |
|---|---|
| Frontend React | repositorio `frontend` |
| Identidad | .NET 10 |
| Campañas | .NET 10 |
| Donación | Java 25 + Spring Boot |
| Institucional | pendiente de implementación completa |
| Notificaciones | pendiente de implementación completa |
| Borde | Caddy |
| Gateway | APISIX |
| Mensajería | Kafka |
| Persistencia | PostgreSQL 16 |
| Observabilidad | Prometheus + Grafana + Kafbat |

# 23. Trazabilidad

```text
SRS
 ↓
SAD / ADR
 ↓
SDD
 ↓
C4 / contrato / diseño UI
 ↓
Implementación
 ↓
QA
```

Ejemplos:

| Necesidad | Diseño |
|---|---|
| autorización por perfil | APISIX + validación en servicio |
| aislamiento de datos | una base por servicio |
| desacoplamiento | Kafka + Outbox |
| seguridad de borde | Caddy |
| trazabilidad | correlación + auditoría |
| usabilidad | sistema visual + estados de interacción |

# 24. Estado de la V2

Para la entrega de Semana 10:

- las vistas C4 están incorporadas y enlazadas;
- la vista de despliegue existe en `architecture/diagrams`;
- el diseño utiliza APISIX, no YARP;
- la integración interservicio utiliza Kafka;
- el modelo de datos se referencia desde DD V3;
- la infraestructura se referencia desde Infraestructura V2;
- el frontend tiene línea de diseño documentada;
- los mockups separados no son requisito para considerar coherente el SDD mientras el diseño visual esté materializado y documentado.

# 25. Pendientes

- cerrar implementación de Institucional;
- cerrar implementación de Notificaciones;
- mantener C4 sincronizado con decisiones vigentes;
- mantener contratos alineados con código;
- actualizar evidencia del frontend cuando los flujos cambien;
- consolidar resultados QA.

# 26. Regla de mantenimiento

El SDD debe actualizarse cuando cambie:

- una vista C4;
- la responsabilidad de un servicio;
- la integración;
- el gateway;
- la estructura del frontend;
- un contrato;
- la persistencia;
- la vista de despliegue;
- una decisión de diseño estructural.

# Anexo A. Índice directo de diagramas

| Vista | Archivo |
|---|---|
| C1 Contexto | [`Red Vital C4-c1.png`](./diagrams/Red%20Vital%20C4-c1.png) |
| C2 Contenedores | [`Red Vital C4-c2- Container Diagram.png`](./diagrams/Red%20Vital%20C4-c2-%20Container%20Diagram.png) |
| C3 Campañas | [`Red Vital C4-c3- campañas.png`](./diagrams/Red%20Vital%20C4-c3-%20campa%C3%B1as.png) |
| C3 Donación | [`Red Vital C4-c3- donacion.png`](./diagrams/Red%20Vital%20C4-c3-%20donacion.png) |
| C3 Identidad | [`Red Vital C4-c3- Identidad.png`](./diagrams/Red%20Vital%20C4-c3-%20Identidad.png) |
| C3 Notificaciones | [`Red Vital C4-c3- Notificaciones.png`](./diagrams/Red%20Vital%20C4-c3-%20Notificaciones.png) |
| C3 Institucional | [`Red Vital C4-c3-institucional.png`](./diagrams/Red%20Vital%20C4-c3-institucional.png) |
| C4 Despliegue | [`Red Vital C4-c4- Despligue.png`](./diagrams/Red%20Vital%20C4-c4-%20Despligue.png) |

# Anexo B. Ubicación esperada

El documento debe residir en:

```text
architecture/SDD.md
```

y los diagramas en:

```text
architecture/diagrams/
```

De esta forma los enlaces relativos del documento funcionan directamente dentro del repositorio.
