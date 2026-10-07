# Software Design Description (SDD) — Red Vital

**Proyecto:** Red Vital  
**Versión:** 4.0  
**Estado:** Documento vivo  
**Fecha:** 7 de octubre de 2026  

---

# 1. Propósito

El Software Design Description (SDD) describe cómo se materializan en el diseño de Red Vital las decisiones arquitectónicas definidas en el SAD.

Mientras el SAD establece la estructura general del sistema, sus restricciones y decisiones arquitectónicas, el SDD documenta cómo dichas decisiones se reflejan en el diseño de:

- la solución;
- los componentes;
- la interfaz;
- los servicios;
- los datos;
- las integraciones;
- los contratos;
- los flujos principales.

El SDD no duplica el contenido detallado de otros artefactos especializados.

Cuando existe un documento específico para datos, integración, infraestructura o contratos, este documento mantiene la relación y referencia correspondiente.

---

# 2. Alcance

El SDD cubre:

- línea de diseño;
- principios de diseño;
- estructura funcional de la solución;
- relación entre frontend y backend;
- vistas C4;
- diseño de los servicios;
- diseño de datos;
- integración entre componentes;
- contratos;
- decisiones de interacción;
- dependencias entre artefactos de diseño.

El diseño físico detallado de datos se mantiene en el documento de datos correspondiente.

---

# 3. Relación con otros artefactos

| Artefacto | Relación con el SDD |
|---|---|
| SAD | Define las decisiones y restricciones arquitectónicas que condicionan el diseño. |
| SRS | Define requisitos, usuarios, módulos y flujos funcionales que deben materializarse en el diseño. |
| ADRs | Justifican decisiones estructurales y técnicas que afectan el diseño. |
| Diagramas C4 | Representan visualmente la estructura y relaciones de la solución. |
| DD | Describe el diseño lógico y físico de los datos. |
| Diccionario de datos | Define entidades, atributos, tipos, restricciones y metadata. |
| Integración y contratos | Define APIs, protocolos, eventos y estructuras de intercambio. |
| Infraestructura | Define dónde y cómo se despliegan los componentes diseñados. |
| Testing | Valida que el diseño implementado satisfaga los requisitos y escenarios definidos. |

---

# 4. Línea de diseño

La línea de diseño define los principios que deben mantenerse de forma consistente en la construcción de Red Vital.

El objetivo es que la solución mantenga una experiencia coherente entre módulos, una estructura técnica comprensible y una separación clara de responsabilidades.

---

## 4.1 Principios generales de diseño

El diseño de Red Vital sigue los siguientes principios:

1. **Consistencia:** los componentes que cumplen funciones equivalentes deben mantener comportamiento y representación homogéneos.

2. **Separación de responsabilidades:** cada módulo, servicio o componente debe tener una responsabilidad claramente definida.

3. **Reutilización:** los elementos comunes deben implementarse como componentes reutilizables y no duplicarse entre módulos.

4. **Simplicidad:** se evita introducir complejidad que no responda a una necesidad funcional o arquitectónica.

5. **Trazabilidad:** las decisiones de diseño deben poder relacionarse con requisitos, arquitectura o reglas de negocio.

6. **Modularidad:** los cambios en un módulo deben generar el menor impacto posible sobre otros componentes.

7. **Accesibilidad:** las interfaces deben evitar depender exclusivamente de elementos visuales como color o posición para comunicar información relevante.

8. **Retroalimentación:** las acciones del usuario deben generar una respuesta clara del sistema.

9. **Prevención de errores:** el diseño debe ayudar a evitar operaciones inválidas antes de que lleguen al backend.

10. **Seguridad por diseño:** la interfaz puede orientar al usuario, pero nunca sustituye las validaciones del backend.

---

# 5. Diseño de la interfaz

El frontend constituye la capa de interacción entre los usuarios y las capacidades expuestas por Red Vital.

La interfaz debe representar las funcionalidades definidas en los módulos del SRS sin incorporar lógica de negocio que corresponda a los servicios.

---

