# Software Architecture Document (SAD) — Red Vital

## 1. Propósito

El Software Architecture Document (SAD) describe las decisiones estructurales que definen la arquitectura de Red Vital, las fuerzas que las motivan y la manera en que la arquitectura responde a los requisitos funcionales, atributos de calidad, restricciones y riesgos del proyecto.

Este documento se concentra en decisiones arquitectónicas cuya modificación posterior tendría un impacto significativo sobre el sistema.

El SAD no sustituye los documentos especializados de diseño, datos, integración, infraestructura o pruebas. Cuando existe un artefacto específico para un tema, este documento presenta la visión arquitectónica correspondiente y enlaza al documento donde se mantiene el detalle.

---

## 2. Alcance

El SAD cubre:

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
- riesgos y restricciones arquitectónicas.

El detalle de diseño e implementación se mantiene en los documentos especializados correspondientes.

---

## 3. Relación con otros artefactos

El SAD funciona como documento central de arquitectura y se relaciona con otros artefactos de la documentación de Red Vital.

| Artefacto | Relación con el SAD |
|---|---|
| SRS | Proporciona requisitos funcionales, no funcionales, usuarios, módulos y restricciones que originan drivers arquitectónicos. |
| ADRs | Registran formalmente las decisiones arquitectónicas y su justificación. |
| Escenarios de calidad | Definen de forma verificable el comportamiento esperado frente a atributos de calidad. |
| SDD | Desarrolla el diseño detallado de la solución a partir de las decisiones definidas en el SAD. |
| DD | Detalla el diseño lógico, físico y la metadata asociada a los datos. |
| Integración y contratos | Especifica protocolos, contratos y estructuras de comunicación entre componentes. |
| Infraestructura | Implementa la topología, configuración, despliegue, monitoreo y observabilidad definidos por la arquitectura. |
| Testing | Verifica requisitos, atributos de calidad y comportamiento del sistema. |
| Políticas y herramientas | Define tecnologías, políticas y lineamientos permitidos para la construcción de la solución. |

---

# 4. Drivers y killers arquitectónicos

## 4.1 Drivers arquitectónicos

Los drivers arquitectónicos son las fuerzas que condicionan la estructura del sistema y cuya omisión podría obligar a modificar de manera significativa la arquitectura.

Los drivers pueden provenir de:

- requisitos funcionales de alto impacto;
- atributos de calidad;
- restricciones técnicas;
- restricciones normativas;
- restricciones de negocio.

El detalle vigente de los drivers arquitectónicos se encuentra en:

[Drivers y Killers Arquitectónicos](../drivers-killers-arquitectonicos.md)

## 4.2 Killers arquitectónicos

Los killers arquitectónicos representan condiciones cuyo incumplimiento puede invalidar una decisión estructural, impedir la entrega de la solución o hacer inviable la arquitectura dentro del alcance del proyecto.

El catálogo vigente se mantiene junto con los drivers arquitectónicos:

[Drivers y Killers Arquitectónicos](../drivers-killers-arquitectonicos.md)

---

# 5. Atributos de calidad

Los atributos de calidad representan características no funcionales que condicionan las decisiones arquitectónicas de Red Vital.

Entre ellos se consideran aspectos como:

- seguridad;
- confidencialidad;
- disponibilidad;
- fiabilidad;
- mantenibilidad;
- eficiencia de desempeño;
- compatibilidad;
- capacidad de interacción;
- flexibilidad.

Los atributos se priorizan de acuerdo con su impacto sobre el sistema y con las restricciones del proyecto.

Los escenarios detallados asociados a estos atributos se mantienen de forma independiente en:

[Quality Attributes](../quality-attributes/README.md)

---

# 6. Escenarios de calidad

Los escenarios de calidad permiten expresar de manera verificable cómo debe responder el sistema ante estímulos relacionados con los atributos de calidad.

Cada escenario identifica:

- fuente;
- estímulo;
- contexto;
- artefacto;
- respuesta;
- medida de respuesta.

El catálogo completo y vigente de escenarios se encuentra en:

[Escenarios de Calidad](../quality-attributes/README.md)

El SAD utiliza estos escenarios como entrada para definir tácticas, decisiones y prioridades arquitectónicas, pero no duplica su contenido completo.

---

# 7. Trade-offs arquitectónicos

Los trade-offs representan compromisos entre atributos de calidad o alternativas arquitectónicas que no pueden optimizarse simultáneamente.

El análisis de trade-offs permite identificar:

- puntos de sensibilidad;
- puntos de compromiso;
- atributos afectados;
- alternativas consideradas;
- consecuencias de cada alternativa.

Cuando un trade-off conduce a una decisión arquitectónica relevante, la decisión se registra formalmente mediante un ADR.

---

# 8. Tácticas arquitectónicas

Las tácticas arquitectónicas son decisiones de diseño empleadas para lograr una respuesta específica frente a un atributo de calidad.

Las tácticas adoptadas por Red Vital se relacionan con los escenarios de calidad y con los ADRs correspondientes.

Las principales categorías de tácticas incluyen:

- autenticación y autorización;
- aislamiento de fallos;
- control de acceso por jurisdicción;
- observabilidad;
- tolerancia a fallos;
- control de consistencia;
- protección de información sensible;
- evolución de contratos;
- contenerización y configuración por ambiente.

Las tácticas específicas deben mantener trazabilidad con:

