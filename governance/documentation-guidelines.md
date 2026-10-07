# Documentation Guidelines — Red Vital

## 1. Propósito

Este documento define los lineamientos para crear, organizar, mantener y evolucionar la documentación de **Red Vital**.

Su objetivo es garantizar que la documentación sea:

- consistente;
- navegable;
- trazable;
- actualizada;
- reutilizable;
- comprensible;
- mantenible;
- alineada con la implementación.

La documentación del proyecto se organiza como una **wiki viva basada principalmente en Markdown**, donde los diferentes artefactos se relacionan mediante enlaces y mantienen una única fuente de verdad.

---

## 2. Principios generales

La documentación de Red Vital debe cumplir los siguientes principios:

1. **Una única fuente de verdad:** la información especializada debe mantenerse en un único artefacto principal.
2. **Evitar duplicación:** otros documentos deben resumir o referenciar la información, no copiarla completa.
3. **Navegabilidad:** cada sección debe permitir desplazarse hacia artefactos relacionados.
4. **Trazabilidad:** las decisiones deben poder relacionarse con requisitos, arquitectura, diseño, implementación y pruebas.
5. **Actualización continua:** la documentación debe evolucionar junto con el sistema.
6. **Claridad:** los documentos deben utilizar lenguaje preciso y estructura consistente.
7. **Modularidad:** los documentos extensos deben dividirse cuando exista una razón funcional clara.
8. **Historial:** las versiones anteriores relevantes deben conservarse cuando sea necesario.
9. **Consistencia:** los nombres, rutas y convenciones deben aplicarse de manera uniforme.
10. **Accesibilidad:** la documentación debe poder consultarse directamente desde el repositorio.

---

# 3. Formato principal

El formato principal para la documentación viva de Red Vital es **Markdown (`.md`)**.

Markdown se utiliza porque permite:

- lectura directa desde GitHub;
- navegación mediante enlaces;
- control de versiones;
- revisión mediante Pull Requests;
- edición sencilla;
- integración con imágenes y diagramas;
- menor dependencia de herramientas externas.

Los documentos Markdown constituyen la fuente principal de consulta de la wiki.

---

# 4. Uso de PDF y LaTeX

Los archivos PDF y LaTeX pueden mantenerse cuando:

- correspondan a entregables académicos;
- representen versiones históricas;
- sea necesario generar un documento formal;
- exista contenido que aún no haya sido migrado a Markdown.

Sin embargo, el PDF o LaTeX no debe reemplazar la documentación viva en Markdown cuando exista una versión actualizada dentro de la wiki.

Las versiones históricas pueden mantenerse dentro de carpetas como:

    legacy/

o:

    deliverables/

según corresponda.

---

# 5. Organización de la documentación

La documentación debe organizarse por responsabilidad y no únicamente por tipo de archivo.

La estructura general del repositorio contempla áreas como:

    docs/
    ├── architecture/
    ├── context/
    ├── data/
    ├── deliverables/
    ├── governance/
    ├── infrastructure/
    ├── integration/
    ├── management/
    ├── manuals/
    ├── processes/
    ├── product/
    ├── requirements/
    ├── templates/
    └── testing/

Cada carpeta debe representar una responsabilidad documental clara.

---

# 6. README como portada de cada sección

Cada carpeta principal debe contener un `README.md`.

El README funciona como:

- portada de la sección;
- índice;
- explicación del propósito;
- guía de navegación;
- relación con otros artefactos.

Un README no debe convertirse en una copia completa de todos los documentos de la carpeta.

Debe explicar:

1. qué contiene la sección;
2. para qué sirve;
3. qué documentos existen;
4. cómo se relacionan;
5. dónde consultar el detalle.

---

# 7. Convención de nombres

Los archivos deben utilizar nombres:

- descriptivos;
- consistentes;
- fáciles de identificar;
- sin ambigüedades.

Se recomienda utilizar:

    nombre-del-documento.md

Ejemplos:

    modelo-datos.md
    logical-model.md
    physical-model.md
    data-dictionary.md
    drivers-killers-arquitectonicos.md

Cuando exista una convención propia del artefacto, debe respetarse.

Ejemplo para ADRs:

    ADR-018-estrategia-de-comunicacion.md

---

# 8. Uso de mayúsculas y espacios

Para archivos Markdown se recomienda:

- utilizar minúsculas;
- separar palabras con guiones;
- evitar espacios;
- evitar caracteres especiales cuando sea posible.

Ejemplo recomendado:

    documentation-guidelines.md