## 5.1 Responsabilidades del frontend

El frontend es responsable de:

- presentar información al usuario;
- capturar entradas;
- validar condiciones básicas de formato;
- administrar navegación;
- representar estados de carga;
- representar errores;
- mostrar confirmaciones;
- consumir los contratos expuestos por la capa de aplicación;
- adaptar la interfaz según el rol del usuario;
- ocultar acciones que no correspondan al contexto del usuario.

El frontend no es responsable de:

- aplicar reglas de autorización definitivas;
- acceder directamente a las bases de datos;
- implementar reglas centrales de negocio;
- modificar datos fuera de los contratos definidos;
- asumir que ocultar una opción equivale a proteger una operación.

---

## 5.2 Organización de la interfaz

La interfaz debe organizarse alrededor de los módulos funcionales definidos para Red Vital.

La navegación debe permitir identificar de forma clara:

- módulo actual;
- acción disponible;
- estado de la operación;
- contexto institucional;
- permisos visibles del usuario.

Los módulos visuales deben corresponder, cuando aplique, con las capacidades funcionales de:

- identidad;
- institucional;
- campañas;
- donación;
- notificaciones.

---

## 5.3 Componentes reutilizables

Los elementos que aparezcan repetidamente en diferentes módulos deben implementarse mediante componentes reutilizables.

Entre ellos pueden encontrarse:

- botones;
- formularios;
- campos;
- tablas;
- tarjetas;
- mensajes;
- alertas;
- modales;
- indicadores de estado;
- elementos de navegación.

Los componentes deben mantener:

- comportamiento consistente;
- nomenclatura uniforme;
- estados definidos;
- validaciones homogéneas;
- manejo consistente de errores.

---

## 5.4 Estados de interacción

Los componentes interactivos deben contemplar, cuando corresponda:

- estado inicial;
- carga;
- éxito;
- error;
- vacío;
- deshabilitado;
- permiso insuficiente.

Las operaciones que puedan modificar información relevante deben informar claramente al usuario sobre su resultado.

---

## 5.5 Formularios

Los formularios deben:

- indicar claramente los campos requeridos;
- validar formato antes del envío;
- mostrar errores cerca del campo relacionado;
- conservar información válida cuando ocurra un error;
- evitar envíos duplicados;
- presentar confirmación cuando la operación finalice correctamente.

Las validaciones de frontend son complementarias.

El backend mantiene la responsabilidad definitiva sobre la validación de reglas de negocio y autorización.

---

## 5.6 Acceso por rol

La interfaz puede adaptar la navegación y las acciones visibles según:

- rol;
- permisos;
- contexto;
- jurisdicción.

Sin embargo, estas condiciones deben validarse nuevamente en gateway y servicios.

La ausencia visual de una opción no constituye un mecanismo de seguridad.

---

# 6. Diseño de servicios

Red Vital organiza las principales responsabilidades de aplicación en cinco servicios:

- Identidad;
- Institucional;
- Campañas;
- Donación;
- Notificaciones.

Cada servicio debe mantener:

- responsabilidad funcional clara;
- contrato explícito;
- propiedad de sus datos;
- independencia respecto de la persistencia de otros servicios;
- capacidad de evolución sin acceso directo a estructuras internas externas.

---

## 6.1 Identidad

El diseño del Servicio de Identidad concentra las responsabilidades relacionadas con:

- autenticación;
- usuarios;
- roles;
- jurisdicción;
- sesiones;
- credenciales;
- identidad utilizada por los demás componentes.

Los demás servicios consumen información de identidad mediante contratos definidos y no mediante acceso directo a su base de datos.

---

## 6.2 Institucional

El Servicio Institucional concentra el diseño relacionado con:

- instituciones;
- bancos de sangre;
- estructura territorial;
- jerarquías;
- información institucional.

Otros módulos deben utilizar identificadores o contratos para referenciar información institucional.

---

## 6.3 Campañas

El Servicio de Campañas concentra:

- creación;
- programación;
- publicación;
- cupos;
- disponibilidad;
- reservas;
- información propia de campañas.

