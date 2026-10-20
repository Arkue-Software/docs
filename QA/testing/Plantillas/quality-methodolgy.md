# Metodología de Calidad — Red Vital

## 1. Propósito

Este documento define la metodología utilizada por **Red Vital** para asegurar y verificar la calidad de la solución durante su ciclo de desarrollo.

La metodología establece:

- cómo se incorporan criterios de calidad;
- qué artefactos sirven como entrada para las pruebas;
- qué niveles de prueba se aplican;
- cómo se registran defectos;
- cómo se mantiene trazabilidad;
- qué criterios permiten considerar una funcionalidad validada;
- cómo se producen y conservan evidencias.

La calidad no se considera una actividad ejecutada únicamente al final del desarrollo.

Debe incorporarse durante:

- requisitos;
- arquitectura;
- diseño;
- implementación;
- integración;
- despliegue;
- evaluación.

---

# 2. Objetivos de calidad

La metodología busca asegurar que Red Vital:

1. cumpla los requisitos funcionales definidos;
2. satisfaga los atributos de calidad prioritarios;
3. mantenga comportamiento consistente entre componentes;
4. preserve seguridad y aislamiento;
5. detecte defectos antes de las entregas;
6. permita reproducir las pruebas;
7. mantenga evidencia de los resultados;
8. conserve trazabilidad entre requisitos, pruebas y defectos;
9. permita verificar la infraestructura y los contratos;
10. facilite la evolución segura de la solución.

---

# 3. Principio de calidad continua

La calidad se incorpora durante todo el ciclo de vida.

La relación general es:

    Requisitos
        ↓
    Arquitectura
        ↓
    Diseño
        ↓
    Implementación
        ↓
    Pruebas
        ↓
    Evidencia
        ↓
    Retroalimentación

Un defecto encontrado durante pruebas puede producir cambios en:

- requisitos;
- arquitectura;
- diseño;
- implementación;
- configuración;
- documentación.

---

# 4. Entradas de la metodología

Las pruebas de Red Vital se derivan principalmente de:

- SRS;
- backlog;
- criterios de aceptación;
- escenarios de calidad;
- SAD;
- SDD;
- ADRs;
- DD;
- contratos de integración;
- OpenAPI;
- infraestructura;
- riesgos identificados.

No deben diseñarse pruebas únicamente a partir del comportamiento visible de la aplicación.

---

# 5. Trazabilidad

Cada prueba debe poder relacionarse con uno o varios elementos que justifican su existencia.

La trazabilidad esperada es:

    Requisito / Escenario / Riesgo
               ↓
          Caso de prueba
               ↓
            Ejecución
               ↓
            Resultado
               ↓
            Evidencia

Cuando una prueba falla:

    Resultado fallido
           ↓
        Defecto
           ↓
       Corrección
           ↓
       Reejecución

---

# 6. Niveles de prueba

Red Vital contempla diferentes niveles de prueba.

## 6.1 Pruebas unitarias

Validan unidades pequeñas de implementación de manera aislada.

Pueden cubrir:

- funciones;
- clases;
- validadores;
- reglas de negocio;
- transformaciones.

Son responsabilidad principalmente del equipo de desarrollo.

---

## 6.2 Pruebas de integración

Validan la interacción entre componentes.

Pueden verificar:

- servicio → base propia;
- gateway → servicio;
- servicio → servicio;
- productor → consumidor;
- contratos;
- eventos;
- autenticación entre componentes.

---

## 6.3 Pruebas de contrato

Verifican que productores y consumidores mantengan compatibilidad.

Pueden utilizar como referencia:

- OpenAPI;
- schemas;
- eventos;
- estructuras de mensajes;
- códigos de respuesta.

Un cambio incompatible debe detectarse antes de afectar consumidores.

---

## 6.4 Pruebas funcionales

Verifican que las funcionalidades respondan a los requisitos y criterios de aceptación.

Se diseñan a partir de:

- historias de usuario;
- requisitos funcionales;
- flujos;
- reglas de negocio.

---

## 6.5 Pruebas end-to-end

Validan flujos completos atravesando varios componentes.

Ejemplo conceptual:

    Usuario
       ↓
    Frontend
       ↓
    Gateway
       ↓
    Servicio
       ↓
    Persistencia

