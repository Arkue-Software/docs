# Infraestructura — Red Vital

**Proyecto:** Red Vital  
**Documento:** Infraestructura  
**Estado:** Documento vivo  
**Fuente inicial:** Documento de Infraestructura V1.0  
**Última actualización:** 7 de octubre de 2026  

---

# 1. Propósito

Este documento describe cómo se despliega, comunica, protege, observa y recupera la infraestructura de **Red Vital**.

El SAD define las decisiones arquitectónicas y sus fronteras. Este documento transforma dichas decisiones en configuración concreta mediante:

- redes;
- puertos;
- contenedores;
- imágenes;
- secretos;
- límites de recursos;
- bases de datos;
- observabilidad;
- procedimientos de despliegue;
- respaldo;
- restauración;
- mecanismos de verificación.

La infraestructura debe poder contrastarse con la configuración real de los ambientes.

---

# 2. Alcance

Este documento cubre:

- zonas de confianza;
- fronteras;
- redes;
- puertos;
- comunicaciones permitidas;
- autorización entre componentes;
- composición por ambiente;
- contenedores;
- orden de arranque;
- sondas de salud;
- recursos;
- imágenes;
- endurecimiento;
- almacenamiento;
- bases de datos;
- seguridad;
- secretos;
- modelo de amenazas;
- integración continua;
- observabilidad;
- respaldo;
- restauración;
- despliegue;
- reversión;
- configuración por ambiente;
- riesgos;
- verificación.

Este documento no sustituye:

- el SAD;
- los ADRs;
- el SDD;
- el DD;
- los contratos de integración;
- el plan de pruebas.

---

# 3. Relación con otros artefactos

| Artefacto | Relación |
|---|---|
| SAD | Define las decisiones arquitectónicas que la infraestructura materializa. |
| ADRs | Definen decisiones específicas de gateway, identidad, borde, plataforma, servicios y seguridad. |
| SDD | Referencia la vista física y de despliegue. |
| DD | Define el modelo y propiedad de los datos. |
| Integration | Define contratos y comunicaciones entre componentes. |
| Testing | Verifica los controles y escenarios implementados. |
| Governance | Define políticas y herramientas permitidas. |

---

# 4. Principios de infraestructura

La infraestructura de Red Vital debe cumplir los siguientes principios:

1. Los componentes ejecutables deben estar contenerizados.
2. Los servicios deben mantener despliegue independiente.
3. Cada base pertenece únicamente a su servicio propietario.
4. Ningún servicio debe tener acceso de red directo a una base ajena.
5. Solo los componentes estrictamente necesarios deben publicar puertos.
6. Los secretos deben mantenerse fuera del código y de las imágenes.
7. Las configuraciones deben externalizarse por ambiente.
8. Los componentes deben aplicar mínimo privilegio.
9. La infraestructura debe ser verificable mediante procedimientos reproducibles.
10. Los ambientes deben utilizar las mismas imágenes siempre que sea posible.
11. Las diferencias entre ambientes deben resolverse mediante configuración.
12. Las tareas de migración, respaldo y restauración deben ejecutarse de manera controlada.

---

# 5. Topología física vigente

Red Vital dispone de una infraestructura compuesta por **siete máquinas virtuales en la Nube Javeriana**.

La distribución propuesta separa:

- herramientas y despliegue;
- ambiente de QA;
- ambiente de Producción.

La organización general es:

    Nube Javeriana
    ├── VM 1 — Tools
    │
    ├── QA
    │   ├── VM 2 — Base de datos
    │   ├── VM 3 — S+I
    │   └── VM 4 — Aplicaciones
    │
    └── Producción
        ├── VM 5 — Base de datos
        ├── VM 6 — S+I
        └── VM 7 — Aplicaciones

> **Pendiente:** documentar formalmente el significado y alcance exacto de `S+I` según la distribución definitiva de componentes.

---

# 6. VM 1 — Tools

La **VM 1** funciona como centro de herramientas, automatización y soporte de los ambientes.

Sus responsabilidades contemplan:

- runner de GitHub Actions;
- ejecución de pipelines de despliegue;
- monitoreo;
- coordinación de respaldos;
- herramientas operativas;
- automatización relacionada con QA y Producción.

