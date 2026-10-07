# Software Architecture Document (SAD) — Red Vital

**Proyecto:** Red Vital  
**Organización:** Arkhé Software S.A.S.  
**Versión:** 4.0  
**Estado:** En actualización  
**Fecha:** 7 de octubre de 2026  
**Clasificación:** Uso interno del proyecto  

---

## Control de versiones

| Versión | Fecha | Descripción |
|---|---|---|
| 1.0 | 01/09/2026 | Versión inicial de la arquitectura de Red Vital. |
| 2.0 | 22/09/2026 | Incorporación de la arquitectura distribuida de cinco servicios, actualización de decisiones arquitectónicas, escenarios de calidad, infraestructura y observabilidad. |
| 4.0 | 07/10/2026 | Reorganización del SAD como documentación viva en Markdown, actualización de arquitectura, persistencia, integración, infraestructura, trazabilidad y referencias a los artefactos especializados de la wiki. |

---

# 1. Introducción

## 1.1 Propósito

Este documento describe la arquitectura de **Red Vital**: las fuerzas que la condicionan, las decisiones estructurales adoptadas y la manera en que dichas decisiones permiten cumplir los requisitos funcionales, atributos de calidad y restricciones del proyecto.

El SAD se concentra en decisiones cuya modificación tendría un impacto significativo sobre la solución.

Por tanto, este documento define principalmente:

- fronteras arquitectónicas;
- responsabilidades de los servicios;
- propiedad de los datos;
- mecanismos de comunicación;
- restricciones;
- tácticas arquitectónicas;
- decisiones estructurales;
- infraestructura de alto nivel;
- riesgos arquitectónicos.

El SAD no reemplaza los documentos especializados de diseño, datos, integración, infraestructura o pruebas.

Cuando existe un artefacto específico para un tema, el SAD establece la decisión arquitectónica correspondiente y referencia el artefacto donde se mantiene el detalle.

---

## 1.2 Alcance

Este documento cubre:

- drivers arquitectónicos;
- killers arquitectónicos;
- atributos de calidad;
- escenarios de calidad;
- trade-offs arquitectónicos;
- tácticas arquitectónicas;
- Architecture Decision Records (ADRs);
- arquitectura de alto nivel;
- arquitectura lógica;
- arquitectura de negocio;
- arquitectura de datos;
- arquitectura de infraestructura;
- integración entre servicios desde la perspectiva arquitectónica;
- riesgos;
- restricciones;
- trazabilidad arquitectónica.

El detalle de implementación corresponde a otros artefactos del repositorio.

---

## 1.3 Relación con otros artefactos

| Artefacto | Relación con el SAD |
|---|---|
| SRS | Define usuarios, módulos, requisitos funcionales, requisitos no funcionales y restricciones que originan los drivers arquitectónicos. |
| Quality Attributes | Contiene los escenarios detallados que permiten verificar los atributos de calidad. |
| ADRs | Registran formalmente las decisiones arquitectónicas adoptadas. |
| Diagramas | Representan visualmente las distintas vistas de la arquitectura. |
| SDD | Describe el diseño detallado de la solución a partir de las decisiones establecidas por el SAD. |
| DD / Datos | Describe el modelo lógico, modelo físico, metadata y diccionario de datos. |
| Integración y contratos | Define protocolos, contratos, llamadas, APIs y eventos utilizados entre componentes. |
| Infraestructura | Detalla configuración, despliegue, ambientes, monitoreo y observabilidad. |
| Testing | Verifica requisitos funcionales, atributos de calidad y comportamiento de la solución. |
| Políticas y herramientas | Define tecnologías, herramientas, políticas y lineamientos permitidos para el proyecto. |

---

# 2. Drivers y killers arquitectónicos

Los drivers y killers arquitectónicos condicionan las decisiones estructurales de Red Vital.

El catálogo detallado se mantiene como artefacto independiente:

[Drivers y Killers Arquitectónicos](./drivers-killers-arquitectonicos.md)

---

## 2.1 Drivers arquitectónicos

Un driver arquitectónico es una condición cuya modificación puede producir cambios significativos en la estructura del sistema.

Los drivers de Red Vital provienen principalmente de:

- requisitos funcionales de alto impacto;
- atributos de calidad;
- seguridad;
- privacidad;
- restricciones del dominio;
- restricciones de infraestructura;
- restricciones tecnológicas;
- regulaciones;
- condiciones operacionales.

