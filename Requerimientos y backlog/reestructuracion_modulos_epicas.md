# RedVital — Reestructuración de Módulos a Épicas
**Arkhé Software S.A.S.** | Insumo del Product Owner
Issue: "Reestructurar módulos funcionales para que coincidan uno a uno con las épicas" · Entregable: SRS V2 / Backlog V2

---

## 1. Estado de este issue

La reestructuración de módulos ya se definió y quedó reflejada en la especificación de requerimientos vigente del proyecto. Este documento es la nota de respaldo del Product Owner: explica el criterio aplicado desde la perspectiva de negocio y deja registrada la correspondencia que el Backlog V2 debe seguir.

## 2. Criterio aplicado

Un módulo funcional —y por lo tanto una épica de negocio— solo existe si cumple **las tres condiciones simultáneamente**:
1. Es dueño exclusivo de al menos una entidad del dominio (propiedad de datos).
2. Existe un usuario que trabaja dentro de su frontera.
3. Sus términos forman un contexto acotado coherente.

Lo que no cumple las tres condiciones deja de ser módulo/épica y se reclasifica como **característica** de otro módulo, **servicio transversal**, o **atributo de calidad**.

## 3. Qué cambió y por qué, en términos de negocio

| Elemento | Antes | Ahora | Justificación de negocio |
|---|---|---|---|
| Gamificación (reconocimiento no monetario) | Módulo/épica propia | Característica de **Gestión de Donantes** | Las insignias no tienen entidad propia ni un usuario que las "administre" — son un derivado del historial de donación. Sacarlas como módulo aparte fragmentaba innecesariamente el dominio del donante. |
| Extensibilidad a otros tipos de donación | Módulo/épica propia | Atributo de calidad (mantenibilidad) + restricción de diseño interna | No es una funcionalidad que un usuario opere: es una propiedad del modelo de dominio. Convertirla en "módulo" obligaba a inventar historias sin usuario real detrás. |
| Restricción de visibilidad territorial | Parte del módulo de administración territorial | Atributo de calidad (control de acceso), implementado como servicio transversal | Restringir por jurisdicción es un comportamiento que aplica a *todos* los módulos, no una funcionalidad exclusiva de uno. El módulo territorial conserva solo lo que sí es funcional: mantener la jerarquía y el registro de instituciones. |

## 4. Correspondencia de identificadores

El catálogo de módulos vigente queda en siete módulos de negocio, más tres servicios transversales que ninguna épica reclama en exclusiva (notificaciones, auditoría, y autenticación/autorización). El Backlog V2 sigue esta numeración de forma estricta — ver `backlog_v2.md`.

## 5. Impacto directo en el backlog

Esta reestructuración obliga a tres ajustes en las historias de usuario que ya existían, más allá del simple cambio de número de módulo:
- Las historias de Gamificación mantienen su contenido pero ahora se marcan como **Característica** de la épica Gestión de Donantes, no como épica propia.
- Las historias de "visibilidad restringida" y "denegación de acceso" que antes vivían como historias de negocio del módulo territorial se **reescriben**: la parte funcional (mantener jerarquía e instituciones) queda como historia de negocio; la parte de control de acceso pasa a ser una historia técnica de arquitectura (ver `backlog_v2.md`, Épica A).
- Se agrega una historia de negocio nueva que no existía en el backlog anterior: **consulta de bitácora por parte de un rol de auditoría**, al formalizarse ese tipo de usuario con su propia preocupación específica dentro del alcance del producto.