La VM Tools recibe las instrucciones de despliegue desde los repositorios y pipelines del proyecto.

La ruta general es:

    GitHub
        ↓
    GitHub Actions
        ↓
    Runner — VM Tools
        ↓
    QA / Producción

La VPN no constituye el mecanismo normal de despliegue.

Su función principal es permitir:

- soporte;
- administración;
- diagnóstico;
- acceso controlado cuando sea necesario.

---

# 7. Ambiente de QA

QA ocupa tres máquinas virtuales.

## 7.1 VM 2 — Persistencia QA

La VM 2 aloja las persistencias utilizadas por el ambiente de QA.

En ella se despliegan mediante Docker las bases correspondientes a:

- Identidad;
- Institucional;
- Campañas;
- Donación.

Las bases deben conservar:

- aislamiento lógico;
- credenciales independientes;
- volúmenes independientes;
- propiedad exclusiva por servicio;
- respaldo controlado.

La coexistencia física de las bases en una VM no convierte las persistencias en una base compartida.

---

## 7.2 VM 3 — S+I QA

La VM 3 aloja los componentes agrupados en la categoría `S+I` de la topología aprobada.

Los componentes deben ejecutarse mediante Docker.

> **Pendiente:** reemplazar la etiqueta `S+I` por la lista explícita de componentes cuando el equipo cierre definitivamente esta asignación.

---

## 7.3 VM 4 — Aplicaciones QA

La VM 4 aloja los componentes de aplicación correspondientes al ambiente de QA.

Estos componentes se ejecutan mediante Docker.

Su asignación concreta debe mantenerse sincronizada con:

- el SAD;
- el SDD;
- el diagrama de despliegue;
- la configuración ejecutable.

---

# 8. Ambiente de Producción

Producción utiliza tres máquinas virtuales independientes de QA.

Esta separación evita que QA y Producción compartan directamente:

- persistencia;
- contenedores;
- configuración;
- volúmenes;
- credenciales.

Producción puede utilizar inicialmente datos sintéticos o controlados mientras el proyecto no esté autorizado para operar con información real.

---

## 8.1 VM 5 — Persistencia Producción

La VM 5 aloja las bases de datos del ambiente de Producción.

Se mantienen persistencias independientes para:

- Identidad;
- Institucional;
- Campañas;
- Donación.

La separación lógica entre contextos continúa siendo obligatoria aunque las bases compartan una misma VM física.

---

## 8.2 VM 6 — S+I Producción

La VM 6 mantiene los componentes equivalentes a los desplegados en la VM 3 para QA.

Los componentes se ejecutan mediante Docker y utilizan configuración exclusiva del ambiente de Producción.

> **Pendiente:** documentar la composición exacta de `S+I`.

---

## 8.3 VM 7 — Aplicaciones Producción

La VM 7 aloja los componentes de aplicación del ambiente de Producción.

Los servicios desplegados en esta VM deben utilizar:

- imágenes versionadas;
- secretos exclusivos de Producción;
- endpoints de Producción;
- configuración externa;
- observabilidad correspondiente al ambiente.

---

# 9. Separación entre QA y Producción

QA y Producción deben considerarse ambientes independientes.

No deben compartir:

- bases de datos;
- credenciales;
- secretos;
- volúmenes;
- archivos de configuración;
- sesiones;
- datos de aplicación.

Las comunicaciones entre ambos ambientes deben estar prohibidas salvo aquellas expresamente justificadas para operación, monitoreo o despliegue.

La infraestructura debe evaluar y configurar:

- redes separadas;
- reglas de firewall;
- grupos de seguridad;
- listas de control;
- rutas permitidas.

> **Pendiente:** confirmar técnicamente si la Nube Javeriana permite implementar redes o segmentos separados para QA y Producción y documentar el mecanismo utilizado.

---

# 10. Flujo de despliegue

Los despliegues se realizan mediante pipeline.

La ruta esperada es:

    Equipo de desarrollo
            ↓
          GitHub
            ↓
      Push / Pull Request
            ↓
      GitHub Actions
            ↓
       VM 1 — Tools
        ↙         ↘
       QA       Producción

