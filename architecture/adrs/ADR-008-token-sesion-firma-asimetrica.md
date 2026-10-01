# ADR-008 — Token de acceso firmado asimétricamente y sesión de renovación rotatoria

| Campo | Contenido |
|---|---|
| **Identificador y título** | ADR-008. Token de acceso firmado asimétricamente y sesión de renovación rotatoria. |
| **Estado** | Aceptado |
| **Restricciones aplicables** | RD-08, RN-01, RI-03, RI-04 |
| **Fecha original** | 22 de septiembre de 2026 |
| **Última actualización** | 30 de septiembre de 2026 |

---

## Contexto

RedVital necesita identificar de forma segura al usuario que ejecuta cada operación y transportar únicamente la información mínima necesaria para aplicar las reglas de autorización por rol y jurisdicción.

La arquitectura distribuida no debe depender de una consulta síncrona al Servicio de Identidad en cada petición. Si todos los servicios tuvieran que consultar centralmente la sesión antes de atender una solicitud, Identidad se convertiría en una dependencia de disponibilidad y en un punto común del camino crítico.

Tampoco es suficiente confiar únicamente en la validación realizada por el API Gateway. Cada servicio propietario debe poder comprobar de manera independiente que el token fue emitido por RedVital, que conserva su integridad, que no está vencido y que corresponde a la audiencia esperada.

El Servicio de Identidad es el propietario de las cuentas, roles, jurisdicciones, sesiones y credenciales de servicio. Es además el único componente autorizado para emitir tokens de acceso.

La solución debe satisfacer simultáneamente las siguientes necesidades:

- validación distribuida sin consulta central en cada petición;
- separación entre emisión y verificación de tokens;
- protección de la clave privada;
- rotación de claves sin invalidar anticipadamente tokens todavía vigentes;
- sesión revocable de mayor duración que el token de acceso;
- detección de reutilización del secreto de renovación;
- ausencia de datos personales y clínicos innecesarios dentro del token;
- validación tanto en el gateway como en cada servicio;
- soporte para procesos internos sin usuario mediante tokens de servicio.

RF-23 establece que, cuando un usuario registrado presenta credenciales válidas, se inicia una sesión con vigencia máxima de ocho horas y se emite un token de acceso con su rol y jurisdicción.

RF-24 establece que el cierre o vencimiento de la sesión exige nuevamente credenciales para cualquier operación que requiera autenticación.

---

## Opciones consideradas

### Opción A — Sesión central almacenada en servidor

Cada petición transporta únicamente un identificador de sesión y los servicios consultan al Servicio de Identidad para resolver el usuario, el rol y la jurisdicción.

Esta alternativa permite revocación central inmediata, pero convierte a Identidad en una dependencia síncrona de prácticamente todas las operaciones del sistema.

También agrega latencia a cada petición y genera un único punto de presión operativo.

**Descartada.**

---

### Opción B — Token firmado con clave simétrica compartida

Los servicios comparten una misma clave para validar tokens.

La implementación es sencilla, pero cualquier componente con acceso a la clave puede no solo validar sino también emitir tokens válidos.

La compromisión de un servicio implicaría entonces comprometer la autoridad de emisión del sistema.

**Descartada.**

---

### Opción C — Token de acceso firmado asimétricamente y validación distribuida

El Servicio de Identidad conserva exclusivamente la clave privada y emite los tokens de acceso.

El API Gateway y los servicios reciben únicamente las claves públicas necesarias para validar los tokens.

Las claves públicas se publican mediante JWKS y se identifican mediante `kid`.

La validación se realiza localmente en cada componente sin consultar al Servicio de Identidad en cada petición.

**Seleccionada.**

---

### Opción D — Plataforma externa completa de identidad

Una plataforma especializada podría administrar autenticación, sesiones, recuperación de contraseña, federación y emisión de tokens.

Aunque estas capacidades pueden ser apropiadas para un entorno productivo de mayor alcance, introducen una plataforma adicional que el equipo tendría que desplegar, configurar, aprender, operar y sustentar.

Para el alcance académico actual, ese costo operativo no se justifica.

**Descartada.**

---

## Decisión

### 1. Propiedad de identidad

El **Servicio de Identidad** es el único componente autorizado para:

- validar las credenciales de usuario;
- iniciar sesiones;
- renovar sesiones;
- revocar sesiones;
- emitir tokens de acceso;
- emitir tokens de servicio;
- administrar las credenciales de servicio;
- publicar las claves públicas utilizadas para validar los tokens.

El Servicio Institucional no emite tokens ni administra sesiones.

---

### 2. Token de acceso

RedVital utiliza un token de acceso firmado mediante criptografía asimétrica.

El token:

