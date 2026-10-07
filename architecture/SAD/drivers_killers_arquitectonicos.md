# RedVital — Drivers y Killers Arquitectónicos
**Arkhé Software S.A.S.** | Insumo del Product Owner para el SAD V1
Issue: "Drivers y killers arquitectónicos del sistema" · Entregable: SAD V1

---

## 1. Propósito de este documento

Este documento traduce los objetivos de negocio del proyecto, las preocupaciones identificadas en cada tipo de usuario y las restricciones impuestas por el cliente en **drivers arquitectónicos**: las fuerzas que más condicionan las decisiones estructurales del sistema. Es el insumo del Product Owner para que la Arquitecta de Software lo use como entrada del Documento de Arquitectura (SAD V1), junto con el catálogo de escenarios de calidad ya trabajado por el equipo.

Se sigue la técnica de Bass, Clements y Kazman (*Software Architecture in Practice*): los drivers surgen del cruce entre **objetivos de negocio**, **atributos de calidad** y **restricciones**, y los "killers" son aquellos tan determinantes que, si se ignoraran, obligarían a rediseñar la arquitectura desde cero.

---

## 2. Objetivos de negocio del proyecto

1. Reducir el déficit estructural de sangre en Colombia optimizando el inventario ya captado (redistribución entre bancos) antes de recurrir a nuevas campañas de donación.
2. Aumentar la proporción de donantes habituales frente a donantes ocasionales.
3. Operar bajo cumplimiento estricto del marco normativo de datos sensibles (Ley 1581 de 2012) y de no remuneración de la donación (Decreto 1571 de 1993).
4. Ofrecer al Ministerio de Salud y al INS una vista nacional consolidada de la red de bancos de sangre, que hoy no existe.
5. Dejar una base arquitectónica extensible a otros tipos de donación, en caso de que el proyecto genere interés más allá del curso.

---

## 3. Drivers arquitectónicos (por atributo de calidad)

| Driver | Atributo de calidad | Por qué es un driver |
|---|---|---|
| Ningún dato clínico o diagnóstico puede exponerse ni almacenarse fuera de su propósito autorizado | Seguridad — Confidencialidad | Deriva directamente de la Ley 1581 y del principio de que la seguridad es un atributo de primer orden en este dominio; no es negociable |
| El acceso a información debe respetar estrictamente la jurisdicción territorial del usuario | Seguridad — Control de acceso | El sistema sirve a la vez a bancos individuales y a entidades de supervisión nacional; sin este control, un actor institucional podría ver datos que no le corresponden |
| Toda operación sobre una unidad y todo intento de acceso indebido debe quedar registrado de forma inmutable | Seguridad — Responsabilidad | Sostiene tanto la auditoría regulatoria como la defensa del sistema ante cuestionamientos legales |
| El registro del donante no puede exigir fricción ni datos innecesarios | Capacidad de interacción — Operabilidad | Ataca directamente el objetivo de negocio 2 (aumentar donantes habituales): cualquier fricción en el primer contacto reduce la captación |
| El modelo de dominio debe admitir nuevos tipos de donación sin rediseño | Mantenibilidad — Modularidad | Sustenta el objetivo de negocio 5 y responde a una preocupación explícita del rol de supervisión nacional |
| El registro de una donación no puede fallar por la caída de un servicio externo (notificaciones) | Fiabilidad — Tolerancia a fallos | La operación crítica (registrar sangre captada) no puede depender de un servicio secundario |

---

## 4. Killers arquitectónicos

Un driver se vuelve "killer" cuando no admite una solución conveniente dentro de un estilo arquitectónico simple, y por sí solo obliga a una decisión estructural específica. En RedVital identifico cuatro:

### Killer 1 — Restricción de arquitectura distribuida
El cliente prohíbe explícitamente el monolito modular, pero también prohíbe *asumir* microservicios como respuesta automática. Esto no es un driver de calidad en sentido estricto — es una restricción impuesta — pero funciona como killer porque **obliga a justificar un estilo concreto de descomposición** (por contexto acotado) en vez de heredar un patrón por defecto. Cualquier decisión posterior sobre datos, contratos entre servicios y despliegue depende de esta primero.

### Killer 2 — Reparto obligatorio entre dos plataformas tecnológicas (Java y .NET)
No es una preferencia técnica: es un requisito de proyecto. Esto significa que **el corte de contextos acotados no puede hacerse solo por cohesión de dominio** — también tiene que producir al menos dos servicios independientes, cada uno viable en una de las dos plataformas exigidas. Es un killer porque restringe simultáneamente el espacio de diseño de la descomposición y del reparto tecnológico: la frontera entre servicios tiene que servir a ambos criterios a la vez.

### Killer 3 — Confidencialidad de la causa clínica
No basta con controlar qué se *muestra* en la interfaz: el equipo ya determinó que la causa clínica de una unidad no apta **no puede existir como campo del modelo de datos, en ningún servicio**. Esto es un killer porque condiciona el modelo de dominio desde su diseño inicial — es una decisión que, si se toma tarde (por ejemplo, después de ya tener el modelo de datos construido), obliga a migrar datos y esquemas ya en producción.

### Killer 4 — Coordinación entre servicios para el escalamiento en dos niveles
La lógica central del producto (redistribuir entre bancos antes de movilizar donantes) **cruza la frontera entre el servicio que gestiona donantes, trazabilidad, inventario y transferencias, y el servicio que gestiona campañas, jerarquía territorial e indicadores**. En una arquitectura distribuida sin acceso directo a datos ajenos entre servicios, esta coordinación solo puede resolverse mediante contratos explícitos entre ellos — es el killer que justifica por qué el diseño de servicios debe documentar dependencias por contrato en vez de resolver la coordinación con un simple cruce de datos entre bases.

---

## 5. Cómo se usa esto en el SAD V1

Estos cuatro killers, junto con los seis drivers de la Sección 3, son la entrada directa para:
- Las decisiones arquitectónicas estructurales que resuelven los Killers 1 y 2 (estilo de descomposición y reparto tecnológico).
- El **modelo de datos**, que debe operacionalizar el Killer 3 desde el primer esquema, no como control posterior.
- El diseño de **contratos entre servicios**, que debe resolver el Killer 4 antes de implementar la funcionalidad de transferencias y movilización.
