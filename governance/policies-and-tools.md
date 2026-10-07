# Policies and Tools — Red Vital

## 1. Propósito

Este documento define las principales **políticas de trabajo** y las **herramientas oficiales** utilizadas en Red Vital.

Su objetivo es establecer reglas comunes para:

- gestión del código;
- documentación;
- ramas;
- Pull Requests;
- seguimiento de trabajo;
- automatización;
- pruebas;
- seguridad;
- observabilidad;
- gestión de artefactos;
- colaboración del equipo.

Este documento complementa los lineamientos de documentación y los procesos definidos para el proyecto.

---

# 2. Principios generales

Las políticas y herramientas de Red Vital se rigen por los siguientes principios:

1. Utilizar herramientas con propósito definido.
2. Evitar herramientas duplicadas para la misma función sin justificación.
3. Mantener trazabilidad entre trabajo, código y documentación.
4. Automatizar tareas repetitivas cuando sea razonable.
5. Aplicar mínimo privilegio.
6. Mantener secretos fuera del código fuente.
7. Integrar seguridad y calidad dentro del ciclo de desarrollo.
8. Mantener documentación viva y versionada.
9. Evitar dependencias innecesarias de herramientas externas.
10. Revisar periódicamente la utilidad de cada herramienta.

---

# 3. Gestión de código fuente

## 3.1 Git

Git es el sistema de control de versiones oficial del proyecto.

Se utiliza para:

- versionar código;
- versionar documentación;
- mantener historial;
- gestionar ramas;
- revisar cambios;
- recuperar versiones anteriores.

Todos los cambios relevantes deben quedar registrados mediante commits.

---

## 3.2 GitHub

GitHub es la plataforma principal de colaboración y gestión del código.

Se utiliza para:

- repositorios;
- Issues;
- Pull Requests;
- revisión de cambios;
- proyectos;
- Actions;
- documentación;
- trazabilidad.

La documentación principal del proyecto debe permanecer vinculada a los repositorios correspondientes.

---

# 4. Estrategia de ramas

El proyecto utiliza una estrategia de ramas basada en GitFlow adaptado al contexto académico y operativo del equipo.

Las ramas deben crearse para cambios específicos.

Ejemplos:

    feature/nombre-funcionalidad
    fix/nombre-error
    docs/nombre-documentacion
    refactor/nombre-cambio
    chore/nombre-tarea

Los cambios importantes no deben realizarse directamente sobre la rama principal.

---

# 5. Pull Requests

Los cambios relevantes deben integrarse mediante Pull Request cuando sea posible.

Un Pull Request debe:

- tener propósito claro;
- describir el cambio;
- relacionarse con un Issue cuando aplique;
- evitar mezclar cambios no relacionados;
- pasar las validaciones automáticas disponibles;
- recibir revisión cuando corresponda.

Los cambios de documentación también pueden utilizar Pull Requests.

---

# 6. Commits

Los commits deben ser:

- pequeños;
- coherentes;
- trazables;
- descriptivos.

Se recomienda utilizar prefijos como:

    feat:
    fix:
    docs:
    refactor:
    test:
    chore:

Ejemplos:

    docs: update SAD persistence section

    feat: add campaign availability endpoint

    fix: correct institutional validation

    test: add donation service integration test

No se recomiendan mensajes ambiguos como:

    cambios

    arreglos

    final

    listo

---

# 7. Gestión del trabajo

## 7.1 GitHub Issues

Los Issues se utilizan para representar:

- tareas;
- defectos;
- mejoras;
- actividades técnicas;
- trabajo documental;
- investigación.

Cada Issue debe tener, cuando aplique:

- título claro;
- descripción;
- criterio de finalización;
- responsable;
- etiquetas;
- relación con historia o épica;
- dependencias.

---

## 7.2 GitHub Projects

GitHub Projects se utiliza para visualizar y gestionar el avance del trabajo.

Puede utilizar estados como:

- Backlog;
- Ready;
- In Progress;
- Review;
- Done.

Los estados deben representar la situación real del trabajo.

---

# 8. Documentación

La documentación viva del proyecto se mantiene principalmente en Markdown.

GitHub funciona como plataforma de visualización y versionamiento.

Los lineamientos detallados se encuentran en:

[Documentation Guidelines](./documentation-guidelines.md)

---

# 9. Gestión de arquitectura

Las decisiones arquitectónicas relevantes deben documentarse mediante ADR.

Los ADRs se mantienen en:

[Architecture Decision Records](../architecture/adrs/README.md)

Las decisiones no deben depender únicamente de conversaciones informales.

---

# 10. Gestión de APIs

Los contratos HTTP deben mantenerse versionados mediante especificaciones como OpenAPI.

La especificación debe representar el comportamiento real de la API.