---

## 6.4 Donación

El Servicio de Donación concentra:

- registro de donaciones;
- trazabilidad;
- ciclo de vida de unidades;
- inventario;
- transferencias;
- escalamiento;
- eventos derivados del dominio.

---

## 6.5 Notificaciones

El Servicio de Notificaciones recibe hechos relevantes producidos por otros servicios y gestiona la entrega de mensajes correspondiente.

Su diseño favorece el desacoplamiento respecto de los procesos que originan las notificaciones.

No accede directamente a las persistencias de otros dominios.

---

# 7. Diagramas de diseño

Los diagramas C4 representan las principales vistas estructurales de Red Vital.

La documentación visual se mantiene como un artefacto independiente para evitar duplicación.

[Consultar Diagramas de Arquitectura](./diagrams/README.md)

---

## 7.1 Diagrama de contexto

El diagrama de contexto representa:

- Red Vital como sistema;
- actores externos;
- usuarios;
- sistemas relacionados;
- principales interacciones externas.

[Consultar C4 Context](./diagrams/README.md)

---

## 7.2 Diagrama de contenedores

El diagrama de contenedores representa:

- frontend;
- capa de entrada;
- gateway;
- servicios;
- mecanismos de integración;
- persistencia;
- dependencias principales.

[Consultar C4 Container](./diagrams/README.md)

---

## 7.3 Diagramas por servicio

Los diagramas detallados permiten profundizar en los principales contextos funcionales.

Actualmente existen vistas para:

- Identidad;
- Institucional;
- Campañas;
- Donación;
- Notificaciones.

[Consultar Diagramas por servicio](./diagrams/README.md)

---

## 7.4 Diagrama de despliegue

La vista de despliegue representa la distribución física de:

- componentes de borde;
- gateway;
- servicios;
- bases de datos;
- componentes de observabilidad;
- infraestructura de QA;
- relaciones de red.

Cuando el diagrama de despliegue sea incorporado al repositorio, deberá mantenerse dentro de la documentación de diagramas y ser referenciado desde este SDD.

---

# 8. Diseño de datos

El diseño de datos se mantiene como un artefacto especializado dentro de la documentación de Red Vital.

El SDD establece su relación con el diseño general, pero evita duplicar:

- entidades;
- atributos;
- relaciones;
- tipos;
- restricciones;
- índices;
- metadata;
- estructuras físicas.

Red Vital utiliza persistencia PostgreSQL separada por responsabilidad.

Actualmente se contemplan los contextos de persistencia de:

- Identidad;
- Institucional;
- Campañas;
- Donación.

Cada servicio mantiene propiedad exclusiva sobre su persistencia.

[Consultar Diseño de Datos](../data/DD.md)

---

# 9. Diccionario de datos

El diccionario de datos documenta de manera detallada los elementos que conforman las estructuras de persistencia.

Incluye, cuando corresponda:

- entidad;
- atributo;
- descripción;
- tipo de dato;
- obligatoriedad;
- restricción;
- clave;
- valor permitido;
- relación;
- clasificación de información.

El diccionario constituye una fuente especializada de consulta y no debe duplicarse dentro del SDD.

[Consultar Diccionario de Datos](../data/data-dictionary.md)

---

# 10. Integración y contratos

La integración entre componentes se realiza mediante contratos explícitos.

Los servicios no deben utilizar acceso directo a bases de datos externas como mecanismo de integración.

El diseño de integración debe definir:

- protocolo;
- productor;
- consumidor;
- operación;
- estructura de solicitud;
- estructura de respuesta;
- eventos;
- códigos de resultado;
- manejo de errores;
- versionado.

El detalle se mantiene como artefacto independiente.

[Consultar Integración y Contratos](../data/integration-contracts.md)

---

## 10.1 Comunicación síncrona

Se utiliza cuando un componente requiere una respuesta inmediata para continuar el flujo.

Las llamadas deben definir:

- productor;
- consumidor;
- contrato;
- solicitud;
- respuesta;
- errores esperados;
- tiempos máximos.