En lugar de:

    Documentation Guidelines Final V2.md

Las excepciones deben justificarse por compatibilidad o por existencia histórica del archivo.

---

# 9. Versionamiento de documentos

Los documentos vivos no necesitan crear un archivo nuevo por cada versión.

Se debe evitar:

    SAD-v1.md
    SAD-v2.md
    SAD-v3.md
    SAD-final.md
    SAD-final-definitivo.md

El documento vigente debe conservar una ruta estable:

    SAD.md

Los cambios quedan registrados por Git.

Cuando sea necesario conservar una versión histórica formal, puede almacenarse en:

    legacy/

o:

    deliverables/

---

# 10. Control de versiones interno

Cuando un documento requiera indicar versión explícita, puede incluir una sección como:

| Versión | Fecha | Descripción |
|---|---|---|
| 1.0 | Fecha | Creación inicial. |
| 2.0 | Fecha | Actualización relevante. |

Este control no reemplaza el historial de Git.

---

# 11. Enlaces relativos

Los documentos deben utilizar enlaces relativos siempre que sea posible.

Ejemplo:

    [SAD](../architecture/SAD.md)

En lugar de utilizar enlaces absolutos al repositorio.

Esto permite que:

- los enlaces funcionen entre ramas;
- la documentación pueda moverse dentro del repositorio;
- la wiki sea portable.

---

# 12. Navegación

Los documentos principales deben incluir una sección de navegación cuando resulte útil.

Ejemplo:

    ## Navegación

    - [Arquitectura](../architecture/README.md)
    - [Datos](../data/README.md)
    - [Integración](../integration/README.md)

La navegación debe evitar enlaces innecesarios o repetitivos.

---

# 13. Imágenes y diagramas

Los diagramas e imágenes pueden almacenarse como:

- PNG;
- SVG;
- diagramas como código;
- otros formatos aprobados por el proyecto.

Los archivos deben:

- tener nombres descriptivos;
- mantenerse dentro de la sección correspondiente;
- ser referenciados desde Markdown;
- actualizarse cuando cambie la arquitectura o diseño relacionado.

Ejemplo:

    ![C4 Context](./diagrams/red-vital-c4-context.png)

Los diagramas no deben quedar aislados sin referencia documental.

---

# 14. Diagramas como artefactos

Cada diagrama debe tener una relación clara con al menos uno de los siguientes:

- SAD;
- SDD;
- DD;
- requisitos;
- infraestructura;
- ADR;
- integración.

El diagrama debe representar el estado vigente de la solución.

Cuando un diagrama quede obsoleto:

- se actualiza;
- se reemplaza;
- o se conserva como histórico si existe una razón válida.

---

# 15. Separación entre documentos

La información debe mantenerse en el documento especializado correspondiente.

Ejemplos:

- arquitectura → SAD;
- diseño → SDD;
- datos → DD;
- decisiones → ADRs;
- integración → Integration;
- pruebas → Testing;
- infraestructura → Infrastructure.

Un documento puede resumir información de otro, pero debe enlazar al artefacto principal.

---

# 16. Regla de no duplicación

Si un contenido ya existe de forma detallada en otro artefacto:

1. no debe copiarse completo;
2. se incluye únicamente el contexto necesario;
3. se agrega el enlace correspondiente.

Ejemplo:

El SAD puede explicar la estrategia de persistencia, pero el detalle de tablas, tipos e índices pertenece al DD.

---

# 17. Trazabilidad documental

Los documentos deben permitir establecer relaciones entre:

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

Cuando aplique, los documentos deben enlazar:

- requisito relacionado;
- ADR;
- diagrama;
- contrato;
- diseño;
- prueba.

---

# 18. ADRs

Las decisiones arquitectónicas relevantes deben registrarse mediante ADR.

Un ADR debe incluir como mínimo:

- estado;
- contexto;
- opciones;
- decisión;
- consecuencias.

Los ADRs aceptados no deben eliminarse cuando cambie una decisión.

Deben marcarse como:

- reemplazados;
- obsoletos;
- rechazados;

según corresponda.

---

# 19. Estudios técnicos

Los estudios técnicos, benchmarks o comparaciones deben mantenerse separados de los ADRs cuando contengan análisis detallado.

La relación esperada es:

    Estudio técnico
        ↓
    Evidencia
        ↓
    ADR
        ↓
    Decisión

El estudio sustenta la decisión, pero no la reemplaza.

---

# 20. Documentos pendientes