- se firma con `RS256`;
- utiliza claves RSA de 3072 bits;
- identifica la clave utilizada mediante `kid`;
- tiene una vigencia de quince minutos;
- es emitido exclusivamente por el Servicio de Identidad.

La clave privada permanece únicamente en el Servicio de Identidad y no se distribuye a los demás componentes.

---

### 3. Reivindicaciones

El token de acceso contiene exactamente las siguientes ocho reivindicaciones:

- `sub`: identificador de la cuenta o nombre del componente en un token de servicio;
- `role`: código del rol del usuario o `servicio`;
- `jurisdiction`: lista de ámbitos autorizados;
- `iss`: emisor del token;
- `aud`: audiencia esperada;
- `iat`: momento de emisión;
- `exp`: momento de expiración;
- `jti`: identificador único del token.

Para los tokens emitidos por RedVital:

- `iss` es `redvital-identidad`;
- `aud` es `redvital-api`.

La tolerancia máxima de reloj para validar `iat` y `exp` es de treinta segundos.

El token no contiene:

- nombre;
- correo;
- documento;
- teléfono;
- grupo sanguíneo;
- información clínica;
- ninguna otra información personal que no sea necesaria para autenticación o autorización.

---

### 4. Validación distribuida

El API Gateway realiza la primera validación del token.

Debe comprobar como mínimo:

- firma;
- `kid`;
- emisor;
- audiencia;
- vigencia.

Después aplica la compuerta gruesa por ruta y rol.

Cada servicio vuelve a validar el token antes de autorizar el acceso a sus recursos.

El servicio propietario conserva la decisión final sobre:

- jurisdicción;
- alcance del recurso;
- reglas de negocio;
- autorización contextual.

La red interna no se considera una zona de confianza implícita.

---

### 5. Publicación de claves mediante JWKS

El Servicio de Identidad publica las claves públicas mediante:

`GET /.well-known/jwks.json`

La respuesta cumple RFC 7517 y contiene únicamente material público.

Esta operación:

- no pasa por el API Gateway;
- solo es alcanzable dentro de la red de aplicación;
- permite que los consumidores validen tokens sin acceder a la clave privada.

Los consumidores mantienen las claves en caché durante diez minutos.

Ante un `kid` desconocido pueden consultar nuevamente las claves, como máximo una vez cada treinta segundos.

Si un consumidor no dispone de una clave válida para verificar un token, rechaza las solicitudes que requieran sesión.

El comportamiento es de **fallo cerrado**.

---

### 6. Rotación de claves

Las claves de firma pueden rotarse sin invalidar inmediatamente los tokens de acceso todavía vigentes.

Durante una rotación:

- el Servicio de Identidad comienza a firmar con una nueva clave;
- la nueva clave pública se publica mediante JWKS;
- las claves públicas anteriores se mantienen disponibles mientras puedan existir tokens vigentes firmados con ellas.

Una rotación ordinaria de claves no revoca las sesiones activas.

La clave privada:

- no se almacena en el repositorio;
- no se incluye dentro de la imagen del contenedor;
- se suministra al servicio mediante el mecanismo de secretos definido para cada ambiente.

---

### 7. Sesión de renovación

El token de acceso de quince minutos no representa por sí solo la duración completa de la sesión.

RedVital mantiene una **sesión de renovación con estado**, propiedad del Servicio de Identidad.

La sesión:

- tiene una vigencia absoluta máxima de ocho horas;
- puede ser revocada;
- no prolonga su vigencia absoluta al renovarse;
- permite obtener nuevos tokens de acceso sin pedir nuevamente las credenciales durante su vigencia.

Cada sesión almacena únicamente el resumen del secreto de renovación vigente.

El token de renovación tiene conceptualmente la forma:

`<sesion>.<secreto>`

El secreto en claro nunca se almacena en la base de datos.

---

### 8. Cookie de renovación

El token de renovación se transporta mediante una cookie de sesión segura.

La cookie se denomina:

`redvital_renovacion`

y debe configurarse con:

- `HttpOnly`;
- `Secure`;
- `SameSite=Strict`;
- `Path=/api/v1/sesiones`.

El secreto de renovación no se devuelve dentro del cuerpo JSON de las respuestas.

La aplicación web no necesita leer directamente su contenido.

La cookie es utilizada por:

- `POST /v1/sesiones/renovacion`;
- las operaciones necesarias para identificar y revocar la sesión actual.

---

### 9. Rotación del secreto de renovación

Cada renovación válida sustituye el secreto de renovación vigente por uno nuevo.

El secreto anterior deja de ser válido inmediatamente después de una renovación exitosa.

El Servicio de Identidad almacena únicamente el resumen del secreto vigente.

Esta estrategia limita el efecto del robo o repetición de un token de renovación.

---

### 10. Detección de reutilización