Cuando exista una modificación incompatible se debe evaluar:

- versionamiento;
- impacto;
- consumidores afectados;
- transición;
- compatibilidad.

Los contratos se mantienen dentro de la documentación de integración.

[Consultar Integración](../integration/)

---

# 11. Desarrollo de frontend

El frontend debe mantener:

- estructura modular;
- componentes reutilizables;
- separación entre presentación y lógica;
- consistencia visual;
- consumo de contratos explícitos;
- validaciones básicas de entrada.

La línea de diseño específica debe mantenerse en el SDD.

> **Pendiente:** consolidar la línea de diseño a partir de la implementación real del frontend.

---

# 12. Desarrollo backend

Los servicios backend deben:

- mantener responsabilidades claras;
- exponer contratos explícitos;
- evitar acceso directo a persistencias ajenas;
- aplicar validaciones de negocio;
- aplicar autorización;
- producir logs estructurados;
- mantener pruebas adecuadas.

Las reglas arquitectónicas se encuentran en el SAD.

[Consultar SAD](../architecture/SAD.md)

---

# 13. Persistencia

PostgreSQL es la tecnología principal de persistencia relacional.

Las bases deben mantenerse separadas por servicio según las decisiones arquitectónicas vigentes.

Las herramientas de persistencia deben soportar:

- migraciones;
- backups;
- restauración;
- control de acceso;
- trazabilidad.

El detalle se mantiene en:

[Datos](../data/README.md)

---

# 14. Contenedores

Docker se utiliza como mecanismo principal de contenerización.

Los contenedores permiten:

- estandarizar entornos;
- reducir diferencias entre máquinas;
- facilitar despliegue;
- aislar dependencias;
- reproducir configuraciones.

Las imágenes deben mantenerse:

- versionadas;
- mínimas;
- actualizadas;
- sin secretos embebidos.

---

# 15. Docker Compose

Docker Compose puede utilizarse para levantar ambientes locales o conjuntos de servicios cuando resulte útil.

Debe permitir:

- ejecutar los componentes necesarios;
- definir dependencias;
- mantener configuración reproducible;
- evitar configuración manual excesiva.

La infraestructura de ambientes superiores puede utilizar mecanismos adicionales según la arquitectura definida.

---

# 16. Reverse Proxy y entrada

Caddy se utiliza como componente de borde.

Entre sus responsabilidades pueden encontrarse:

- terminación TLS;
- redirección HTTPS;
- headers de seguridad;
- límites básicos;
- logging de acceso;
- enrutamiento hacia el gateway.

Caddy no debe contener lógica de negocio.

---

# 17. API Gateway

El API Gateway centraliza funciones transversales de entrada.

Puede encargarse de:

- routing;
- validación inicial de identidad;
- políticas;
- rate limiting;
- propagación de contexto;
- correlation IDs.

La autorización final debe mantenerse también en los servicios correspondientes.

---

# 18. Observabilidad

La solución debe contar con mecanismos de observabilidad.

## 18.1 Prometheus

Prometheus se utiliza para recolección de métricas.

Las métricas deben:

- evitar datos sensibles;
- utilizar nombres consistentes;
- permitir análisis de salud y rendimiento.

---

## 18.2 Grafana

Grafana se utiliza para visualización de métricas.

Los dashboards deben orientarse a:

- disponibilidad;
- latencia;
- errores;
- uso de recursos;
- comportamiento operativo.

---

## 18.3 Logs

Los logs deben ser estructurados y permitir correlación.

No deben contener:

- contraseñas;
- tokens;
- secretos;
- información sensible innecesaria.

---

# 19. Health Checks

Los servicios deben exponer mecanismos de verificación de salud cuando aplique.

Los health checks pueden utilizarse para:

- validar disponibilidad;
- detectar fallos;
- apoyar monitoreo;
- automatizar recuperación.

No deben exponer información sensible.

---

# 20. Pruebas

Las herramientas de prueba deben seleccionarse según el nivel requerido.

Se deben contemplar:

- pruebas unitarias;
- pruebas de integración;
- pruebas de contratos;
- pruebas de sistema;
- pruebas de calidad;
- pruebas de seguridad cuando corresponda.

El detalle se mantiene en:

[Testing](../testing/README.md)

---

# 21. Calidad de código

El proyecto debe utilizar mecanismos para detectar:

- errores;
- problemas de estilo;
- vulnerabilidades;
- dependencias inseguras;
- código innecesariamente complejo.

Cuando sea posible, estas validaciones deben ejecutarse automáticamente.

---

# 22. CI/CD

GitHub Actions puede utilizarse como herramienta de integración y automatización.

Los workflows pueden ejecutar:

- build;
- pruebas;
- lint;
- validaciones;
- análisis de seguridad;
- generación de artefactos;
- despliegues controlados.