Entre los drivers arquitectónicos principales se encuentran:

- control de acceso basado en rol y jurisdicción;
- aislamiento de datos entre dominios;
- separación de responsabilidades;
- trazabilidad del ciclo de vida de la donación;
- disponibilidad de procesos críticos;
- comunicación controlada entre servicios;
- capacidad de evolución independiente;
- observabilidad;
- despliegue reproducible;
- protección de datos personales y sensibles;
- consistencia controlada entre servicios.

---

## 2.2 Killers arquitectónicos

Los killers representan condiciones cuya aparición puede invalidar una alternativa arquitectónica.

Entre los principales killers se encuentran:

- compartir directamente una base de datos entre servicios;
- permitir consultas directas a la base de otro servicio;
- almacenar datos clínicos prohibidos;
- permitir acceso fuera de la jurisdicción autorizada;
- utilizar credenciales compartidas entre servicios;
- introducir dependencias técnicas que impidan la evolución independiente;
- introducir tecnologías fuera del catálogo aprobado sin decisión formal;
- permitir comunicación no controlada entre zonas de confianza;
- perder trazabilidad de operaciones sensibles;
- almacenar secretos dentro del repositorio;
- utilizar la interfaz de usuario como único mecanismo de autorización.

---

# 3. Atributos de calidad

Los atributos de calidad condicionan las decisiones arquitectónicas de Red Vital.

Entre los atributos priorizados se encuentran:

- seguridad;
- confidencialidad;
- integridad;
- disponibilidad;
- fiabilidad;
- adecuación funcional;
- eficiencia de desempeño;
- capacidad de interacción;
- compatibilidad;
- mantenibilidad;
- flexibilidad;
- observabilidad;
- extensibilidad.

Los atributos y su priorización detallada se mantienen en:

[Quality Attributes](./quality-attributes/README.md)

---

# 4. Escenarios de calidad

Los escenarios de calidad permiten transformar los atributos de calidad en condiciones verificables.

Cada escenario establece:

- fuente;
- estímulo;
- contexto;
- artefacto afectado;
- respuesta esperada;
- medida de respuesta.

El catálogo completo se mantiene fuera del SAD para evitar duplicación:

[Escenarios de Calidad](./quality-attributes/README.md)

Los escenarios constituyen una entrada directa para:

- definición de tácticas;
- adopción de decisiones arquitectónicas;
- diseño de pruebas;
- definición de observabilidad;
- validación en QA.

---

# 5. Trade-offs arquitectónicos

Las decisiones arquitectónicas implican compromisos entre objetivos que no pueden maximizarse simultáneamente.

Los trade-offs relevantes se documentan a continuación.

---

## 5.1 Seguridad vs. complejidad

Red Vital aplica controles de autorización en más de una capa.

Esto aumenta la complejidad de implementación, pero evita depender exclusivamente del gateway como punto único de seguridad.

La interfaz de usuario nunca constituye una frontera de seguridad.

---

## 5.2 Independencia de servicios vs. complejidad operativa

La separación de responsabilidades en varios servicios permite:

- reducir acoplamiento;
- aislar fallos;
- mantener propiedad independiente de datos;
- evolucionar componentes de manera separada.

A cambio, aumenta:

- el número de componentes;
- la complejidad del despliegue;
- la necesidad de monitoreo;
- la gestión de contratos;
- la complejidad de comunicación.

---

## 5.3 Separación de datos vs. facilidad de consulta

Una base compartida facilitaría consultas entre dominios.

Sin embargo, produciría:

- acoplamiento;
- dependencia entre esquemas;
- pérdida de propiedad de datos;
- mayores riesgos de seguridad;
- dificultad para evolucionar los servicios.

Red Vital prioriza la **propiedad exclusiva de datos**.

---

## 5.4 Comunicación síncrona vs. desacoplamiento

La comunicación síncrona permite obtener una respuesta inmediata, pero crea dependencia temporal entre servicios.

La comunicación asíncrona aumenta el desacoplamiento y permite recuperación independiente, pero introduce consistencia eventual.

La arquitectura utiliza ambos mecanismos según el tipo de interacción.

---

## 5.5 Consistencia inmediata vs. disponibilidad

