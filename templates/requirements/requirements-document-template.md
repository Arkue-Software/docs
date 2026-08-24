# Especificación de requisitos - [Nombre del alcance]

> **Instrucciones de uso - eliminar al crear un documento real:**  
> Esta plantilla se utiliza para documentar requisitos funcionales o no funcionales de RedVital.  
>
> Copia este archivo en `requirements/` y nómbralo según su alcance:
>
> - `functional-requirements.md` para requisitos funcionales.
> - `non-functional-requirements.md` para requisitos no funcionales generales.
> - `security-requirements.md` para los requisitos de seguridad.
>
> Cada requisito debe tener un identificador único, un criterio verificable y un enlace al ítem correspondiente de Jira. Las historias de usuario individuales se gestionan en Jira; este documento las agrupa y permite mantener trazabilidad con pruebas y arquitectura.

## Control del documento

| Campo | Información |
|---|---|
| Código del documento | `REQ-[FR/NFR]-001` |
| Versión | `0.1.0` |
| Estado | `Borrador / En revisión / Aprobado` |
| Responsable del documento | `[Product Owner / Responsable de requisitos]` |
| Revisado por | `[Arquitecto de software / QA]` |
| Aprobado por | `[Product Owner]` |
| Fecha de creación | `AAAA-MM-DD` |
| Última actualización | `AAAA-MM-DD` |
| Clasificación | `Interno` |
| Épicas o ítems relacionados de Jira | `[Enlaces]` |
| Documentos relacionados | `[Enlaces a ADRs, arquitectura o pruebas]` |

## Historial de cambios

| Versión | Fecha | Descripción del cambio | Autor | Aprobado por |
|---|---|---|---|---|
| 0.1.0 | AAAA-MM-DD | Creación inicial del documento | `[Nombre]` | Pendiente |

---

## 1. Propósito

[Explicar qué tipo de requisitos se documentan y por qué son necesarios para RedVital.]

## 2. Alcance

### 2.1 Incluye

- [Módulos, procesos o atributos de calidad cubiertos.]

### 2.2 No incluye

- [Elementos excluidos.]

## 3. Fuentes y restricciones

| Fuente o restricción | Descripción | Impacto en los requisitos |
|---|---|---|
| `[Necesidad del cliente]` | `[Descripción]` | `[Impacto]` |
| `[Norma o ley aplicable]` | `[Descripción]` | `[Impacto]` |
| `[Restricción académica]` | `[Descripción]` | `[Impacto]` |

## 4. Catálogo de requisitos

| ID | Tipo | Requisito | Prioridad | Criterio de aceptación o métrica | Ítem de Jira |
|---|---|---|---|---|---|
| `FR-001 / NFR-001` | `Funcional / No funcional` | El sistema deberá `[descripción verificable]`. | `Must / Should / Could` | `[Criterio Dado-Cuando-Entonces o meta medible]` | `[RV-000]` |

## 5. Trazabilidad

| ID del requisito | Épica o historia de usuario | Decisión arquitectónica | Caso de prueba | Estado |
|---|---|---|---|---|
| `[FR-001 / NFR-001]` | `[RV-000]` | `[ADR-000 o N/A]` | `[TC-000]` | `Pendiente / En desarrollo / Validado` |

## 6. Dependencias y riesgos

| Elemento | Tipo | Impacto | Responsable | Acción o decisión requerida |
|---|---|---|---|---|
| `[Dependencia, riesgo o pregunta]` | `Dependencia / Riesgo / Pregunta` | `Alto / Medio / Bajo` | `[Nombre]` | `[Acción]` |

## 7. Referencias

- [Enlace al tablero de Jira.]
- [Enlace al documento de arquitectura.]
- [Enlace a ADRs relacionados.]
- [Enlace al plan de pruebas.]
- [Norma, ley o fuente aplicable.]

## 8. Revisión y aprobación

| Actividad | Persona | Fecha | Resultado |
|---|---|---|---|
| Revisión de requisitos | `[Nombre]` | AAAA-MM-DD | `Pendiente / Aprobado` |
| Revisión técnica | `[Nombre]` | AAAA-MM-DD | `Pendiente / Aprobado` |
| Aprobación final | `[Nombre]` | AAAA-MM-DD | `Pendiente / Aprobado` |