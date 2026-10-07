# Governance — Red Vital

Esta sección contiene los lineamientos de **gobierno del proyecto Red Vital**.

Su propósito es establecer reglas comunes para la gestión de:

- documentación;
- herramientas;
- políticas de trabajo;
- versionamiento;
- trazabilidad;
- colaboración;
- mantenimiento de artefactos;
- evolución del repositorio.

La carpeta `governance` funciona como punto de referencia para las reglas transversales que aplican al resto de la documentación y del proyecto.

---

## 1. Propósito

La documentación de gobierno busca asegurar que Red Vital mantenga:

- consistencia;
- orden;
- trazabilidad;
- control de cambios;
- criterios comunes;
- prácticas reproducibles;
- herramientas claramente definidas;
- responsabilidades documentales comprensibles.

Estas reglas no sustituyen los documentos especializados de arquitectura, datos, integración, infraestructura o testing.

Su función es establecer cómo deben mantenerse y gobernarse esos artefactos.

---

## 2. Documentation Guidelines

El documento **Documentation Guidelines** define las reglas para crear, organizar y mantener la documentación del proyecto.

Incluye:

- uso de Markdown;
- organización de carpetas;
- uso de README;
- convenciones de nombres;
- versionamiento;
- enlaces relativos;
- manejo de diagramas;
- trazabilidad;
- documentación histórica;
- entregables;
- mantenimiento de la wiki.

[Consultar Documentation Guidelines](./documentation-guidelines.md)

---

## 3. Policies and Tools

El documento **Policies and Tools** define las políticas generales de trabajo y las herramientas oficiales utilizadas en Red Vital.

Incluye:

- Git;
- GitHub;
- ramas;
- Pull Requests;
- commits;
- GitHub Issues;
- GitHub Projects;
- OpenAPI;
- PostgreSQL;
- Docker;
- Caddy;
- CI/CD;
- observabilidad;
- Prometheus;
- Grafana;
- seguridad;
- gestión de secretos;
- herramientas de pruebas.

[Consultar Policies and Tools](./policies-and-tools.md)

---

## 4. Alcance de Governance

La sección de Governance contiene reglas transversales.

No debe utilizarse para almacenar información especializada que corresponda a otras áreas.

Por ejemplo:

| Tema | Ubicación |
|---|---|
| Arquitectura | `architecture/` |
| Datos | `data/` |
| Integración | `integration/` |
| Infraestructura | `infrastructure/` |
| Requisitos | `requirements/` |
| Pruebas | `testing/` |
| Procesos | `processes/` |
| Gobierno documental y herramientas | `governance/` |

---

## 5. Relación con Processes

`governance` y `processes` tienen responsabilidades diferentes.

### Governance

Define:

> qué reglas y políticas deben cumplirse.

Ejemplos:

- cómo documentar;
- qué herramientas utilizar;
- cómo versionar;
- qué convenciones aplicar.

### Processes

Define:

> cómo ejecutar actividades concretas.

Ejemplos:

- GitFlow;
- gestión del backlog;
- Sprint Planning;
- revisiones;
- procedimientos operativos.

La relación general es:

    Governance
        ↓
    establece políticas
        ↓
    Processes
        ↓
    operacionaliza las políticas

---

## 6. Relación con Templates

Las políticas documentales deben reflejarse, cuando corresponda, en las plantillas del proyecto.

Las plantillas ayudan a aplicar de manera consistente:

- estructura;
- encabezados;
- campos mínimos;
- trazabilidad;
- criterios de calidad.

[Consultar Templates](../templates/)

---

## 7. Gobierno documental

La documentación de Red Vital debe mantenerse como una **wiki viva**.

Esto implica que:

1. cada carpeta principal tiene una responsabilidad clara;
2. cada sección utiliza un `README.md` como entrada;
3. los documentos especializados mantienen una única fuente de verdad;
4. los enlaces conectan artefactos relacionados;
5. Git conserva el historial de cambios;
6. los documentos históricos se mantienen separados de los vigentes;
7. la documentación debe actualizarse junto con la solución.

---

## 8. Gobierno de herramientas

Las herramientas utilizadas por Red Vital deben responder a una necesidad concreta.

Una herramienta debe evaluarse considerando:

- propósito;
- impacto;
- mantenimiento;
- seguridad;
- integración;
- compatibilidad;
- beneficio frente a herramientas existentes.

Cuando la incorporación o reemplazo de una herramienta tenga impacto arquitectónico relevante, debe existir una decisión formal mediante ADR.

---

## 9. Trazabilidad

Las políticas de Governance deben favorecer la trazabilidad entre:

    Requisito
        ↓
    Arquitectura
        ↓
    Decisión
        ↓
    Diseño
        ↓
    Implementación
        ↓
    Prueba
        ↓
    Entrega

Las herramientas y reglas documentales deben facilitar esta relación y no convertirse en una barrera para mantenerla.

---

## 10. Control de cambios

Los cambios relevantes a políticas o lineamientos deben:

- registrarse en Git;
- tener una descripción clara;
- revisarse cuando afecten múltiples áreas;
- actualizar los documentos relacionados;
- comunicar cambios que impacten la forma de trabajo del equipo.

Los cambios arquitectónicos derivados de estas políticas deben documentarse adicionalmente mediante ADR cuando corresponda.

---

## 11. Documentación vigente e histórica

La sección Governance contiene las políticas vigentes.

Los documentos históricos o entregas anteriores deben mantenerse fuera de esta carpeta cuando ya no representen las reglas actuales.

Pueden utilizarse:

    deliverables/

o:

    legacy/

según corresponda.

---

## 12. Organización de la sección

La estructura actual es:

    governance/
    ├── README.md
    ├── documentation-guidelines.md
    └── policies-and-tools.md

### `README.md`

Portada e índice de Governance.

### `documentation-guidelines.md`

Reglas de documentación y mantenimiento de la wiki.

### `policies-and-tools.md`

Políticas generales y herramientas oficiales del proyecto.

---

## 13. Criterios de mantenimiento

Los documentos de Governance deben revisarse cuando:

- cambie la estructura del repositorio;
- se adopte una nueva herramienta;
- se retire una herramienta;
- cambie el flujo de trabajo;
- cambien las reglas documentales;
- cambien políticas de seguridad;
- cambien las convenciones de nombres;
- se incorpore un nuevo tipo de artefacto.

---

## 14. Navegación rápida

| Artefacto | Acceso |
|---|---|
| Documentation Guidelines | [Documentation Guidelines](./documentation-guidelines.md) |
| Policies and Tools | [Policies and Tools](./policies-and-tools.md) |
| Arquitectura | [Architecture](../architecture/README.md) |
| Datos | [Data](../data/README.md) |
| Integración | [Integration](../integration/) |
| Infraestructura | [Infrastructure](../infrastructure/) |
| Requisitos | [Requirements](../requirements/) |
| Testing | [Testing](../testing/) |
| Processes | [Processes](../processes/) |
| Templates | [Templates](../templates/) |

---

## 15. Regla principal

La sección `governance` debe permitir responder:

- ¿qué reglas aplican al proyecto?;
- ¿cómo debe mantenerse la documentación?;
- ¿qué herramientas están oficialmente adoptadas?;
- ¿qué prácticas deben seguirse?;
- ¿dónde se documentan los procedimientos concretos?;
- ¿cómo se mantiene la consistencia entre artefactos?

Governance define las reglas transversales del proyecto, mientras que cada área especializada mantiene el detalle de su propia responsabilidad.