Red Vital evita transacciones distribuidas entre servicios.

Cuando una operación cruza contextos, la solución prioriza:

- límites claros de responsabilidad;
- consistencia local;
- contratos explícitos;
- consistencia eventual cuando sea aceptable.

---

## 5.6 Granularidad de servicios vs. costo operacional

Una separación excesivamente fina produciría:

- más despliegues;
- más llamadas remotas;
- más infraestructura;
- mayor complejidad operacional.

Por esta razón, Red Vital utiliza servicios de **grano relativamente grueso**, alineados con responsabilidades funcionales claramente diferenciadas.

---

# 6. Tácticas arquitectónicas

Las tácticas permiten responder a los escenarios de calidad y materializar los atributos priorizados.

---

## 6.1 Seguridad

Se aplican las siguientes tácticas:

- autenticación centralizada;
- autorización basada en roles;
- autorización basada en jurisdicción;
- validación en gateway;
- validación nuevamente en los servicios;
- mínimo privilegio;
- credenciales independientes;
- secretos fuera del repositorio;
- firma asimétrica de tokens;
- expiración limitada de tokens de acceso;
- protección de rutas internas;
- TLS en el borde;
- cabeceras de seguridad;
- limitación de tasa;
- auditoría de operaciones sensibles.

---

## 6.2 Disponibilidad y resiliencia

Se aplican:

- aislamiento entre servicios;
- health checks;
- límites de tiempo;
- recuperación independiente;
- degradación controlada;
- procesamiento asíncrono cuando corresponda;
- reintentos únicamente en operaciones seguras;
- aislamiento de dependencias.

---

## 6.3 Mantenibilidad

Se aplican:

- límites claros de responsabilidad;
- contratos versionados;
- ADRs;
- documentación viva en Markdown;
- separación entre arquitectura, diseño e implementación;
- servicios independientes;
- migraciones de base versionadas;
- trazabilidad entre decisiones y componentes.

---

## 6.4 Observabilidad

Se aplican:

- métricas;
- registros estructurados;
- identificadores de correlación;
- health checks;
- monitoreo de recursos;
- métricas de aplicación;
- Prometheus;
- Grafana.

---

## 6.5 Evolución

Se aplican:

- contratos explícitos;
- OpenAPI cuando corresponda;
- separación de dominios;
- propiedad independiente de datos;
- ADRs para cambios estructurales;
- configuración externa;
- despliegue independiente de componentes.

---

# 7. Architecture Decision Records

Las decisiones arquitectónicas relevantes se documentan mediante **Architecture Decision Records (ADRs)**.

Cada ADR registra:

- estado;
- contexto;
- opciones consideradas;
- decisión;
- consecuencias.

El catálogo vigente se encuentra en:

[Architecture Decision Records](./adrs/README.md)

Los estudios técnicos pueden apoyar una decisión, pero no sustituyen al ADR.

El estudio conserva la evidencia y comparación de alternativas; el ADR registra formalmente la decisión adoptada.

---

# 8. Arquitectura de alto nivel

Red Vital adopta una arquitectura distribuida organizada alrededor de cinco servicios principales:

1. Identidad.
2. Institucional.
3. Campañas.
4. Donación.
5. Notificaciones.

Los servicios se encuentran detrás de una capa de entrada formada por el proxy de borde y el gateway.

Los diagramas vigentes se mantienen en:

[Diagramas de Arquitectura](./diagrams/README.md)

---

## 8.1 Componentes principales

A alto nivel, la arquitectura contiene:

- cliente o interfaz;
- proxy de borde;
- API Gateway;
- Servicio de Identidad;
- Servicio Institucional;
- Servicio de Campañas;
- Servicio de Donación;
- Servicio de Notificaciones;
- persistencia independiente;
- mecanismos de integración;
- observabilidad.

---

## 8.2 Proxy de borde

Caddy actúa como componente de borde.

Sus responsabilidades incluyen:

- terminación TLS;
- redirección HTTPS;
- aplicación de cabeceras de seguridad;
- limitación del tamaño de solicitudes;
- registro de acceso;
- encaminamiento hacia la capa interna correspondiente.

Caddy no contiene lógica de negocio.

---

## 8.3 API Gateway

El gateway constituye el punto de entrada a las APIs de aplicación.

Entre sus responsabilidades se encuentran:

