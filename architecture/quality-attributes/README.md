# Escenarios de calidad — RedVital

Catálogo de escenarios estímulo–respuesta que expresan de forma verificable los atributos de calidad del sistema. Es el entregable de **HT-12** y la fuente de la sección de atributos de calidad del SAD.

| Campo | Valor |
|---|---|
| **Versión** | 1.0 |
| **Fecha** | 2026-08-31 |
| **Responsable** | Sara — Arquitecta de Software |
| **Norma de referencia** | ISO/IEC 25010:2023 |
| **Deriva de** | `docs/contexto/00-contexto-maestro.md` §7 y `docs/requerimientos/srs.md` §2 |

---

## Cómo se leen

Un atributo de calidad enunciado como adjetivo —«el sistema debe ser seguro»— no se puede verificar ni discutir. El escenario lo convierte en una situación concreta con seis elementos, y sobre todo con una **medida de respuesta** que es un número o una condición binaria.

Cada archivo contiene un escenario. La sección *Verificación* que sigue a la tabla no forma parte de la plantilla original: recoge cómo se comprueba el escenario y qué error se busca evitar, para que quien lo pruebe no tenga que reconstruir la intención.

El campo *HU relacionadas* apunta hoy a los requerimientos del SRS (`RF-XX`) y a las historias técnicas del Sprint 1 (`HT-XX`), que son los identificadores que existen. Cuando el backlog de producto asigne identificadores `HU-XXX`, se añaden aquí.

---

## Catálogo

| ID | Escenario | Atributo de calidad | Subcaracterística | Prioridad |
|---|---|---|---|---|
| [EC-01](EC-01-control-de-acceso-por-jurisdiccion.md) | Control de acceso por jurisdicción | Seguridad | Confidencialidad | Alta |
| [EC-02](EC-02-confidencialidad-de-la-causa-clinica.md) | Confidencialidad de la causa clínica | Seguridad | Confidencialidad | Alta |
| [EC-03](EC-03-autenticidad-de-la-sesion.md) | Autenticidad de la sesión | Seguridad | Autenticidad | Alta |
| [EC-04](EC-04-integridad-del-ciclo-de-vida-de-la-unidad.md) | Integridad del ciclo de vida de la unidad | Seguridad | Integridad | Alta |
| [EC-05](EC-05-no-repudio-de-la-bitacora.md) | No repudio de la bitácora de auditoría | Seguridad | No repudio y responsabilidad | Alta |
| [EC-06](EC-06-resistencia-ante-abuso-automatizado.md) | Resistencia ante abuso automatizado | Seguridad | Resistencia | Media |
| [EC-07](EC-07-disponibilidad-de-la-consulta-de-inventario.md) | Disponibilidad de la consulta de inventario | Fiabilidad | Disponibilidad | Alta |
| [EC-08](EC-08-aislamiento-del-fallo-del-servicio-par.md) | Aislamiento del fallo del servicio par | Fiabilidad | Tolerancia a fallos | Alta |
| [EC-09](EC-09-recuperacion-tras-perdida-de-datos.md) | Recuperación tras pérdida de datos | Fiabilidad | Recuperabilidad | Alta |
| [EC-10](EC-10-registro-de-donacion-idempotente.md) | Registro de donación idempotente | Fiabilidad | Ausencia de fallos | Alta |
| [EC-11](EC-11-reanudacion-de-notificaciones.md) | Reanudación de notificaciones | Fiabilidad | Recuperabilidad | Media |
| [EC-12](EC-12-bloqueo-de-despacho-de-unidad-no-apta.md) | Bloqueo del despacho de una unidad no apta o vencida | Seguridad física | A prueba de fallos | Alta |
| [EC-13](EC-13-exclusion-automatica-de-unidades-vencidas.md) | Exclusión automática de unidades vencidas | Adecuación funcional | Corrección funcional | Alta |
| [EC-14](EC-14-reconocimiento-sin-contraprestacion.md) | Reconocimiento sin contraprestación económica | Adecuación funcional | Pertinencia funcional | Alta |
| [EC-15](EC-15-tiempo-de-respuesta-del-inventario.md) | Tiempo de respuesta de la consulta de inventario | Eficiencia de desempeño | Comportamiento temporal | Alta |
| [EC-16](EC-16-concurrencia-en-pico-de-campana.md) | Concurrencia durante el pico de una campaña | Eficiencia de desempeño | Capacidad | Media |
| [EC-17](EC-17-registro-anonimo-en-tres-pasos.md) | Registro anónimo en tres pasos | Capacidad de interacción | Operabilidad | Alta |
| [EC-18](EC-18-uso-con-lector-de-pantalla-y-sin-raton.md) | Uso con lector de pantalla y sin ratón | Capacidad de interacción | Inclusividad | Media |
| [EC-19](EC-19-proteccion-ante-desecho-accidental.md) | Protección ante el desecho accidental de una unidad | Capacidad de interacción | Protección contra errores de usuario | Alta |
| [EC-20](EC-20-diagnostico-de-un-fallo-en-qa.md) | Diagnóstico de un fallo en el ambiente de QA | Mantenibilidad | Analizabilidad | Media |
| [EC-21](EC-21-cambio-de-la-regla-de-elegibilidad.md) | Cambio de la regla de elegibilidad | Mantenibilidad | Modificabilidad | Media |
| [EC-22](EC-22-evolucion-del-contrato-entre-servicios.md) | Evolución del contrato entre servicios | Compatibilidad | Interoperabilidad | Alta |
| [EC-23](EC-23-puesta-en-marcha-del-sistema-completo.md) | Puesta en marcha del sistema completo | Flexibilidad | Instalabilidad | Alta |
| [EC-24](EC-24-extension-a-otro-tipo-de-donacion.md) | Extensión a otro tipo de donación | Flexibilidad | Adaptabilidad | Baja |