---

## 10.2 Comunicación asíncrona

Se utiliza cuando el proceso no necesita una respuesta inmediata y el desacoplamiento aporta valor.

Los eventos deben definir:

- productor;
- consumidor;
- nombre del evento;
- propósito;
- payload;
- momento de emisión;
- estrategia ante fallos;
- estrategia ante duplicados.

El Servicio de Notificaciones constituye uno de los principales consumidores de eventos del sistema.

---

## 10.3 Contratos de API

Los contratos expuestos por los servicios deben mantenerse versionados.

Cuando se utilice OpenAPI, la especificación debe representar el comportamiento real de la implementación.

Los cambios incompatibles deben tratarse explícitamente y mantener trazabilidad con las decisiones correspondientes.

---

# 11. Dependencias de diseño

Las dependencias entre componentes deben mantenerse explícitas.

La dirección general esperada es:

    Usuario
       ↓
    Frontend
       ↓
    Borde
       ↓
    Gateway
       ↓
    Servicios
       ↓
    Persistencia propia

Para interacciones entre servicios:

    Servicio A
       ↓
    Contrato / Evento
       ↓
    Servicio B
       ↓
    Persistencia de B

No se permite:

    Servicio A
       ↓
    Base de datos de B

---

# 12. Relación entre SAD y SDD

El SAD responde principalmente:

> ¿Cómo está estructurado Red Vital y por qué?

El SDD responde principalmente:

> ¿Cómo se materializa ese diseño?

Por esta razón:

- el SAD define decisiones;
- los ADRs justifican decisiones;
- el SDD describe el diseño resultante;
- el DD desarrolla el diseño de datos;
- Integración y Contratos desarrolla la comunicación;
- Infraestructura desarrolla el despliegue;
- Testing valida el comportamiento resultante.

---

# 13. Trazabilidad de diseño

El diseño debe mantener trazabilidad con los artefactos que lo originan.

La relación general es:

    Requisito
        ↓
    Arquitectura
        ↓
    SAD / ADR
        ↓
    SDD
        ↓
    Diseño especializado
        ↓
    Implementación
        ↓
    Prueba

Los cambios importantes de diseño deben permitir identificar:

- requisito asociado;
- decisión arquitectónica relacionada;
- componente afectado;
- contrato afectado;
- datos afectados;
- prueba relacionada.

---

# 14. Evolución del SDD

El SDD es un documento vivo.

Debe actualizarse cuando:

- cambie la línea de diseño;
- se incorpore un nuevo módulo;
- cambie la estructura del frontend;
- cambie la responsabilidad de un servicio;
- se modifique un flujo relevante;
- cambie un contrato;
- cambie el modelo de datos;
- se cree un nuevo diagrama;
- una decisión arquitectónica produzca un cambio de diseño.

La información especializada debe mantenerse en su artefacto correspondiente y enlazarse desde el SDD.

---

# 15. Navegación

## Arquitectura

- [Arquitectura](./README.md)
- [SAD](./SAD.md)
- [ADRs](./adrs/README.md)
- [Diagramas](./diagrams/README.md)
- [Quality Attributes](./quality-attributes/README.md)

## Diseño relacionado

- [Datos](../data/)
- [Diseño de Datos](../data/DD.md)
- [Diccionario de Datos](../data/data-dictionary.md)
- [Integración y Contratos](../data/integration-contracts.md)
- [Infraestructura](../infrastructure/)
- [Requisitos](../requirements/)
- [Testing](../testing/)

---

# 16. Regla de mantenimiento

El SDD no debe convertirse en una copia del SAD, del DD ni del documento de integración.

Su función es conectar el diseño de la solución y proporcionar una vista coherente de cómo se materializa la arquitectura.

Cuando exista información detallada en otro documento:

1. el SDD presenta el contexto necesario;
2. explica la relación con el diseño;
3. referencia el artefacto especializado;
4. evita duplicar el contenido completo.

De esta forma, cada artefacto mantiene una única fuente de verdad dentro de la wiki de Red Vital.