- enrutamiento;
- validación inicial de identidad;
- aplicación de políticas de autorización;
- control de acceso;
- limitación de tasa;
- propagación de contexto;
- generación o propagación del identificador de correlación.

El gateway no constituye el único punto de autorización.

Los servicios deben validar nuevamente las condiciones de seguridad que les correspondan.

---

# 9. Arquitectura lógica

La arquitectura lógica distribuye las responsabilidades funcionales entre cinco servicios.

---

## 9.1 Servicio de Identidad

Responsable de:

- autenticación;
- usuarios;
- roles;
- asignación de jurisdicción;
- sesiones;
- emisión de identidad;
- emisión y validación de credenciales;
- gestión de credenciales de servicio.

Ningún otro servicio almacena contraseñas ni credenciales de usuario.

---

## 9.2 Servicio Institucional

Responsable de:

- instituciones;
- bancos de sangre;
- estructura territorial;
- jerarquía territorial;
- información institucional;
- consultas institucionales;
- composición de información de auditoría cuando corresponda.

---

## 9.3 Servicio de Campañas

Responsable de:

- creación de campañas;
- publicación de campañas;
- programación;
- disponibilidad de cupos;
- reservas;
- información asociada a las campañas.

---

## 9.4 Servicio de Donación

Responsable de:

- registro de donaciones;
- ciclo de vida de las unidades;
- trazabilidad;
- inventario;
- transferencias;
- reconocimiento;
- procesos de escalamiento;
- generación de eventos de negocio relacionados con el dominio.

El Servicio de Donación constituye el propietario principal de los datos asociados al ciclo de vida de la donación.

---

## 9.5 Servicio de Notificaciones

Responsable de:

- consumir eventos notificables;
- determinar los destinatarios correspondientes;
- ejecutar el envío;
- manejar reintentos;
- evitar duplicados;
- desacoplar la entrega de mensajes del proceso de negocio que originó el evento.

El Servicio de Notificaciones **no accede directamente a las bases de datos de otros servicios**.

La información necesaria se obtiene mediante contratos o eventos definidos.

---

# 10. Integración y comunicación

Los componentes de Red Vital se comunican mediante contratos explícitos.

La comunicación se divide en dos categorías.

---

## 10.1 Comunicación síncrona

Se utiliza cuando un servicio requiere una respuesta inmediata para continuar una operación.

Las llamadas deben:

- utilizar contratos explícitos;
- respetar límites de responsabilidad;
- establecer tiempos máximos;
- manejar fallos;
- evitar dependencias circulares.

Los servicios nunca utilizan acceso directo a otra base de datos como mecanismo de integración.

---

## 10.2 Comunicación asíncrona

Se utiliza cuando el proceso no requiere respuesta inmediata y el desacoplamiento aporta valor.

Es especialmente relevante para:

- notificaciones;
- eventos de dominio;
- procesos que pueden recuperarse posteriormente.

Los eventos permiten que un servicio publique un hecho sin depender de la disponibilidad inmediata de todos sus consumidores.

---

## 10.3 Notificaciones y consumo de eventos

Cuando ocurre un hecho que requiere notificación:

1. el servicio propietario completa su operación;
2. registra o publica el evento correspondiente;
3. el mecanismo de mensajería pone el evento a disposición;
4. el Servicio de Notificaciones actúa como consumidor;
5. procesa el evento;
6. ejecuta la entrega;
7. registra el resultado correspondiente.

De esta manera, una falla temporal del mecanismo de notificación no invalida la operación principal.

El detalle de protocolos, contratos, eventos y APIs se mantiene en:

[Integración](../integration/)

---

# 11. Arquitectura de negocio

La arquitectura de negocio relaciona las capacidades del dominio con los componentes responsables de soportarlas.

Las capacidades principales son:

- identidad y control de acceso;
- gestión institucional;
- gestión territorial;
- campañas;
- gestión de donaciones;
- trazabilidad;
- inventario;
- transferencias;
- escalamiento;
- notificaciones;
- indicadores.

---

## 11.1 Principio de responsabilidad

Cada capacidad debe tener un responsable claro.

Los servicios no deben implementar reglas que pertenecen a otro contexto únicamente para evitar una llamada.

Cuando una operación requiere información externa:

- se consulta mediante contrato;
- se utiliza información transportada de forma controlada;
- o se aplica consistencia eventual cuando el caso lo permita.