Estas pruebas se reservan para procesos relevantes y no sustituyen pruebas unitarias o de integración.

---

## 6.6 Pruebas de seguridad

Validan controles como:

- autenticación;
- autorización;
- roles;
- jurisdicción;
- aislamiento;
- protección de rutas internas;
- acceso a persistencias;
- exposición de puertos;
- secretos;
- headers;
- rate limiting.

---

## 6.7 Pruebas de rendimiento

Evalúan:

- latencia;
- throughput;
- comportamiento bajo carga;
- uso de CPU;
- memoria;
- conexiones;
- degradación.

Las pruebas de carga deben ejecutarse desde un equipo distinto al sistema medido cuando sea posible.

---

## 6.8 Pruebas de infraestructura

Validan:

- redes;
- puertos;
- matriz de alcanzabilidad;
- health checks;
- contenedores;
- aislamiento de bases;
- backups;
- restore;
- monitoreo;
- despliegue;
- reversión.

---

## 6.9 Pruebas de recuperación

Evalúan la capacidad para recuperar componentes o datos después de un fallo.

Pueden incluir:

- restauración de bases;
- reinicio de servicios;
- pérdida de contenedor;
- reversión de despliegue;
- recuperación de configuración.

---

# 7. Técnicas de diseño de pruebas

Dependiendo del requisito pueden utilizarse técnicas como:

- partición de equivalencia;
- análisis de valores límite;
- tablas de decisión;
- transición de estados;
- casos positivos;
- casos negativos;
- pruebas basadas en escenarios;
- pruebas basadas en riesgos.

La técnica utilizada debe seleccionarse según el comportamiento que se desea verificar.

---

# 8. Pruebas positivas y negativas

Los casos de prueba no deben limitarse al flujo correcto.

Cada funcionalidad debe considerar, cuando corresponda:

### Casos positivos

Validan operaciones permitidas.

### Casos negativos

Validan:

- entradas inválidas;
- permisos insuficientes;
- recursos inexistentes;
- estados incompatibles;
- operaciones prohibidas;
- fallos de dependencias;
- datos incorrectos.

---

# 9. Pruebas basadas en riesgos

Los riesgos arquitectónicos, de seguridad e infraestructura deben influir en la prioridad de pruebas.

Los elementos con:

- mayor impacto;
- mayor probabilidad de fallo;
- mayor exposición;
- mayor dependencia;

deben recibir mayor cobertura.

---

# 10. Ambientes de prueba

Las pruebas pueden ejecutarse en:

## Desarrollo

Utilizado principalmente para:

- pruebas unitarias;
- integración inicial;
- validaciones locales.

## QA

Es el ambiente principal para:

- integración;
- sistema;
- seguridad;
- rendimiento;
- infraestructura;
- observabilidad;
- recuperación.

QA debe mantenerse separado de Producción.

## Producción

Las pruebas directas en Producción deben limitarse a verificaciones controladas como:

- smoke tests;
- health checks;
- monitoreo.

No deben realizarse pruebas destructivas sobre Producción.

---

# 11. Datos de prueba

Los ambientes académicos y de QA deben utilizar datos:

- sintéticos;
- controlados;
- reproducibles;
- no sensibles.

No deben utilizarse datos personales reales cuando no exista autorización explícita.

Los conjuntos de prueba deben permitir cubrir:

- casos normales;
- límites;
- errores;
- permisos;
- estados diferentes.

---

# 12. Automatización

Las pruebas repetibles deben automatizarse cuando el costo lo justifique.

La automatización puede incluir:

- pruebas unitarias;
- integración;
- contratos;
- validaciones de infraestructura;
- análisis estático;
- detección de secretos;
- escaneo de vulnerabilidades;
- smoke tests.

La automatización debe integrarse progresivamente al pipeline.

---

# 13. Integración continua

Los Pull Requests pueden ejecutar controles automáticos como:

- compilación;
- lint;
- pruebas;
- análisis estático;
- validación de contratos;
- detección de secretos;
- construcción de imágenes;
- escaneo de vulnerabilidades.

Un control crítico fallido debe impedir la integración hasta su resolución.

---

# 14. Gestión de defectos

Un defecto debe registrar, como mínimo:

