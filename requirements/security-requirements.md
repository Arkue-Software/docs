# Requerimientos de Seguridad — Red Vital

## 1. Propósito

Este documento consolida los **requerimientos de seguridad vigentes de Red Vital**.

Su objetivo es reunir en una única vista especializada los requisitos relacionados con:

- confidencialidad;
- autenticación;
- autorización;
- control de acceso;
- jurisdicción;
- sesiones;
- tokens;
- auditoría;
- protección de datos;
- rutas internas;
- aislamiento;
- secretos;
- trazabilidad de acciones.

Este documento complementa:

- los requerimientos funcionales;
- los requerimientos no funcionales;
- el SAD;
- los ADRs;
- la infraestructura;
- el diseño de pruebas.

---

# 2. Principio de seguridad

Red Vital maneja información personal y sensible.

Por esta razón, la seguridad se considera un **atributo de calidad de primer orden** y debe estar presente en:

- requisitos;
- arquitectura;
- diseño;
- implementación;
- infraestructura;
- integración;
- pruebas;
- operación.

La seguridad no debe depender de un único componente.

---

# 3. Fuentes de los requerimientos

Los principales requisitos de seguridad provienen de:

- RNF-01 — Confidencialidad;
- RNF-02 — Control de acceso;
- RNF-09 — Responsabilidad;
- RF-21 — Consulta de bitácora;
- RF-22 — Asignación de jurisdicción;
- RF-23 — Inicio de sesión;
- RF-24 — Término de sesión;
- RD-08 — Seguridad como atributo de primer orden;
- ADRs de identidad, autorización y auditoría.

---

# 4. SR-01 — Protección de información clínica

**Origen:** RNF-01

El sistema deberá evitar almacenar, presentar, registrar o exponer información clínica que no pertenezca al alcance funcional de Red Vital.

En particular, cuando una unidad sea marcada como no apta, el sistema deberá registrar únicamente el veredicto de aptitud.

No deberá almacenarse ni exponerse:

- causa clínica;
- diagnóstico;
- información médica innecesaria;
- información sensible no requerida por el proceso.

La restricción aplica a:

- modelo de datos;
- API;
- interfaz;
- logs;
- métricas;
- eventos.

---

# 5. SR-02 — Control de acceso por jurisdicción

**Origen:** RNF-02

Todo acceso a datos deberá respetar la jurisdicción asignada al usuario.

Cuando un usuario solicite información fuera de su ámbito autorizado, el sistema deberá:

- rechazar la operación;
- impedir la entrega de información;
- registrar el intento;
- evitar revelar la existencia del recurso;
- evitar revelar la jurisdicción propietaria del recurso.

La tasa esperada de cumplimiento es:

**100 % de solicitudes fuera de jurisdicción denegadas y registradas.**

---

# 6. SR-03 — Autorización por rol

Cada operación protegida deberá verificar que el usuario posea el rol requerido.

El sistema deberá impedir que un usuario:

- consulte información fuera de sus permisos;
- ejecute operaciones reservadas a otro rol;
- modifique recursos para los que solo tiene lectura;
- utilice capacidades administrativas sin autorización.

La autorización debe aplicarse en:

- gateway;
- servicio propietario;
- acceso al recurso.

---

# 7. SR-04 — Defensa en profundidad

El control de acceso no deberá depender únicamente del API Gateway.

La petición debe atravesar controles independientes:

    Cliente
      ↓
    Borde
      ↓
    API Gateway
      ↓
    Servicio propietario
      ↓
    Persistencia

Cada capa debe aplicar controles propios.

---

# 8. SR-05 — Inicio de sesión

**Origen:** RF-23

Cuando un usuario registrado presente credenciales válidas, el sistema deberá:

- iniciar una sesión;
- emitir un token de acceso;
- incluir rol;
- incluir jurisdicción;
- aplicar la vigencia establecida.

La sesión tendrá una vigencia máxima de:

**8 horas.**

---

# 9. SR-06 — Vigencia del token de acceso

Los tokens de acceso deberán tener vigencia limitada.

La configuración vigente establece:

**15 minutos.**

Los tokens vencidos deberán ser rechazados.

---

# 10. SR-07 — Protección ante credenciales inválidas

Cuando se presenten credenciales incorrectas, la respuesta no deberá revelar:

