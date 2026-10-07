# Infrastructure — Red Vital

Esta sección contiene la documentación de **infraestructura de Red Vital**.

Su propósito es describir cómo se materializan físicamente las decisiones arquitectónicas mediante:

- máquinas virtuales;
- redes;
- contenedores;
- bases de datos;
- despliegue;
- seguridad;
- observabilidad;
- respaldos;
- configuración por ambiente;
- mecanismos de verificación.

La infraestructura debe mantenerse alineada con el SAD, los ADRs, el SDD, el DD y la configuración ejecutable del proyecto.

---

## 1. Documento principal de infraestructura

El documento principal de esta sección es:

[Consultar Infrastructure](./infrastructure.md)

Este documento describe:

- topología física;
- distribución por máquinas;
- separación entre QA y Producción;
- redes;
- puertos;
- componentes;
- contenedores;
- persistencia;
- seguridad;
- autenticación entre componentes;
- secretos;
- monitoreo;
- respaldos;
- restauración;
- despliegue;
- reversión;
- riesgos;
- verificación.

---

## 2. Topología actual

Red Vital dispone de una infraestructura compuesta por **siete máquinas virtuales en la Nube Javeriana**.

La distribución actual propuesta es:

    Nube Javeriana
    │
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

La VM de Tools funciona como centro de:

- automatización;
- runner de GitHub Actions;
- monitoreo;
- soporte;
- coordinación de despliegues;
- coordinación de respaldos.

---

## 3. Ambientes

La infraestructura diferencia al menos los siguientes ambientes:

### Desarrollo

Se ejecuta principalmente en los equipos de los integrantes.

Características:

- ejecución local;
- Docker;
- datos sintéticos;
- configuración de desarrollo;
- reconstrucción sencilla;
- sin respaldo obligatorio.

### QA

Se despliega en las máquinas virtuales:

- VM 2;
- VM 3;
- VM 4.

QA se utiliza para:

- pruebas;
- integración;
- validación;
- monitoreo;
- pruebas de carga;
- verificación de controles;
- pruebas de despliegue;
- pruebas de restauración.

### Producción

Se despliega en:

- VM 5;
- VM 6;
- VM 7.

Producción mantiene configuración, credenciales y persistencia independientes de QA.

Mientras el proyecto no esté autorizado para operar con datos reales, este ambiente puede utilizar datos sintéticos o controlados.

---

## 4. Separación entre QA y Producción

QA y Producción deben mantenerse separados.

No deben compartir:

- bases de datos;
- volúmenes;
- credenciales;
- secretos;
- sesiones;
- archivos de configuración;
- persistencia de aplicación.

Las comunicaciones entre ambientes deben limitarse a las necesarias para:

- despliegue;
- monitoreo;
- administración;
- respaldo;
- soporte.

Todo tráfico no explícitamente permitido debe considerarse prohibido.

---

## 5. Herramientas y automatización

La VM 1 centraliza las principales herramientas operativas.

La ruta esperada de despliegue es:

    Equipo de desarrollo
            ↓
          GitHub
            ↓
      GitHub Actions
            ↓
       VM 1 — Tools
        ↙         ↘
       QA       Producción

La VPN se mantiene como mecanismo de:

- soporte;
- administración;
- diagnóstico;
- contingencia.

No constituye el mecanismo normal de despliegue.

---

## 6. Contenedores

Los componentes de Red Vital se ejecutan mediante Docker.

La infraestructura debe mantener:

- imágenes versionadas;
- configuración externa;
- mínimo privilegio;
- límites de recursos;
- health checks;
- separación de persistencias;
- ausencia de secretos dentro de las imágenes.

Las imágenes propias deben evitar incluir elementos innecesarios para ejecución.

---

## 7. Persistencia

Red Vital utiliza PostgreSQL como tecnología principal de persistencia.

Se mantienen cuatro contextos principales:

| Persistencia | Servicio propietario |
|---|---|
| Identidad | Servicio de Identidad |
| Institucional | Servicio Institucional |
| Campañas | Servicio de Campañas |
| Donación | Servicio de Donación |

La coexistencia de varias bases en una misma VM no elimina su separación lógica.

Cada base mantiene:

- credenciales;
- volumen;
- migraciones;
- permisos;
- respaldo;
- restauración;

de forma independiente.

[Consultar Data](../data/README.md)

---

## 8. Redes y alcanzabilidad

La infraestructura debe documentar de forma explícita:

- origen;
- destino;
- puerto;
- propósito;
- ambiente;
- mecanismo de autorización.

Las reglas de red deben respetar el principio:

> Todo tráfico no explícitamente permitido debe considerarse prohibido.

La matriz de alcanzabilidad debe mantenerse sincronizada con la topología real de las siete máquinas virtuales.

---

## 9. Seguridad de infraestructura

La infraestructura aplica defensa en profundidad.

Los controles se distribuyen entre:

- borde;
- gateway;
- servicios;
- bases de datos;
- redes;
- credenciales;
- secretos;
- contenedores.

Los componentes deben aplicar:

- mínimo privilegio;
- separación de roles;
- aislamiento;
- protección de secretos;
- restricción de puertos;
- control de acceso administrativo;
- endurecimiento de contenedores.