El equipo de desarrollo no debe depender del acceso manual por VPN para realizar despliegues rutinarios.

---

# 11. Despliegue a QA

El despliegue a QA puede ejecutarse automáticamente cuando se cumplan las condiciones definidas por el flujo de integración.

El proceso general es:

1. cambio integrado en el repositorio correspondiente;
2. ejecución del pipeline;
3. construcción o selección de imágenes;
4. validaciones automáticas;
5. conexión del runner con QA;
6. ejecución de migraciones cuando aplique;
7. despliegue de contenedores;
8. health checks;
9. pruebas de humo;
10. registro del resultado.

---

# 12. Despliegue a Producción

Los despliegues a Producción deben requerir mayor control que los despliegues a QA.

La ruta es:

    GitHub
        ↓
    Pipeline validado
        ↓
    VM Tools
        ↓
    Aprobación
        ↓
    Producción

> **Pendiente:** definir formalmente quién aprueba un despliegue a Producción dentro de la Definition of Done y del proceso de despliegue.

Como mínimo debe existir:

- aprobación explícita;
- imagen previamente validada;
- respaldo previo cuando aplique;
- migraciones controladas;
- health checks;
- prueba de humo;
- posibilidad de reversión.

---

# 38. Política de respaldo

Los respaldos de Producción son coordinados desde la infraestructura de Tools.

La VM 1 puede ejecutar o programar los trabajos necesarios para obtener respaldos de las bases alojadas en la VM 5.

La existencia del proceso en Tools no significa que el respaldo deba almacenarse exclusivamente en esa máquina.

La estrategia debe contemplar:

- respaldo individual por base;
- checksum;
- fecha y hora;
- versión del esquema;
- política de retención;
- prueba de restauración.

---

# 39. Ubicación de respaldos

Mantener únicamente los respaldos dentro de la misma Nube Javeriana introduce un dominio de fallo común.

Por esta razón, se debe distinguir entre:

### Copia operativa

Puede mantenerse dentro de la infraestructura institucional para permitir restauraciones rápidas.

### Copia externa

Debe evaluarse una ubicación fuera del mismo dominio de fallo para una versión futura o productiva de la solución.

> **Riesgo pendiente:** los respaldos podrían quedar afectados por una falla general de la Nube Javeriana si no existe una segunda copia externa.

---

# 40. Restauración

La restauración debe ejecutarse independientemente por contexto de persistencia.

El procedimiento general es:

1. identificar la base afectada;
2. detener únicamente los componentes dependientes;
3. validar el respaldo;
4. restaurar la base;
5. ejecutar migraciones posteriores si existen;
6. comprobar integridad;
7. iniciar el servicio;
8. validar health check;
9. ejecutar prueba de humo;
10. registrar tiempos y resultado.

La restauración de una base no debe requerir restaurar todas las demás.

---

# 41. Monitoreo

La VM Tools constituye el punto central de operación del monitoreo definido para la infraestructura.

El monitoreo debe permitir observar, como mínimo:

- disponibilidad;
- salud de los servicios;
- CPU;
- memoria;
- almacenamiento;
- errores;
- latencia;
- estado de contenedores;
- estado de las bases.

La ubicación concreta de Prometheus y Grafana debe mantenerse sincronizada con el despliegue real.

---

# 42. Riesgo de la VM Tools

La VM Tools constituye un punto central de operación.

Una caída de esta máquina puede impedir temporalmente:

- nuevos despliegues;
- ejecución de pipelines asociados al runner;
- monitoreo centralizado;
- ejecución automatizada de determinadas tareas operativas.

Sin embargo, su caída no debe detener automáticamente los servicios ya desplegados en QA o Producción.

Este riesgo se acepta inicialmente por las restricciones de infraestructura del proyecto.

> **Riesgo:** VM Tools constituye un punto único de falla para funciones operativas.

> **Mitigación futura:** evaluar runner alterno, redundancia de monitoreo o procedimientos manuales de contingencia.

---

# 43. VPN

La VPN de la Universidad Javeriana se utiliza como mecanismo de soporte y administración.

No constituye la ruta normal del pipeline.