Cuando una sección aún no esté terminada puede utilizarse una marca explícita:

> **Pendiente:** completar diagrama físico actualizado.

Los pendientes deben:

- ser claros;
- identificar qué falta;
- evitar aparentar que el documento está completo.

No deben utilizarse como sustituto permanente de documentación faltante.

---

# 21. Estado de los documentos

Cuando resulte útil, un documento puede indicar uno de los siguientes estados:

- Borrador
- En revisión
- Vigente
- Obsoleto
- Histórico

El estado debe representar la situación real del artefacto.

---

# 22. Documentación e implementación

La documentación debe mantenerse alineada con la implementación.

Cuando un cambio de código afecte:

- arquitectura;
- contratos;
- datos;
- infraestructura;
- flujos;
- seguridad;
- pruebas;

debe revisarse la documentación relacionada.

La documentación no debe actualizarse semanas después como una actividad separada.

Debe formar parte del mismo flujo de cambio.

---

# 23. Pull Requests

Los cambios documentales relevantes deben revisarse mediante Pull Request cuando el flujo del equipo lo permita.

La revisión debe verificar:

- coherencia;
- enlaces;
- consistencia con la implementación;
- ausencia de duplicación;
- nombres;
- estructura;
- trazabilidad.

---

# 24. Commits de documentación

Los commits deben indicar claramente el propósito del cambio.

Ejemplos:

    docs: update SAD persistence strategy

    docs: add data dictionary

    docs: reorganize architecture wiki

    docs: update C4 diagrams

Se deben evitar mensajes ambiguos como:

    cambios

    arreglo

    final

---

# 25. Templates

Los nuevos documentos deben utilizar las plantillas disponibles cuando exista una plantilla correspondiente.

Las plantillas permiten mantener:

- estructura;
- encabezados;
- criterios mínimos;
- uniformidad.

Las plantillas no deben copiar contenido específico de otros documentos.

---

# 26. Documentación histórica

Los documentos históricos no deben eliminarse cuando:

- representen entregas anteriores;
- sean necesarios para trazabilidad;
- documenten decisiones reemplazadas;
- sean requeridos académicamente.

Deben mantenerse claramente separados de la documentación vigente.

Ejemplos:

    legacy/

    deliverables/

---

# 27. Artefactos entregables

Los documentos dentro de `deliverables/` representan snapshots o entregas formales.

No deben convertirse en la fuente viva de información.

La fuente vigente debe mantenerse dentro de la carpeta funcional correspondiente.

Ejemplo:

    architecture/SAD.md

es la fuente viva.

Mientras que:

    deliverables/sprint-3/SAD.pdf

representa una entrega.

---

# 28. Revisión periódica

La documentación debe revisarse periódicamente para detectar:

- enlaces rotos;
- información duplicada;
- documentos obsoletos;
- nombres inconsistentes;
- diagramas desactualizados;
- referencias faltantes;
- archivos sin responsable documental claro.

La revisión puede realizarse al cierre de cada sprint.

---

# 29. Criterios de calidad documental

Un documento se considera adecuado cuando:

- tiene propósito claro;
- tiene alcance definido;
- utiliza estructura consistente;
- no duplica información innecesariamente;
- enlaza artefactos relacionados;
- representa el estado vigente;
- puede comprenderse sin conocimiento informal del equipo;
- mantiene trazabilidad cuando corresponde.

---

# 30. Regla principal

La documentación de Red Vital debe permitir responder:

- qué existe;
- por qué existe;
- cómo se relaciona;
- dónde está el detalle;
- qué decisión lo originó;
- cómo se implementa;
- cómo se verifica.

La wiki debe servir como punto de entrada al conocimiento del proyecto, no como una colección aislada de archivos.

---

# 31. Navegación

- [Arquitectura](../architecture/README.md)
- [Datos](../data/README.md)
- [Integración](../integration/README.md)
- [Infraestructura](../infrastructure/README.md)
- [Requisitos](../requirements/README.md)
- [Testing](../testing/README.md)
- [Processes](../processes/)
- [Templates](../templates/)

---

# 32. Regla de mantenimiento

Este documento debe actualizarse cuando:

- cambie la estructura documental;
- se adopte una nueva convención;
- cambie el flujo de revisión;
- se incorpore un nuevo tipo de artefacto;
- cambien las reglas de versionamiento;
- cambien las políticas de documentación.

Las nuevas reglas deben aplicarse progresivamente a los documentos existentes sin eliminar información válida o trazabilidad histórica.