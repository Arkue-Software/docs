# ADR-007 — API Gateway como punto único de entrada mediante YARP

| Campo | Contenido |
|---|---|
| **Identificador y título** | ADR-007. API Gateway como punto único de entrada mediante YARP. |
| **Estado** | Propuesta para deliberación y aprobación en Mesa de Arquitectura. |
| **Restricciones aplicables** | RD-08, RI-03, RI-04. |
| **Fecha** | 22 de septiembre de 2026. |

---

## Contexto

La arquitectura distribuida de RedVital requiere un punto único de entrada para las solicitudes provenientes de la aplicación web. Sin ese punto, el cliente tendría que conocer directamente la ubicación de cada servicio y cada servicio tendría que exponer y mantener por separado políticas comunes de entrada.

El control de acceso tiene además un peso de primer orden en el sistema. La autorización se expresa por grupo de operaciones y por jurisdicción, y debe aplicarse antes de permitir que una solicitud alcance el recurso solicitado. Sin embargo, concentrar toda la autorización únicamente en un componente de borde permitiría que una invocación directa a un servicio evitara el control.

Se necesita entonces un componente que concentre encaminamiento y políticas transversales de entrada, sin asumir lógica de negocio ni sustituir la validación realizada por cada servicio propietario.

El proyecto es académico. Por ello, además de la capacidad técnica, se considera el costo de adopción: se prefieren herramientas sin costo de licenciamiento y que no introduzcan una plataforma adicional cuya configuración, aprendizaje y operación excedan el alcance del proyecto.

---

## Opciones consideradas

### Opción A — Acceso directo de la aplicación web a cada servicio

La aplicación web conocería la dirección de cada servicio y realizaría las llamadas directamente.

Evita introducir un gateway, pero obliga a publicar múltiples puntos de entrada, acopla la aplicación a la topología interna y distribuye entre los servicios políticas que deberían aplicarse de manera uniforme.

También amplía la superficie expuesta y obliga al cliente a conocer qué servicio resuelve cada operación.

**Descartada.**

### Opción B — YARP sobre ASP.NET Core

YARP actúa como punto único de entrada, recibe las solicitudes externas y las enruta hacia el servicio propietario correspondiente.

Permite incorporar sobre la misma plataforma políticas de autenticación inicial, autorización por ruta y rol, propagación del identificador de correlación y registro de accesos denegados.

No introduce costo de licenciamiento para el alcance académico y se integra con la plataforma .NET ya presente en la arquitectura.

**Seleccionada.**

### Opción C — Ocelot

Ocelot permite implementar un API Gateway dentro del ecosistema .NET y cubre las funciones básicas de encaminamiento y aplicación de políticas.

Se descarta porque, para las necesidades actuales, no aporta una ventaja suficiente frente a YARP que justifique incorporar otra abstracción.

**Descartada.**

### Opción D — Plataforma especializada de gestión de API

Una plataforma dedicada podría asumir encaminamiento, administración de API, autenticación, limitación de tráfico y otras funciones de infraestructura.

Estas capacidades resultan útiles en escenarios de mayor escala, pero introducen una herramienta adicional que el equipo tendría que aprender, configurar, operar y sustentar. Para el alcance académico actual, ese costo operativo no se justifica.

**Descartada.**

---

## Compromiso evaluado

**Se gana.** Un único punto de entrada para la aplicación web, ocultamiento de la topología interna y aplicación uniforme de políticas transversales. El cliente deja de necesitar conocimiento sobre la ubicación de cada servicio y la distribución interna puede cambiar sin modificar sus contratos de consumo.

La herramienta elegida no introduce costo relevante de licenciamiento y reutiliza una plataforma ya presente en el sistema.

**Se sacrifica.** El gateway se convierte en una dependencia común del acceso externo. Si se ejecuta una sola instancia y esta deja de estar disponible, los servicios internos pueden continuar funcionando, pero la aplicación web no puede alcanzarlos por el canal normal.

También se introduce un componente adicional que debe configurarse, probarse y mantenerse. En este proyecto el costo principal de esa decisión no es monetario, sino el tiempo del equipo.

---

## Decisión

1. **YARP sobre ASP.NET Core se adopta como API Gateway de RedVital.**

2. **La aplicación web accede a los servicios de aplicación exclusivamente a través del API Gateway.** Los servicios internos no se publican directamente como puntos de entrada para el cliente.

3. **El gateway realiza la primera validación del token y la compuerta de autorización por ruta y rol** antes de enrutar una solicitud.

4. **La validación realizada por el gateway no sustituye la autorización del servicio propietario.** Cada servicio vuelve a comprobar las restricciones correspondientes a sus datos, especialmente la jurisdicción.