---

## Cobertura de las nueve características de ISO/IEC 25010:2023

La norma en su versión 2023 tiene nueve características, no las ocho de 2011. Las nueve están cubiertas. El reparto no es uniforme y no debería serlo: seguridad y fiabilidad concentran once de los veinticuatro escenarios porque son los atributos que el cliente declaró de primer orden y porque el dominio maneja datos sensibles de salud.

| Característica | Escenarios | Peso |
|---|---|---|
| Seguridad | EC-01 a EC-06 | 6 |
| Fiabilidad | EC-07 a EC-11 | 5 |
| Capacidad de interacción | EC-17, EC-18, EC-19 | 3 |
| Adecuación funcional | EC-13, EC-14 | 2 |
| Eficiencia de desempeño | EC-15, EC-16 | 2 |
| Mantenibilidad | EC-20, EC-21 | 2 |
| Flexibilidad | EC-23, EC-24 | 2 |
| Compatibilidad | EC-22 | 1 |
| Seguridad física | EC-12 | 1 |

Las seis subcaracterísticas de seguridad —confidencialidad, integridad, no repudio, responsabilidad, autenticidad y resistencia— tienen escenario propio. Es la consecuencia directa de RD-08.

---

## Trazabilidad a requerimientos y restricciones

| Origen | Escenarios que lo verifican |
|---|---|
| RNF-01 — no exponer la causa clínica | EC-02, y como condición de contorno EC-05, EC-12, EC-20 |
| RNF-02 — jurisdicción del usuario territorial | EC-01, con EC-03 y EC-06 como apoyo |
| RNF-03 — registro anónimo en tres pasos | EC-17 |
| RNF-04 — consulta de inventario en menos de 3 s | EC-15, EC-16 |
| RNF-08 — exclusión automática de vencidas | EC-13, EC-12 |
| RD-01 — arquitectura distribuida | EC-08, EC-22 |
| RD-03 — todo contenerizado | EC-23, EC-07 |
| RD-08 — seguridad de primer orden | EC-01 a EC-06 |
| Ley 1581 de 2012 — datos sensibles | EC-02, EC-05, EC-20 |
| Decreto 1571 de 1993 — donación no remunerada | EC-14 |
| Resolución 901 de 1996 — disposición final | EC-12, EC-13, EC-19 |

---

## Cuándo se verifica cada uno

El escenario se escribe en el Sprint 1, pero solo se puede comprobar cuando existe aquello que lo realiza. Comprometer una verificación antes de tiempo produce un resultado falso.

| Sprint | Escenarios verificables | Qué lo habilita |
|---|---|---|
| 1 | EC-24 | Se revisa sobre el modelo de datos, no requiere código |
| 2 | EC-22, EC-23 | Contratos publicados y componentes contenerizados |
| 3 | EC-01, EC-02, EC-03, EC-04, EC-05, EC-10, EC-12, EC-13, EC-17, EC-19, EC-21 | Gateway, autenticación y primer incremento funcional desplegado en QA |
| 4 | EC-06, EC-07, EC-08, EC-11, EC-15, EC-16, EC-20 | Observabilidad instalada: sin medición no hay medida de respuesta |
| 5 | EC-09, EC-18 | Ensayo de restauración y revisión de accesibilidad sobre la interfaz ya estable |

---

## Regla de precedencia entre escenarios

Cuando dos escenarios entran en tensión, el orden es:

1. **Seguridad física** — EC-12. Ninguna consideración de usabilidad o desempeño justifica despachar una unidad no apta.
2. **Seguridad y cumplimiento legal** — EC-01, EC-02, EC-14. Son restricciones normativas, no objetivos negociables.
3. **Fiabilidad** — EC-07 a EC-11.
4. **El resto**, que se pondera según el caso.

El conflicto más frecuente es seguridad contra facilidad de uso: EC-17 pide un registro de tres pasos y EC-06 pide contener el abuso del mismo endpoint. Se resuelve en el gateway, con límite de tasa por origen, y no añadiendo un paso de verificación al registro.