- identificador;
- descripción;
- prueba que lo detectó;
- resultado esperado;
- resultado obtenido;
- severidad;
- evidencia;
- componente afectado;
- estado.

Los defectos pueden administrarse mediante GitHub Issues.

---

# 15. Severidad de defectos

Se recomienda utilizar:

| Severidad | Descripción |
|---|---|
| Crítica | Impide operar una función esencial o introduce un riesgo grave. |
| Alta | Afecta una funcionalidad importante sin solución aceptable. |
| Media | Existe afectación funcional pero puede existir alternativa temporal. |
| Baja | Impacto menor, visual o no crítico. |

La severidad no necesariamente determina por sí sola la prioridad.

---

# 16. Evidencia

Toda prueba ejecutada debe producir evidencia suficiente para demostrar su resultado.

La evidencia puede incluir:

- salida automatizada;
- logs;
- captura;
- métricas;
- reporte;
- resultado de pipeline;
- respuesta de API;
- archivo generado.

La evidencia debe poder asociarse con el caso de prueba correspondiente.

---

# 17. Criterios de entrada

Antes de iniciar una campaña de pruebas debe verificarse, cuando aplique:

- requisitos disponibles;
- criterios de aceptación definidos;
- build disponible;
- ambiente operativo;
- datos de prueba preparados;
- dependencias disponibles;
- casos de prueba definidos.

---

# 18. Criterios de salida

Una campaña puede considerarse finalizada cuando:

- se ejecutaron las pruebas previstas;
- las pruebas críticas están aprobadas;
- no existen defectos críticos abiertos;
- los defectos restantes están documentados;
- existe evidencia;
- los resultados están registrados;
- se conocen los riesgos residuales.

---

# 19. Definition of Done y calidad

Una funcionalidad no debería considerarse terminada únicamente porque el código fue implementado.

Cuando corresponda, debe cumplir:

- implementación terminada;
- revisión realizada;
- pruebas ejecutadas;
- criterios de aceptación satisfechos;
- documentación actualizada;
- defectos críticos resueltos;
- evidencia disponible.

---

# 20. Diseño de pruebas

Los casos, escenarios, datos, precondiciones y resultados esperados se documentan en:

[Diseño de Pruebas](./test-design.md)

---

# 21. Plan de pruebas

La planificación general de las actividades de prueba se mantiene en:

[Plan de Pruebas](./test-plan.md)

---

# 22. Reporte de pruebas

Los resultados de las ejecuciones se consolidan en:

[Reporte de Pruebas](./test-report.md)

---

# 23. Relación con atributos de calidad

Los escenarios de calidad definidos por arquitectura deben convertirse en pruebas verificables.

La relación es:

    Atributo de calidad
          ↓
    Escenario de calidad
          ↓
      Diseño de prueba
          ↓
       Ejecución
          ↓
        Evidencia

[Consultar Quality Attributes](../architecture/quality-attributes/README.md)

---

# 24. Relación con infraestructura

Los controles de infraestructura deben transformarse en verificaciones reproducibles.

Ejemplos:

- aislamiento de bases;
- puertos publicados;
- health checks;
- backup;
- restore;
- despliegue;
- monitoreo.

[Consultar Infrastructure](../infrastructure/README.md)

---

# 25. Responsabilidades

La calidad es responsabilidad compartida.

## QA

Responsable principalmente de:

- metodología;
- diseño de pruebas;
- campañas de prueba;
- evidencia;
- reporte.

## Desarrollo

Responsable de:

- pruebas unitarias;
- corrección de defectos;
- soporte a integración;
- mantenibilidad del código.

## Arquitectura

Responsable de proporcionar:

- escenarios de calidad;
- restricciones;
- riesgos;
- decisiones que deben verificarse.

## DevOps

Responsable de apoyar:

- ambientes;
- pipelines;
- observabilidad;
- validaciones de infraestructura;
- despliegues.

---

# 26. Regla de mantenimiento

Este documento debe actualizarse cuando:

- cambie la estrategia de calidad;
- se incorpore un nuevo tipo de prueba;
- cambien los ambientes;
- cambien los criterios de entrada o salida;
- cambie el flujo de defectos;
- cambie la Definition of Done;
- una decisión arquitectónica introduzca nuevos controles verificables.

La metodología debe representar el proceso real utilizado por el equipo y no únicamente una descripción teórica.