Los pipelines deben evitar incluir secretos directamente en los archivos de workflow.

---

# 23. Seguridad de dependencias

Las dependencias deben revisarse periódicamente.

Se deben evitar:

- versiones abandonadas;
- paquetes inseguros;
- dependencias innecesarias;
- paquetes sin mantenimiento cuando existan alternativas viables.

Las alertas de seguridad deben revisarse y priorizarse según impacto.

---

# 24. Gestión de secretos

Los secretos no deben almacenarse en:

- código;
- commits;
- archivos Markdown;
- imágenes Docker;
- archivos públicos de configuración.

Los secretos deben gestionarse mediante mecanismos seguros del entorno o plataforma correspondiente.

Ejemplos:

- variables de entorno;
- secret stores;
- GitHub Secrets.

---

# 25. Variables de entorno

La configuración dependiente del ambiente debe externalizarse.

Ejemplos:

    DATABASE_URL
    API_BASE_URL
    JWT_SECRET
    LOG_LEVEL

Los valores sensibles no deben incluirse en archivos versionados.

Puede utilizarse un archivo de ejemplo:

    .env.example

sin valores reales.

---

# 26. Ambientes

La configuración debe diferenciar claramente los ambientes.

Ejemplos:

- desarrollo;
- QA;
- producción.

Cada ambiente puede variar en:

- endpoints;
- credenciales;
- logging;
- escalabilidad;
- observabilidad;
- recursos.

El código fuente debe mantenerse lo más independiente posible del ambiente.

---

# 27. Gestión de vulnerabilidades

Las vulnerabilidades detectadas deben:

1. registrarse;
2. clasificarse;
3. priorizarse;
4. corregirse;
5. verificarse.

La prioridad debe considerar:

- severidad;
- exposición;
- probabilidad;
- impacto.

---

# 28. Principio de mínimo privilegio

Los usuarios, servicios y herramientas deben contar únicamente con los permisos necesarios.

Esto aplica a:

- bases de datos;
- repositorios;
- infraestructura;
- servicios;
- pipelines;
- herramientas externas.

Los permisos administrativos no deben utilizarse para operación cotidiana.

---

# 29. Matriz de herramientas

| Área | Herramienta | Uso principal |
|---|---|---|
| Control de versiones | Git | Versionamiento |
| Repositorios | GitHub | Colaboración y trazabilidad |
| Gestión del trabajo | GitHub Issues / Projects | Backlog y seguimiento |
| Documentación | Markdown / GitHub | Wiki viva |
| ADR | Markdown | Decisiones arquitectónicas |
| Contratos API | OpenAPI | Especificación de APIs |
| Persistencia | PostgreSQL | Datos relacionales |
| Contenedores | Docker | Empaquetado y ejecución |
| Orquestación local | Docker Compose | Ambientes locales |
| Borde | Caddy | Reverse proxy y TLS |
| Automatización | GitHub Actions | CI/CD |
| Métricas | Prometheus | Recolección de métricas |
| Dashboards | Grafana | Visualización |
| Diagramas | C4 | Arquitectura |
| Pruebas | Herramientas por componente | Validación |

---

# 30. Incorporación de nuevas herramientas

Una herramienta nueva debe incorporarse únicamente cuando:

- resuelva una necesidad identificada;
- aporte valor frente a las herramientas existentes;
- su costo de mantenimiento sea aceptable;
- sea compatible con la arquitectura;
- tenga soporte suficiente;
- no introduzca riesgo innecesario.

Cuando la elección tenga impacto arquitectónico relevante, debe documentarse mediante ADR.

---

# 31. Herramientas obsoletas

Cuando una herramienta deje de utilizarse:

- debe actualizarse la documentación;
- deben eliminarse referencias activas;
- deben conservarse decisiones históricas relevantes;
- debe documentarse su reemplazo cuando aplique.

---

# 32. Relación con otros artefactos

- [Documentation Guidelines](./documentation-guidelines.md)
- [Arquitectura](../architecture/README.md)
- [ADRs](../architecture/adrs/README.md)
- [Datos](../data/README.md)
- [Integración](../integration/)
- [Infraestructura](../infrastructure/)
- [Testing](../testing/README.md)
- [Processes](../processes/)
- [Templates](../templates/)

---

# 33. Regla de mantenimiento

Este documento debe actualizarse cuando:

- se adopte una nueva herramienta;
- se retire una herramienta;
- cambie una política de trabajo;
- cambie el flujo Git;
- cambie el mecanismo de CI/CD;
- cambie una herramienta de observabilidad;
- cambien las políticas de seguridad;
- cambien las reglas de documentación.

La lista de herramientas debe representar únicamente tecnologías y plataformas vigentes o formalmente planificadas para Red Vital.