---

## 11.2 Ciclo de vida de la unidad

El Servicio de Donación es responsable de mantener la evolución de una unidad a través de sus estados permitidos.

Las transiciones deben:

- respetar las reglas de negocio;
- registrarse;
- mantener trazabilidad;
- impedir transiciones inválidas;
- conservar el historial.

---

## 11.3 Escalamiento

El proceso de escalamiento pertenece al Servicio de Donación porque:

- depende del inventario;
- el inventario pertenece al contexto de Donación;
- las transferencias pertenecen al mismo contexto;
- la información institucional requerida es únicamente de consulta.

El proceso puede consultar al Servicio Institucional, pero no modifica datos institucionales.

---

# 12. Arquitectura de datos

La arquitectura de datos establece:

- propiedad;
- aislamiento;
- clasificación;
- consistencia;
- retención;
- recuperación;
- evolución.

El detalle físico y lógico se mantiene en:

[Documentación de Datos](../data/)

---

## 12.1 Tecnología de persistencia

Red Vital utiliza **PostgreSQL** como tecnología principal de persistencia relacional.

PostgreSQL proporciona:

- integridad transaccional local;
- restricciones;
- índices;
- control de acceso;
- respaldo y restauración;
- migraciones de esquema;
- soporte para datos relacionales consistentes.

La utilización de PostgreSQL no implica una base compartida.

El principio arquitectónico es **database per service / persistence ownership**.

---

## 12.2 Propiedad de datos

Se aplica la siguiente regla:

> Un dato tiene un único servicio propietario.

El servicio propietario constituye la única vía autorizada para modificar sus datos.

Ningún servicio puede consultar directamente las tablas de otro servicio.

---

## 12.3 Persistencia separada

La arquitectura contempla cuatro contextos principales de persistencia PostgreSQL:

| Persistencia | Servicio propietario |
|---|---|
| Base de Identidad | Servicio de Identidad |
| Base Institucional | Servicio Institucional |
| Base de Campañas | Servicio de Campañas |
| Base de Donación | Servicio de Donación |

Cada persistencia mantiene:

- credenciales propias;
- esquema bajo responsabilidad del servicio propietario;
- permisos independientes;
- migraciones propias;
- respaldo independiente;
- acceso restringido.

El Servicio de Notificaciones no obtiene acceso directo a estas bases.

---

## 12.4 Servicio de Notificaciones y persistencia

El Servicio de Notificaciones no accede directamente a las bases de datos pertenecientes a otros servicios.

La información requerida para generar o entregar una notificación debe obtenerse mediante:

- eventos;
- contratos internos;
- APIs autorizadas;
- información incluida explícitamente en el mensaje o evento correspondiente.

Cuando el Servicio de Notificaciones necesite información perteneciente a otro dominio, debe solicitarla al servicio propietario mediante el contrato definido.

No se permite el acceso directo desde Notificaciones hacia las persistencias de:

- Identidad;
- Institucional;
- Campañas;
- Donación.

La interacción válida es:

    Servicio de Notificaciones
            ↓
    Contrato / API / Evento
            ↓
    Servicio propietario
            ↓
    PostgreSQL

---

## 12.5 Principios de persistencia

La arquitectura de persistencia de Red Vital se rige por los siguientes principios:

1. Cada dato tiene un único servicio propietario.
2. Ningún servicio accede directamente a las tablas de otro servicio.
3. Cada servicio utiliza credenciales independientes.
4. Cada persistencia mantiene su propio esquema y ciclo de evolución.
5. Las integraciones entre dominios se realizan mediante contratos explícitos.
6. Los cambios de esquema se administran mediante migraciones versionadas.
7. Las migraciones deben mantener trazabilidad.
8. Los datos prohibidos no deben incorporarse al modelo.
9. Los mecanismos de auditoría deben preservar integridad y trazabilidad.
10. Los procedimientos de respaldo y restauración deben validarse periódicamente.

---

## 12.6 Roles de PostgreSQL

Cada persistencia PostgreSQL debe aplicar separación de privilegios.

Como mínimo se consideran los siguientes roles:

### Rol de administración

Utilizado exclusivamente para:

- creación inicial;
- tareas excepcionales de administración;
- mantenimiento controlado;
- recuperación cuando sea necesario.