- si el usuario existe;
- cuál campo fue incorrecto;
- información adicional de la cuenta.

El mensaje de error debe evitar facilitar enumeración de usuarios.

---

# 11. SR-08 — Término y revocación de sesión

**Origen:** RF-24

Cuando:

- un usuario cierre sesión; o
- la sesión alcance su vigencia máxima;

el sistema deberá revocar la sesión.

Después de la revocación deberá exigir nuevamente credenciales para cualquier operación protegida.

---

# 12. SR-09 — Asignación de jurisdicción

**Origen:** RF-22

Cuando un administrador nacional modifique la jurisdicción de un usuario territorial, el sistema deberá:

- registrar el nuevo ámbito;
- registrar quién realizó el cambio;
- registrar cuándo se realizó;
- aplicar el nuevo ámbito a partir del siguiente token emitido.

El cambio debe quedar reflejado en auditoría.

---

# 13. SR-10 — Token con rol y jurisdicción

El token de acceso deberá transportar como mínimo la información necesaria para aplicar:

- identidad;
- rol;
- jurisdicción;
- vigencia.

No deberá incluir información sensible innecesaria.

---

# 14. SR-11 — Validación distribuida del token

El API Gateway deberá realizar una validación inicial.

Los servicios deberán validar nuevamente el token antes de ejecutar operaciones protegidas.

La validación debe comprobar, cuando corresponda:

- firma;
- vigencia;
- emisor;
- audiencia;
- rol;
- jurisdicción.

---

# 15. SR-12 — Firma de tokens

Los tokens deberán utilizar mecanismos de firma que permitan verificar su autenticidad.

La clave privada utilizada para firmar deberá:

- mantenerse protegida;
- mantenerse fuera del repositorio;
- mantenerse fuera de imágenes;
- rotarse según la política definida.

Los validadores deberán utilizar únicamente la clave pública correspondiente.

---

# 16. SR-13 — Tokens de servicio

Los procesos automáticos que operen sin usuario deberán utilizar identidad propia.

Cada servicio deberá disponer de credenciales distintas cuando requiera autenticación entre componentes.

Un token de servicio no deberá permitir operaciones destinadas a usuarios finales.

---

# 17. SR-14 — Separación entre token de usuario y token de servicio

Las rutas de usuario deberán rechazar tokens de servicio.

Las operaciones internas deberán rechazar tokens de usuario cuando requieran identidad de servicio.

Esto evita reutilización indebida de credenciales entre contextos de autenticación distintos.

---

# 18. SR-15 — Protección de rutas internas

Las operaciones internas no deberán exponerse públicamente.

Las rutas identificadas como internas deberán:

- permanecer fuera del enrutamiento externo;
- validar la identidad del servicio llamador;
- permitir únicamente sujetos explícitamente autorizados.

---

# 19. SR-16 — Auditoría de acciones relevantes

**Origen:** RNF-09

Cuando un usuario:

- cambie el estado de una unidad;
- ejecute una acción administrativa;
- intente acceder fuera de jurisdicción;

el sistema deberá registrar el evento.

El registro deberá contener:

- autor;
- operación;
- jurisdicción solicitada;
- marca temporal.

---

# 20. SR-17 — Inmutabilidad de auditoría

Los registros de auditoría deberán protegerse contra modificación indebida.

Las operaciones normales de aplicación no deberán permitir:

- editar;
- sobrescribir;
- borrar;

registros que deban conservarse como evidencia.

---

# 21. SR-18 — Auditor con acceso de solo lectura

**Origen:** RF-21

El usuario Auditor deberá poder consultar la bitácora.

No deberá disponer de operaciones que permitan:

- crear registros;
- modificar registros;
- eliminar registros.

La capacidad de auditoría debe ser de solo lectura.

---

# 22. SR-19 — Minimización de datos en auditoría

Los registros de auditoría deberán contener únicamente la información necesaria para reconstruir la acción.

No deberán copiar:

- contenido completo de recursos;
- datos sensibles innecesarios;
- tokens;
- contraseñas;
- información clínica prohibida.

---

# 23. SR-20 — Protección de secretos

Los secretos no deberán almacenarse en:

- código fuente;
- archivos Markdown;
- imágenes Docker;
- commits;
- configuraciones públicas.