1. el atributo de calidad que atienden;
2. el escenario de calidad correspondiente;
3. el ADR que formaliza la decisión cuando aplique.

---

# 9. Architecture Decision Records (ADRs)

Las decisiones arquitectónicas relevantes de Red Vital se documentan mediante Architecture Decision Records.

Los ADRs registran:

- contexto;
- alternativas consideradas;
- decisión;
- consecuencias;
- estado.

El catálogo vigente se encuentra en:

[Architecture Decision Records](../adrs/README.md)

El SAD resume las decisiones estructurales, mientras que cada ADR conserva la deliberación completa que sustenta la decisión.

---

# 10. Arquitectura de alto nivel

La arquitectura de alto nivel describe los principales elementos estructurales de Red Vital, sus responsabilidades y sus relaciones.

Incluye:

- actores externos;
- fronteras del sistema;
- contenedores principales;
- servicios;
- componentes de infraestructura;
- relaciones entre componentes.

Los diagramas arquitectónicos se mantienen en:

[Diagramas de Arquitectura](../diagrams/README.md)

Los diagramas C4 vigentes incluyen:

- contexto;
- contenedores;
- componentes o vistas específicas;
- despliegue, cuando corresponda.

---

# 11. Arquitectura lógica

La arquitectura lógica describe cómo se distribuyen las responsabilidades del sistema entre módulos, servicios y componentes.

Esta vista define:

- separación de responsabilidades;
- límites entre servicios;
- propiedad funcional;
- dependencias permitidas;
- comunicación entre componentes;
- servicios transversales.

La arquitectura lógica debe mantenerse consistente con:

- los módulos funcionales definidos en el SRS;
- los ADRs;
- los contratos de integración;
- los diagramas C4.

---

# 12. Arquitectura de negocio

La arquitectura de negocio describe cómo las capacidades del dominio son soportadas por la arquitectura de Red Vital.

Incluye:

- capacidades principales;
- procesos centrales;
- reglas de negocio invariantes;
- relaciones entre módulos funcionales;
- responsabilidades de los servicios sobre procesos del dominio.

La arquitectura técnica no debe modificar las reglas de negocio definidas en los artefactos funcionales.

---

# 13. Arquitectura de datos

La arquitectura de datos define, desde la perspectiva arquitectónica:

- propiedad de los datos;
- fronteras de almacenamiento;
- clasificación de información;
- consistencia entre servicios;
- datos permitidos y prohibidos;
- retención;
- respaldo;
- evolución del esquema.

El detalle del modelo lógico, físico y la metadata se mantiene en:

[Documentación de Datos](../../data/README.md)

El SAD únicamente mantiene las decisiones estructurales relacionadas con los datos y evita duplicar el diseño detallado.

---

# 14. Arquitectura de infraestructura

La arquitectura de infraestructura define las decisiones estructurales relacionadas con el entorno donde se ejecuta Red Vital.

Incluye:

- topología general;
- contenerización;
- ambientes;
- zonas de confianza;
- seguridad de borde;
- comunicación de red;
- observabilidad;
- respaldo y recuperación.

El detalle de configuración y despliegue se mantiene en:

[Infraestructura](../../infrastructure/README.md)

---

# 15. Riesgos y restricciones arquitectónicas

## 15.1 Restricciones

Las restricciones arquitectónicas pueden provenir de:

- negocio;
- requerimientos;
- regulación;
- tecnología;
- infraestructura;
- políticas del proyecto.

Las restricciones actúan como límites dentro de los cuales deben tomarse las decisiones arquitectónicas.

## 15.2 Riesgos

Los riesgos arquitectónicos corresponden a condiciones que podrían afectar la viabilidad, calidad o evolución de la solución.

Cada riesgo relevante debe identificar:

- descripción;
- impacto;
- probabilidad;
- componente afectado;
- estrategia de mitigación;
- decisión o artefacto relacionado.

## 15.3 Deuda arquitectónica

La deuda arquitectónica aceptada debe registrarse explícitamente cuando una solución temporal o una limitación conocida se mantenga deliberadamente dentro de la arquitectura.

---

# 16. Trazabilidad arquitectónica

Las decisiones del SAD deben mantener trazabilidad con los demás artefactos del proyecto.

La relación esperada es:

**Requisito / Restricción → Driver → Atributo de calidad → Escenario → Táctica → ADR → Arquitectura → Implementación → Prueba**

Esta trazabilidad permite verificar que las decisiones estructurales responden a necesidades reales del sistema y que posteriormente pueden validarse mediante pruebas.

---

# 17. Gobierno y evolución

El SAD es un documento vivo y debe actualizarse cuando:

- cambie una decisión arquitectónica;
- se apruebe un nuevo ADR;
- cambien los drivers o restricciones;
- se modifique la arquitectura de alto nivel;
- se incorporen nuevos servicios;
- cambie la propiedad de datos;
- se modifique la infraestructura;
- aparezca un riesgo arquitectónico relevante.

Los detalles que pertenezcan a documentos especializados deben actualizarse en su artefacto correspondiente y enlazarse desde el SAD, evitando duplicar información.

---

## Navegación

- [Arquitectura](../README.md)
- [ADRs](../adrs/README.md)
- [Diagramas](../diagrams/README.md)
- [Atributos de Calidad](../quality-attributes/README.md)
- [Datos](../../data/README.md)
- [Infraestructura](../../infrastructure/README.md)