Este rol no debe utilizarse por los servicios durante la operación normal.

### Rol de migraciones

Utilizado para:

- crear objetos de base de datos;
- modificar estructuras;
- aplicar cambios de esquema asociados a nuevas versiones.

### Rol del servicio

Utilizado por la aplicación durante su operación normal.

Este rol debe disponer únicamente de los privilegios mínimos necesarios para cumplir las responsabilidades del servicio correspondiente.

---

## 12.7 Datos compartidos entre contextos

Cuando un servicio necesita información perteneciente a otro contexto funcional, no debe acceder directamente a su persistencia.

La información puede obtenerse mediante:

- llamadas síncronas;
- eventos;
- datos transportados de forma controlada;
- copias locales justificadas por una decisión arquitectónica explícita.

No se permite:

- compartir credenciales;
- consultar directamente tablas externas;
- utilizar una base común como mecanismo de integración;
- modificar datos cuyo propietario sea otro servicio.

---

## 12.8 Consistencia

La consistencia fuerte se mantiene dentro de las transacciones locales de cada servicio.

La arquitectura evita depender de transacciones distribuidas entre servicios.

Según las características de la operación, se puede utilizar:

- consistencia fuerte local;
- consistencia eventual;
- eventos;
- procesamiento asíncrono;
- información fijada al momento de una operación;
- datos transportados mediante contratos.

La estrategia utilizada debe mantener la integridad del dominio y evitar dependencias innecesarias entre servicios.

---

## 12.9 Identidad, rol y jurisdicción

La identidad del usuario, su rol y la información necesaria para evaluar la jurisdicción pueden transportarse mediante el token de acceso.

Esto permite reducir llamadas innecesarias al Servicio de Identidad durante cada solicitud.

La información contenida en el token debe:

- limitarse a lo necesario;
- tener una vigencia definida;
- validarse correctamente;
- no contener información sensible innecesaria.

---

## 12.10 Información institucional

Cuando otro servicio requiere información institucional:

- conserva únicamente los identificadores necesarios;
- consulta al Servicio Institucional cuando necesita información adicional;
- evita replicar innecesariamente registros institucionales completos;
- no modifica información cuyo propietario sea el Servicio Institucional.

---

## 12.11 Información de campañas

Los datos propios de una campaña pertenecen al Servicio de Campañas.

Cuando otro servicio necesita relacionar una operación con una campaña, utiliza su identificador y el contrato definido para dicha interacción.

Los demás servicios no acceden directamente a las tablas del Servicio de Campañas.

---

## 12.12 Eventos notificables

Los eventos utilizados por el Servicio de Notificaciones representan hechos ocurridos dentro de los servicios propietarios del dominio.

La arquitectura debe garantizar que la operación principal y la generación del evento mantengan la consistencia necesaria para evitar:

- pérdida de eventos;
- generación de notificaciones sobre operaciones inexistentes;
- duplicación innecesaria;
- estados inconsistentes.

La entrega posterior de la notificación puede realizarse de forma eventual.

---

## 12.13 Clasificación de datos

La información manejada por Red Vital se clasifica según su nivel de sensibilidad y uso.

| Clasificación | Ejemplos | Tratamiento |
|---|---|---|
| Sensible | Información sensible autorizada por el dominio | Acceso restringido y exclusión de logs y métricas. |
| Personal | Identificación y datos de contacto | Acceso sujeto a autorización y jurisdicción. |
| Operacional restringido | Inventario, unidades y transferencias | Acceso limitado a los actores autorizados. |
| Operacional agregado | Indicadores y métricas agregadas | Presentación según el nivel de agregación permitido. |
| Auditoría | Actor, operación, recurso, resultado y fecha | Conservación íntegra y acceso restringido. |
| Prohibido | Información clínica no contemplada dentro del alcance | No debe capturarse, almacenarse, transmitirse ni registrarse. |

---

## 12.14 Respaldo y restauración

Las persistencias PostgreSQL deben respaldarse de manera independiente.

El procedimiento de respaldo debe permitir:

- generar copias de seguridad;
- restaurar una persistencia individual;
- validar la integridad de la restauración;
- recuperar información sin afectar innecesariamente otras bases;
- documentar el procedimiento utilizado.

La restauración de una base no debe requerir restaurar todas las demás persistencias.