Los secretos deben administrarse mediante mecanismos externos.

Ejemplos:

- archivos montados;
- variables seguras;
- secret stores;
- mecanismos propios del ambiente.

---

# 24. SR-21 — Mínimo privilegio

Cada componente deberá tener únicamente los permisos necesarios.

Esto aplica a:

- usuarios;
- servicios;
- bases de datos;
- pipelines;
- infraestructura;
- repositorios.

Los servicios no deberán operar con credenciales administrativas si no es estrictamente necesario.

---

# 25. SR-22 — Aislamiento de persistencias

Cada servicio deberá acceder únicamente a su propia persistencia.

Se deberá impedir:

- acceso directo a bases ajenas;
- lectura de tablas de otro contexto;
- escritura en persistencia ajena;
- joins entre bases de servicios distintos.

El aislamiento debe reforzarse mediante:

- red;
- credenciales;
- permisos.

---

# 26. SR-23 — Roles de base de datos

Las bases deberán diferenciar al menos:

- rol administrativo;
- rol de migración/propietario;
- rol de servicio.

El rol de servicio deberá disponer únicamente de permisos requeridos para la operación normal.

---

# 27. SR-24 — Protección de logs

Los logs no deberán contener:

- contraseñas;
- tokens;
- cookies;
- secretos;
- causas clínicas;
- datos personales innecesarios.

Los logs deberán utilizar información mínima necesaria para:

- diagnóstico;
- correlación;
- auditoría operativa.

---

# 28. SR-25 — Correlation ID

Las solicitudes deberán poder rastrearse mediante un identificador de correlación.

El correlation ID deberá propagarse entre los componentes participantes en una operación distribuida.

No deberá contener información personal.

---

# 29. SR-26 — Protección de métricas

Las métricas no deberán utilizar etiquetas con:

- identificadores de usuario;
- identificadores de donante;
- identificadores de unidad;
- tokens;
- información clínica;
- texto libre sensible.

Las etiquetas deberán utilizar valores de cardinalidad controlada.

---

# 30. SR-27 — Protección de comunicaciones externas

Las comunicaciones externas deberán utilizar HTTPS.

El proxy de borde deberá gestionar la terminación TLS correspondiente.

El tráfico HTTP externo deberá redirigirse a HTTPS cuando aplique.

---

# 31. SR-28 — Headers de seguridad

El borde deberá aplicar headers de seguridad cuando corresponda.

Entre ellos:

- `Strict-Transport-Security`;
- `Content-Security-Policy`;
- `X-Content-Type-Options`;
- `Referrer-Policy`;
- `Permissions-Policy`.

---

# 32. SR-29 — Protección frente a solicitudes excesivas

El sistema deberá implementar mecanismos para limitar abuso de solicitudes.

El API Gateway deberá aplicar rate limiting.

La política debe diferenciar cuando corresponda:

- solicitudes anónimas;
- solicitudes autenticadas.

---

# 33. SR-30 — Protección frente a solicitudes de tamaño excesivo

El borde deberá limitar el tamaño máximo permitido de las solicitudes.

Las solicitudes que excedan el límite configurado deberán rechazarse antes de alcanzar los servicios internos.

---

# 34. SR-31 — Exposición mínima de puertos

Solo deberán exponerse externamente los puertos necesarios.

No deberán exponerse directamente:

- PostgreSQL;
- puertos de gestión;
- Prometheus;
- servicios internos.

La exposición debe corresponder a la matriz de alcanzabilidad vigente.

---

# 35. SR-32 — Separación QA / Producción

QA y Producción deberán utilizar:

- credenciales distintas;
- secretos distintos;
- persistencias distintas;
- configuración distinta;
- sesiones independientes.

Las comunicaciones entre ambientes deberán limitarse explícitamente.

---

# 36. SR-33 — Seguridad de contenedores

Los contenedores deberán aplicar, cuando corresponda:

- usuario no privilegiado;
- `no-new-privileges`;
- filesystem de solo lectura;
- capacidades mínimas;
- límites de recursos;
- imágenes mínimas;
- ausencia de secretos embebidos.

---

# 37. SR-34 — Detección de secretos

La integración continua deberá verificar la presencia accidental de secretos en los repositorios.

Un secreto detectado deberá bloquear la integración hasta su corrección o tratamiento formal.

