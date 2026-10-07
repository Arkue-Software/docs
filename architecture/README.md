# Arquitectura — Red Vital

Esta sección contiene la documentación arquitectónica de **Red Vital**.

Su propósito es centralizar las decisiones, vistas, restricciones y artefactos que describen cómo está estructurado el sistema y por qué se adoptaron determinadas decisiones.

La documentación se mantiene como una **wiki viva en Markdown**, de manera que los diferentes artefactos arquitectónicos puedan relacionarse entre sí mediante enlaces y evolucionar junto con la solución.

---

## 1. Documentación principal

### Software Architecture Document (SAD)

El **SAD** constituye el documento principal de arquitectura de Red Vital.

Describe:

- drivers y killers arquitectónicos;
- atributos de calidad;
- escenarios de calidad;
- trade-offs;
- tácticas arquitectónicas;
- decisiones arquitectónicas;
- arquitectura de alto nivel;
- arquitectura lógica;
- arquitectura de negocio;
- arquitectura de datos;
- arquitectura de infraestructura;
- riesgos y restricciones;
- trazabilidad arquitectónica.

[Consultar SAD](./SAD.md)

---

### Software Design Description (SDD)

El **SDD** desarrolla el diseño de la solución a partir de las decisiones definidas en el SAD.

Incluye o referencia:

- línea de diseño;
- diagramas C4;
- diseño detallado;
- documentación de datos;
- diccionario de datos;
- integración;
- contratos entre componentes.

[Consultar SDD](./SDD.md)

---

## 2. Architecture Decision Records

Los **Architecture Decision Records (ADRs)** registran formalmente las decisiones arquitectónicas relevantes tomadas durante el desarrollo de Red Vital.

Cada ADR documenta:

- estado;
- contexto;
- alternativas consideradas;
- decisión;
- consecuencias.

Los ADRs permiten conservar la trazabilidad histórica de la arquitectura y comprender por qué se adoptó cada decisión.

[Consultar ADRs](./adrs/README.md)

---

## 3. Drivers y killers arquitectónicos

Los drivers representan las fuerzas que condicionan las decisiones estructurales de Red Vital.

Los killers representan condiciones que pueden invalidar una alternativa arquitectónica o hacerla incompatible con las restricciones del proyecto.

Estos elementos sirven como entrada para:

- atributos de calidad;
- tácticas arquitectónicas;
- ADRs;
- decisiones de diseño.

[Consultar Drivers y Killers Arquitectónicos](./drivers-killers-arquitectonicos.md)

---

## 4. Atributos y escenarios de calidad

Los atributos de calidad establecen las características no funcionales que debe preservar la arquitectura.

Los escenarios de calidad permiten expresar estos atributos de manera verificable mediante:

- fuente;
- estímulo;
- contexto;
- artefacto;
- respuesta;
- medida de respuesta.

Estos escenarios sirven como base para definir tácticas arquitectónicas y posteriormente diseñar las pruebas correspondientes.

[Consultar Atributos y Escenarios de Calidad](./quality-attributes/README.md)

---

## 5. Diagramas de arquitectura

Los diagramas representan visualmente diferentes perspectivas de la arquitectura de Red Vital.

Actualmente se utilizan principalmente diagramas del modelo C4 para representar:

- contexto del sistema;
- contenedores;
- componentes o vistas específicas de los servicios;
- despliegue de la solución.

Los diagramas complementan el SAD, el SDD y los ADRs y deben mantenerse sincronizados con la arquitectura vigente.

[Consultar Diagramas de Arquitectura](./diagrams/README.md)

---

## 6. Estudios arquitectónicos

Los estudios arquitectónicos contienen análisis técnicos utilizados para evaluar alternativas antes de adoptar una decisión.

Pueden incluir:

- benchmarks técnicos;
- análisis de granularidad;
- evaluación de tecnologías;
- comparaciones de mecanismos de comunicación;
- análisis que sustentan decisiones arquitectónicas.