Puede utilizarse para:

- soporte;
- mantenimiento;
- diagnóstico;
- intervención excepcional;
- administración autorizada.

El acceso debe aplicar mínimo privilegio.

---

# 44. Integración con proveedor OAuth

QA y Producción pueden requerir comunicación con un proveedor OAuth externo.

La infraestructura debe determinar:

- endpoints requeridos;
- tráfico saliente permitido;
- IP pública utilizada por QA;
- IP pública utilizada por Producción;
- requisitos de lista blanca;
- configuración de callbacks;
- secretos independientes por ambiente.

> **Pendiente:** confirmar si el proveedor OAuth requiere listas blancas de IP y registrar las IP autorizadas de QA y Producción.

---

# 45. Firewall y alcanzabilidad

La distribución en múltiples VMs exige que las comunicaciones permitidas se implementen también a nivel de red.

La matriz de alcanzabilidad debe especificar:

| Origen | Destino | Puerto | Propósito | Ambiente |
|---|---|---:|---|---|
| Tools | QA | Según despliegue | Pipeline | QA |
| Tools | Producción | Según despliegue | Pipeline | Producción |
| Apps QA | Datos QA | 5432 o según componente | Persistencia | QA |
| Apps Prod | Datos Prod | 5432 o según componente | Persistencia | Producción |
| QA | OAuth | HTTPS 443 | Autenticación externa | QA |
| Producción | OAuth | HTTPS 443 | Autenticación externa | Producción |

La matriz definitiva debe incluir todos los componentes y aplicar el principio:

> Todo tráfico no explícitamente permitido debe considerarse prohibido.

---

# 46. Configuración por ambiente

QA y Producción utilizan imágenes equivalentes, pero configuraciones diferentes.

Se deben mantener separados:

- variables de ambiente;
- credenciales;
- dominios;
- URLs;
- claves;
- conexiones de bases;
- configuraciones OAuth;
- logging;
- observabilidad.

Ejemplo conceptual:

    QA
    ├── QA_DATABASE_*
    ├── QA_OAUTH_*
    └── QA_SECRET_*

    Producción
    ├── PROD_DATABASE_*
    ├── PROD_OAUTH_*
    └── PROD_SECRET_*

Los nombres físicos concretos pueden diferir, pero la separación debe mantenerse.

---

# 49. Diagrama de despliegue

La vista de despliegue vigente debe representar las siete máquinas virtuales disponibles.

La estructura base es:

    Nube Javeriana
    │
    ├── VM 1
    │   └── Tools
    │
    ├── QA
    │   ├── VM 2 — Base de datos
    │   ├── VM 3 — S+I
    │   └── VM 4 — Apps
    │
    └── Producción
        ├── VM 5 — Base de datos
        ├── VM 6 — S+I
        └── VM 7 — Apps

También deben representarse:

- GitHub;
- GitHub Actions;
- flujo de despliegue;
- VPN;
- proveedor OAuth;
- conexiones entre máquinas;
- separación QA / Producción.

El diagrama actualizado debe mantenerse dentro de:

[Diagramas de Arquitectura](../architecture/diagrams/README.md)

---

# 50. Pendientes de infraestructura

Antes de considerar cerrada la topología física deben resolverse los siguientes puntos:

- [ ] Definir qué componentes forman `S+I`.
- [ ] Definir exactamente qué servicios quedan en VM 3 y VM 6.
- [ ] Definir exactamente qué componentes quedan en VM 4 y VM 7.
- [ ] Confirmar separación de red entre QA y Producción.
- [ ] Actualizar la matriz de alcanzabilidad entre las siete VMs.
- [ ] Definir reglas de firewall.
- [ ] Confirmar IPs de salida de QA y Producción.
- [ ] Confirmar requisitos de lista blanca del proveedor OAuth.
- [ ] Definir quién aprueba despliegues a Producción.
- [ ] Definir ubicación de la segunda copia de respaldos.
- [ ] Definir contingencia ante caída de VM Tools.
- [ ] Actualizar el diagrama de despliegue.
- [ ] Validar recursos de CPU, memoria y disco de cada VM.
- [ ] Validar conectividad real mediante pruebas.