5. **El gateway no contiene lógica de negocio.** No determina estados del dominio, no ejecuta reglas funcionales ni accede directamente a las bases de datos.

6. **Toda solicitud recibe o propaga un identificador de correlación**, utilizado para reconstruir su recorrido entre componentes.

7. **Los accesos denegados generan registro de auditoría.** Su persistencia y consulta se rigen por ADR-010.

8. **La responsabilidad del proxy de borde permanece separada de la del gateway.** La selección entre Caddy y nginx se resuelve independientemente; funciones como terminación TLS pueden permanecer en ese componente.

9. **La arquitectura no depende de que exista una única instancia de YARP.** Para el ambiente académico puede desplegarse una instancia, pero la decisión permite replicarla detrás de un balanceador si un ambiente futuro exige mayor disponibilidad.

10. **La decisión se verifica mediante la prueba de concepto definida en T-410**, que debe demostrar validación de token y compuerta por ruta y rol, incluido el rechazo de una solicitud que no corresponda a la jurisdicción autorizada.

---

## Justificación

La función del gateway es equivalente a una portería común: ofrece una sola entrada y decide hacia qué servicio debe dirigirse cada solicitud. Esto evita que la aplicación web tenga que conocer la distribución interna del sistema.

El gateway concentra controles que son transversales, pero no se convierte en la única barrera de seguridad. La comprobación se repite deliberadamente en el servicio propietario porque una solicitud que alcance directamente la red interna no debe obtener acceso simplemente por haber evitado el gateway.

YARP satisface las funciones requeridas sin introducir una plataforma nueva y se integra con ASP.NET Core, tecnología ya utilizada en el sistema.

El contexto académico refuerza la decisión. El proyecto no necesita las capacidades administrativas de una plataforma empresarial de gestión de API y no obtiene valor suficiente al asumir su costo de aprendizaje y operación. YARP permite demostrar la decisión arquitectónica sin introducir costo de licenciamiento.

La pérdida potencial de disponibilidad por centralizar el acceso se acepta para el ambiente académico y se limita manteniendo el gateway sin lógica de negocio y con posibilidad de replicarse en un despliegue futuro.

---

## Vigencia de lo elegido

La decisión de disponer de un API Gateway como punto único de entrada permanece vigente mientras el sistema conserve múltiples servicios expuestos a un mismo cliente.

La elección concreta de YARP es reversible. Puede sustituirse por otra implementación si cambian las necesidades de tráfico, operación o administración de API, siempre que se mantengan las responsabilidades definidas en este registro y que el cambio no obligue a modificar la lógica de negocio de los servicios.

La versión de ASP.NET Core utilizada debe conservar una ventana de soporte compatible con el horizonte del proyecto; su verificación corresponde a T-103.

---

## Consecuencias

**Sobre la arquitectura.** La aplicación web conoce un solo punto de entrada y deja de depender de la ubicación individual de los servicios.

**Sobre la seguridad.** La autorización se aplica en dos niveles: compuerta inicial en el gateway y validación contextual en cada servicio propietario.

**Sobre la disponibilidad.** Una única instancia del gateway constituye un punto común de fallo para el acceso externo. El ambiente académico acepta esa configuración, pero la arquitectura permite múltiples instancias si posteriormente se requiere mayor disponibilidad.

**Sobre las herramientas.** Se incorpora YARP sobre ASP.NET Core. La selección del proxy de borde continúa siendo independiente. No se introduce una plataforma especializada adicional de gestión de API.

**Sobre el costo.** No se introduce costo significativo de licenciamiento. El costo asumido es principalmente de aprendizaje, configuración, pruebas y mantenimiento.

**Sobre la auditoría.** Los accesos denegados registrados por el gateway se integran con la estrategia definida en ADR-010 y no crean un repositorio independiente.

**Sobre la verificación.** T-410 debe comprobar el comportamiento real del gateway antes de cerrar el sprint. El backlog exige que la prueba de concepto incluya YARP, verificación de token y compuerta por ruta y rol.

**Sobre el Tech Radar.** YARP debe aparecer en el catálogo de herramientas con su anillo y justificación, sin introducir herramientas adicionales que no estén respaldadas por una necesidad concreta.

---

## Responsable y fecha de revisión

| Rol | Responsabilidad |
|---|---|
| Arquitecta de Software | Coherencia entre el gateway, las vistas de arquitectura y las políticas de control de acceso. |
| Desarrollador | Implementación y evidencia de la prueba de concepto del gateway. |
| QA / Tester | Verificación de accesos permitidos y denegados, incluido el acceso directo a los servicios. |

**Próxima revisión:** cierre del Sprint 2, con la prueba de concepto T-410 ejecutada y la ventana de soporte de ASP.NET Core confirmada.