Si se presenta un secreto asociado a una sesión existente, vigente y no revocada, pero su resumen no coincide con el secreto vigente, se considera que se está reutilizando un secreto previamente rotado.

En ese caso:

1. la sesión se revoca;
2. el motivo de revocación se registra como `reutilizacion`;
3. el hecho queda registrado en la auditoría;
4. no se emite un nuevo token de acceso;
5. el cliente deberá autenticarse nuevamente.

No se conserva un historial de secretos anteriores.

---

### 11. Inicio de sesión

La operación:

`POST /v1/sesiones`

recibe las credenciales del usuario.

El correo es el identificador de acceso de la cuenta.

Cuando las credenciales son válidas:

1. se crea la sesión;
2. se emite el token de acceso;
3. se genera el secreto de renovación;
4. se almacena únicamente su resumen;
5. se establece la cookie `redvital_renovacion`.

La respuesta ante credenciales inválidas no diferencia entre:

- correo inexistente;
- credencial incorrecta;
- secreto inválido.

Estos casos se presentan externamente de manera indistinguible para evitar filtración de información sobre cuentas existentes.

---

### 12. Renovación

La operación:

`POST /v1/sesiones/renovacion`

utiliza la cookie de renovación.

Una renovación válida:

- verifica la sesión;
- verifica el secreto;
- comprueba que la sesión no esté revocada;
- comprueba que la sesión no haya vencido;
- rota el secreto;
- emite un nuevo token de acceso;
- actualiza la cookie de renovación.

La renovación no extiende la vigencia absoluta de ocho horas de la sesión.

---

### 13. Término de sesión

La operación:

`DELETE /v1/sesiones/actual`

revoca la sesión de renovación actual.

El token de acceso que ya fue emitido no mantiene estado central y por tanto continúa siendo válido hasta alcanzar su `exp`, con un máximo de quince minutos desde su emisión.

Después del cierre de sesión no pueden emitirse nuevos tokens de acceso utilizando esa sesión.

---

### 14. Revocación administrativa

Las sesiones de renovación se revocan cuando corresponda, entre otros casos, por:

- cierre explícito;
- desactivación del usuario;
- cambio de rol;
- cambio de jurisdicción;
- reutilización de secreto;
- rotación de emergencia.

El cambio de rol o jurisdicción se refleja en el siguiente token emitido y revoca las sesiones de renovación existentes cuando así lo exige el diseño vigente.

---

### 15. Tokens de servicio

Los procesos que no actúan en nombre de un usuario se autentican mediante credenciales de servicio.

El gateway, el Servicio de Donación y el trabajador de Notificaciones pueden canjear su credencial por un token de servicio.

El token de servicio:

- tiene una vigencia de quince minutos;
- no dispone de renovación;
- utiliza `role=servicio`;
- utiliza como `sub` el nombre del componente;
- emplea `jurisdiction=territorio:/00`.

Cuando una llamada entre servicios ocurre dentro de una petición iniciada por un usuario, se propaga el token del usuario.

El token de servicio se utiliza únicamente para procesos sin usuario.

---

### 16. Autorización por tipo de sujeto

Una operación que solo admite usuarios rechaza un token de servicio.

Una operación que solo admite servicios rechaza un token de usuario.

La respuesta correspondiente es `403 operacion-no-permitida`, salvo las operaciones internas, cuya protección adicional puede ocultar la existencia de la ruta según las reglas establecidas para la red interna.

---

### 17. Errores de autenticación

Los errores de autenticación siguen el catálogo común del DD.

El error:

`401 sesion-invalida`

se utiliza para casos como:

- token ausente;
- token vencido;
- firma inválida;
- emisor incorrecto;
- audiencia incorrecta;
- secreto de renovación inválido;
- sesión inválida.

Externamente estos casos no revelan detalles que permitan inferir información sobre la cuenta o sobre la razón específica del fallo.

---

## Justificación

La firma asimétrica separa dos responsabilidades distintas:

- emitir credenciales;
- verificar credenciales.

Solo el Servicio de Identidad necesita la clave privada.

Los demás componentes únicamente requieren las claves públicas.

Esto evita que un servicio capaz de validar tokens pueda también fabricar tokens válidos.

La validación local disminuye el acoplamiento de disponibilidad, porque una vez emitido el token no es necesario consultar al Servicio de Identidad en cada petición.

El token de acceso corto limita el tiempo durante el cual una credencial ya emitida puede seguir siendo utilizada después de un cambio de estado.

La sesión de renovación de ocho horas permite mantener una experiencia de sesión razonable sin convertir el token de acceso en una credencial de larga duración.

La rotación del secreto de renovación y la detección de reutilización reducen el riesgo asociado al robo de una credencial de renovación.

La publicación mediante JWKS permite rotar claves sin distribuir secretos ni invalidar innecesariamente sesiones activas.

---

## Compromiso evaluado