---

## 10. Observabilidad

La infraestructura contempla mecanismos de observabilidad mediante:

- métricas;
- Prometheus;
- Grafana;
- logs estructurados;
- correlation IDs;
- health checks;
- alertas.

La observabilidad debe permitir detectar:

- indisponibilidad;
- errores;
- latencia;
- consumo de CPU;
- consumo de memoria;
- problemas de conectividad;
- fallos de componentes.

---

## 11. Respaldo y recuperación

Las persistencias deben poder respaldarse y restaurarse de manera independiente.

La estrategia debe contemplar:

- backups;
- checksums;
- retención;
- restauración;
- validación;
- pruebas periódicas.

La VM Tools puede coordinar los procesos de respaldo, pero debe evaluarse una copia externa para evitar dependencia total de la misma nube.

---

## 12. Despliegue

Los despliegues deben realizarse mediante pipeline.

QA y Producción utilizan el mismo principio:

    Código
      ↓
    Pipeline
      ↓
    Imagen versionada
      ↓
    Despliegue
      ↓
    Health check
      ↓
    Smoke test

Producción debe incorporar aprobación explícita antes del despliegue.

---

## 13. Relación con arquitectura

La infraestructura implementa decisiones definidas en arquitectura.

La relación principal es:

    SAD / ADR
        ↓
    Infraestructura
        ↓
    Configuración ejecutable
        ↓
    Despliegue
        ↓
    Verificación

Consultar:

- [Architecture](../architecture/README.md)
- [SAD](../architecture/SAD.md)
- [ADRs](../architecture/adrs/README.md)
- [SDD](../architecture/SDD.md)

---

## 14. Relación con integración

La infraestructura habilita físicamente las comunicaciones definidas en integración.

Los contratos determinan:

- quién se comunica;
- con quién;
- mediante qué protocolo;
- qué operación ejecuta.

La infraestructura determina:

- si existe conectividad;
- qué puerto se habilita;
- qué red permite la comunicación;
- qué controles protegen la comunicación.

[Consultar Integration](../integration/)

---

## 15. Relación con testing

Los controles de infraestructura deben ser verificables.

Testing debe poder validar:

- puertos;
- redes;
- aislamiento;
- credenciales;
- health checks;
- backups;
- restore;
- monitoreo;
- despliegue;
- reversión;
- seguridad.

[Consultar Testing](../testing/)

---

## 16. Riesgos principales

Los principales riesgos de infraestructura que deben mantenerse bajo seguimiento incluyen:

- VM Tools como punto único de falla operativo;
- ausencia de copia externa de respaldo;
- posible falta de separación de red entre QA y Producción;
- dependencia de una sola nube institucional;
- errores en reglas de firewall;
- exposición accidental de puertos;
- configuración incorrecta de secretos;
- fallo de conectividad con proveedor OAuth.

---

## 17. Pendientes

Actualmente se encuentran pendientes:

- [ ] definir composición exacta de `S+I`;
- [ ] definir qué servicios quedan en VM 3 y VM 6;
- [ ] definir qué componentes quedan en VM 4 y VM 7;
- [ ] cerrar separación de red QA / Producción;
- [ ] actualizar matriz de alcanzabilidad;
- [ ] cerrar reglas de firewall;
- [ ] confirmar IPs de salida;
- [ ] validar requisitos del proveedor OAuth;
- [ ] definir aprobación de despliegue a Producción;
- [ ] definir segunda ubicación de respaldos;
- [ ] definir contingencia ante caída de VM Tools;
- [ ] validar recursos por VM;
- [ ] actualizar diagrama de despliegue.

---

## 18. Organización de la sección

La estructura actual es:

    infrastructure/
    ├── README.md
    ├── infrastructure.md
    └── legacy/
        └── infraestructura.pdf

### `README.md`

Portada e índice de infraestructura.

### `infrastructure.md`

Documento vivo con la infraestructura vigente.

### `legacy/infraestructura.pdf`

Versión histórica previa utilizada como referencia durante la migración a la wiki.

---

## 19. Regla de mantenimiento

La carpeta `infrastructure` debe representar la infraestructura vigente de Red Vital.

Debe actualizarse cuando:

- cambie una VM;
- cambie la distribución de componentes;
- cambien redes;
- cambien puertos;
- cambie un firewall;
- cambie un secreto;
- cambie la estrategia de backup;
- cambie el pipeline;
- cambie el monitoreo;
- cambie el despliegue;
- cambie una decisión arquitectónica que afecte infraestructura.

La configuración ejecutable y la documentación deben mantenerse sincronizadas.

---

## 20. Navegación rápida

| Artefacto | Acceso |
|---|---|
| Infrastructure | [Infrastructure](./infrastructure.md) |
| Architecture | [Architecture](../architecture/README.md) |
| SAD | [SAD](../architecture/SAD.md) |
| SDD | [SDD](../architecture/SDD.md) |
| Diagramas | [Diagrams](../architecture/diagrams/README.md) |
| Data | [Data](../data/README.md) |
| Integration | [Integration](../integration/) |
| Testing | [Testing](../testing/) |
| Governance | [Governance](../governance/README.md) |