---

# 38. SR-35 — Escaneo de vulnerabilidades

Las imágenes y dependencias deberán analizarse para detectar vulnerabilidades conocidas.

Los hallazgos críticos deberán tratarse antes de liberar una versión cuando exista corrección disponible.

---

# 39. SR-36 — Acceso administrativo

El acceso administrativo a infraestructura deberá:

- estar restringido;
- utilizar identidades individuales;
- evitar contraseñas compartidas;
- aplicar mínimo privilegio;
- quedar limitado a responsables autorizados.

---

# 40. SR-37 — Protección de backups

Los respaldos deberán:

- mantenerse fuera de los contenedores;
- restringirse a usuarios autorizados;
- validar integridad;
- contar con política de retención.

Si se utilizan datos reales, deberán incorporarse controles adicionales de cifrado y protección.

---

# 41. SR-38 — Integridad de backups

Cada backup deberá disponer de mecanismo para verificar su integridad.

Se debe utilizar un checksum o mecanismo equivalente antes de ejecutar una restauración.

---

# 42. SR-39 — Seguridad de dependencias externas

Las integraciones externas, como proveedores OAuth, deberán configurarse mediante:

- endpoints explícitos;
- secretos independientes por ambiente;
- callbacks controlados;
- listas blancas cuando sean requeridas.

---

# 43. Matriz de trazabilidad de seguridad

| Requisito de seguridad | Fuente |
|---|---|
| SR-01 | RNF-01 |
| SR-02 | RNF-02 |
| SR-03 | RNF-02 / matriz de autorización |
| SR-05 | RF-23 |
| SR-08 | RF-24 |
| SR-09 | RF-22 |
| SR-16 | RNF-09 |
| SR-18 | RF-21 |
| SR-22 | Arquitectura / propiedad exclusiva de datos |
| SR-32 | Infraestructura |
| SR-34 | CI / políticas de seguridad |
| SR-35 | CI / infraestructura |

La matriz debe ampliarse con los ADRs y casos de prueba correspondientes.

---

# 44. Verificación

Los requisitos de seguridad deben verificarse mediante:

- pruebas funcionales;
- pruebas negativas;
- pruebas de autorización;
- inspección;
- análisis de configuración;
- revisión de logs;
- pruebas de infraestructura;
- pruebas automatizadas;
- escaneo de vulnerabilidades.

---

# 45. Casos de prueba relacionados

Entre los casos definidos en Testing se encuentran:

- acceso fuera de jurisdicción;
- usuario sin rol;
- token expirado;
- token de servicio en ruta de usuario;
- acceso a ruta interna;
- servicio intentando acceder a base ajena;
- puertos expuestos;
- datos sensibles en logs;
- separación QA / Producción.

Consultar:

[Test Design](../testing/test-design.md)

---

# 46. Relación con requisitos funcionales

Los requisitos de seguridad complementan capacidades funcionales como:

- inicio de sesión;
- cierre de sesión;
- administración de jurisdicción;
- auditoría.

Consultar:

[Functional Requirements](./functional-requirements.md)

---

# 47. Relación con requisitos no funcionales

Los principales RNF de seguridad son:

- RNF-01 — Confidencialidad;
- RNF-02 — Control de acceso;
- RNF-09 — Responsabilidad.

Consultar:

[Non-Functional Requirements](./non-functional-requirements.md)

---

# 48. Relación con arquitectura

Las decisiones técnicas de implementación de estos requisitos se encuentran en:

- SAD;
- ADRs;
- infraestructura;
- integración.

Este documento define **qué debe cumplirse**.

La arquitectura define **cómo se materializa**.

---

# 49. Regla de mantenimiento

Este documento debe actualizarse cuando:

- cambie el modelo de autenticación;
- cambien roles;
- cambie la jurisdicción;
- cambie el mecanismo de tokens;
- cambien las políticas de sesiones;
- cambie la infraestructura;
- cambien secretos;
- aparezca una nueva integración;
- se identifique una nueva amenaza;
- una prueba de seguridad revele un requisito faltante.

Los requisitos de seguridad deben permanecer trazables hacia:

    Requisito
       ↓
    Arquitectura
       ↓
    Control
       ↓
    Caso de prueba
       ↓
    Evidencia