### Se gana

- validación distribuida;
- menor dependencia síncrona del Servicio de Identidad;
- separación entre emisión y verificación;
- protección de la clave privada;
- soporte para rotación de claves;
- tokens de acceso de corta duración;
- sesiones revocables;
- detección de reutilización del secreto;
- reducción de datos personales dentro del token.

### Se sacrifica

Un token de acceso ya emitido puede continuar siendo válido hasta su vencimiento aunque la sesión haya sido revocada después de su emisión.

Ese intervalo está limitado a un máximo de quince minutos.

También se asume complejidad adicional en:

- gestión de claves;
- publicación JWKS;
- sesiones de renovación;
- rotación de secretos;
- auditoría;
- pruebas de seguridad.

---

## Consecuencias

### Seguridad

La clave privada queda restringida al Servicio de Identidad.

La compromisión de un servicio que solo disponga de las claves públicas no permite emitir tokens válidos.

### Disponibilidad

Los servicios pueden validar tokens sin consultar al Servicio de Identidad en cada petición.

### Privacidad

El token no contiene datos personales o clínicos distintos de los estrictamente necesarios para autenticación y autorización.

### Revocación

La sesión de renovación puede revocarse inmediatamente.

El token de acceso continúa siendo válido hasta su vencimiento natural.

### Rotación

Las claves anteriores se mantienen temporalmente disponibles en JWKS para validar tokens aún vigentes.

### Gateway

El gateway valida autenticidad y vigencia y realiza la compuerta gruesa de autorización.

### Servicios

Cada servicio vuelve a validar el token y aplica las reglas de acceso correspondientes al recurso.

### Datos

La tabla `sesion` del Servicio de Identidad conserva el estado necesario para renovación, revocación y detección de reutilización.

### Contratos

Los contratos OpenAPI de Identidad deben reflejar esta decisión en:

- `POST /v1/sesiones`;
- `POST /v1/sesiones/renovacion`;
- `DELETE /v1/sesiones/actual`;
- `GET /.well-known/jwks.json`.

---

## Verificación

Deben probarse como mínimo los siguientes casos:

1. inicio de sesión con credenciales válidas;
2. credenciales inválidas sin revelar cuál dato falló;
3. token con firma inválida;
4. token con emisor incorrecto;
5. token con audiencia incorrecta;
6. token vencido;
7. token con rol no autorizado;
8. acceso fuera de jurisdicción;
9. renovación con secreto válido;
10. rotación correcta del secreto;
11. reutilización de un secreto anterior;
12. sesión vencida después de ocho horas;
13. sesión revocada;
14. cierre explícito de sesión;
15. `kid` desconocido;
16. indisponibilidad de claves públicas;
17. rotación de clave de firma sin invalidar tokens todavía vigentes;
18. intento de usar un token de servicio en una operación exclusiva de usuario;
19. intento de usar un token de usuario en una operación exclusiva de servicio.

---

## Trazabilidad

Esta decisión soporta principalmente:

- RF-22 — Asignación de jurisdicción de usuario;
- RF-23 — Inicio de sesión;
- RF-24 — Término de sesión;
- EC-01 — Control de acceso por jurisdicción;
- EC-03 — Autenticidad de la sesión;
- ADR-007 — API Gateway;
- ADR-011 — Matriz de autorización;
- ADR-015 — Seguridad del borde;
- DD V2.0 — Modelo lógico del Servicio de Identidad y contratos;
- SAD V2.0 — Seguridad de infraestructura y validación distribuida.

---

## Vigencia de lo elegido

La decisión permanece vigente mientras RedVital mantenga:

- un Servicio de Identidad como autoridad única de emisión;
- múltiples componentes que deban validar credenciales;
- autorización distribuida por rol y jurisdicción;
- necesidad de evitar una consulta central de sesión en cada petición.

La biblioteca concreta utilizada para JWT/JWKS puede cambiar sin modificar esta decisión, siempre que conserve:

- firma asimétrica;
- compatibilidad con RS256;
- publicación JWKS;
- separación entre clave privada y claves públicas;
- semántica de las ocho reivindicaciones;
- sesión de renovación rotatoria.

---

## Responsable y fecha de revisión

| Rol | Responsabilidad |
|---|---|
| Arquitecta de Software | Mantener coherencia entre ADR, SAD, DD, SRS y contratos OpenAPI |
| Desarrollador de Identidad | Implementar emisión, sesiones, renovación, revocación y JWKS |
| Desarrolladores de servicios | Implementar validación local del token |
| QA / Tester | Verificar autenticación, autorización, renovación, revocación y rotación |

**Próxima revisión:** cuando cambie el mecanismo de autenticación, la duración de las credenciales, el esquema de sesión, el algoritmo de firma o la autoridad responsable de emitir tokens.