Los estudios proporcionan evidencia, pero no reemplazan un ADR.

Cuando un estudio conduce a una decisión arquitectónica relevante, debe existir una referencia entre ambos artefactos.

[Consultar Estudios Arquitectónicos](./studies/README.md)

---

## 7. Relación entre los artefactos

La documentación arquitectónica mantiene la siguiente relación general:

    Requisitos y restricciones
            ↓
    Drivers y killers
            ↓
    Atributos de calidad
            ↓
    Escenarios de calidad
            ↓
    Estudios y análisis
            ↓
    Tácticas arquitectónicas
            ↓
    ADRs
            ↓
    SAD
            ↓
    SDD
            ↓
    Diseño e implementación
            ↓
    Pruebas

Esta relación permite mantener trazabilidad entre las necesidades del sistema, las decisiones arquitectónicas y su implementación.

---

## 8. Relación con otras áreas de la documentación

La arquitectura se relaciona directamente con otros artefactos de la wiki de Red Vital.

| Área | Relación |
|---|---|
| [Producto](../product/) | Define la visión, contexto y dirección general del producto. |
| [Requisitos](../requirements/) | Proporciona requisitos, módulos, usuarios y restricciones que originan decisiones arquitectónicas. |
| [Datos](../data/) | Mantiene el diseño lógico, diseño físico, metadata y diccionario de datos. |
| [Integración](../integration/) | Define contratos, protocolos, APIs, llamadas y eventos entre componentes. |
| [Infraestructura](../infrastructure/) | Materializa las decisiones de despliegue, configuración, monitoreo y observabilidad. |
| [Testing](../testing/) | Verifica requisitos, escenarios de calidad y comportamiento de la solución. |
| [Templates](../templates/) | Contiene las plantillas utilizadas para mantener consistencia documental. |

---

## 9. Principios de mantenimiento

La documentación arquitectónica debe evolucionar junto con la solución.

Cuando se realice un cambio arquitectónico relevante se debe revisar, según corresponda:

1. el SAD;
2. el SDD;
3. los ADRs;
4. los diagramas;
5. los drivers y killers;
6. los atributos y escenarios de calidad;
7. la documentación de datos;
8. los contratos de integración;
9. la infraestructura;
10. las pruebas relacionadas.

La información especializada debe mantenerse en su artefacto correspondiente y enlazarse desde los demás documentos cuando sea necesario.

Se debe evitar duplicar el mismo contenido en diferentes archivos.

---

## 10. Navegación rápida

| Artefacto | Acceso |
|---|---|
| SAD | [Software Architecture Document](./SAD.md) |
| SDD | [Software Design Description](./SDD.md) |
| ADRs | [Architecture Decision Records](./adrs/README.md) |
| Drivers y Killers | [Drivers y Killers Arquitectónicos](./drivers-killers-arquitectonicos.md) |
| Atributos de Calidad | [Quality Attributes](./quality-attributes/README.md) |
| Diagramas | [Diagramas de Arquitectura](./diagrams/README.md) |
| Estudios | [Estudios Arquitectónicos](./studies/README.md) |

---

## 11. Regla de documentación

La carpeta `architecture` representa la **fuente viva de la arquitectura vigente de Red Vital**.

Las versiones históricas o artefactos utilizados en entregas anteriores pueden conservarse como referencia, pero no deben confundirse con la documentación arquitectónica actualmente vigente.

El contenido de esta sección debe permitir responder, como mínimo:

- ¿cómo está estructurado Red Vital?;
- ¿por qué está estructurado de esa manera?;
- ¿qué decisiones arquitectónicas se tomaron?;
- ¿qué atributos de calidad condicionaron esas decisiones?;
- ¿qué riesgos y restricciones existen?;
- ¿dónde se encuentra el detalle de cada decisión?;
- ¿cómo se relaciona la arquitectura con el diseño, los datos, la infraestructura y las pruebas?