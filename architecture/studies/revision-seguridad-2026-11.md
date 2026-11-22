# Revisión de seguridad de noviembre de 2026

| Campo | Información |
|---|---|
| Proyecto | RedVital |
| Estado | Para revisión de la Mesa de Arquitectura |
| Fecha | 2026-11-16 |
| Alcance | Rama `develop` de `identity`, `institutional`, `campaign`, `donation`, `notifications`, `frontend`, `tools`, `databases` y `Despliegue_RedVital` |
| Método | Revisión estática del código, la configuración y los flujos de integración continua. No incluye pruebas dinámicas ni de penetración. |
| Requisitos de referencia | `requirements/security-requirements.md` (SR-01 a SR-39), ADR-008, ADR-011, EC-03 y EC-06 |

## 1. Motivo

Durante las pruebas se observó que, al iniciar sesión o crear una cuenta, el correo y la contraseña se ven en el cuerpo de la petición en las herramientas de desarrollo del navegador. Esta revisión responde si eso es una exposición y busca las que sí lo sean.

## 2. Sobre el cuerpo de la petición visible en el navegador

No es una vulnerabilidad. La pestaña de red muestra lo que el propio navegador envía, antes de cifrarlo: lo ve la persona que escribió la contraseña en su equipo, no un tercero. Cifrar o resumir la contraseña en el navegador antes de enviarla no agrega protección, porque el valor resultante pasa a ser la credencial y quien lo capture puede reutilizarlo igual. La protección de una credencial se da en otros puntos, y esos están cubiertos:

| Punto | Estado en `develop` |
|---|---|
| Tránsito entre navegador y borde | TLS en Caddy, redirección de HTTP a HTTPS y HSTS (SR-27, SR-28). |
| Dónde viaja la credencial | Solo en el cuerpo de `POST /api/v1/sesiones` y `POST /api/v1/cuentas-donante`. Nunca en la URL. El documento del donante también va en el cuerpo, incluso en la búsqueda del operador. |
| Almacenamiento en el servidor | Resumen con sal de `PasswordHasher` (PBKDF2). La credencial en claro no se guarda ni se registra. |
| Respuestas | Ninguna respuesta devuelve credenciales ni resúmenes. El 401 no dice cuál dato falló (SR-07). |
| Almacenamiento en el navegador | El token de acceso vive solo en memoria. La sesión de renovación va en una cookie `HttpOnly`, `Secure`, `SameSite=Strict` limitada a `/api/v1/sesiones`. No se usa `localStorage` ni `sessionStorage`. |
| Registros | Los registros de Caddy ocultan las cabeceras de autorización y las cookies. Los servicios no registran cuerpos de petición. |
| Contenido activo | CSP `default-src 'self'` sin scripts en línea, `frame-ancestors 'none'`, `nosniff` y `Referrer-Policy: no-referrer`. |

## 3. Hallazgos

Severidad: alta, media o baja según el impacto y la facilidad de aprovecharlo. Ninguno es crítico.

| Id | Severidad | Componente | Hallazgo | Tratamiento |
|---|---|---|---|---|
| S-01 | Media | Donación, Identidad, Notificaciones | El nombre del donante era texto libre de hasta 120 caracteres y aparece en el saludo del correo de confirmación, que se envía a un correo de contacto sin verificar. Permitía usar el alta para hacer llegar un enlace o un mensaje ajeno dentro de un correo de RedVital. | Corregido: el nombre se limita a letras, espacios, apóstrofo, guion y punto de abreviatura en Identidad, Donación y la web. Queda abierta la verificación del correo (P-02). |
| S-02 | Media | Integración continua | Los flujos de `identity`, `campaign`, `institutional` y `databases` no corrían en los Pull Requests hacia `develop`: tres apuntaban solo a `main` y el de Identidad a una rama `development` que no existe. Lo integrado en `develop` no pasaba por compilación ni pruebas. | Corregido en los cuatro repositorios. |
| S-03 | Media | `databases` | El flujo de validación leía los secretos de la base institucional sin generarlos y no se disparaba con sus migraciones: la base institucional quedaba sin verificar. | Corregido. |
| S-04 | Media | Identidad | El inicio de sesión respondía más rápido cuando la cuenta no existía, porque no calculaba ningún resumen. La diferencia de tiempo permite saber qué correos tienen cuenta (SR-07). | Corregido: se verifica contra un resumen señuelo. |
| S-05 | Baja | Identidad | `DELETE /api/v1/sesiones/actual` revocaba la sesión indicada en la cookie sin comprobar que perteneciera a quien presentaba el token. Requiere conocer el identificador de una sesión ajena, que es un UUID aleatorio. | Corregido: solo se revoca la sesión del titular del token. |
| S-06 | Baja | Identidad | El inicio de sesión comparaba el correo tal como llegaba, mientras el alta lo guarda en minúsculas: una cuenta creada desde la web fallaba al entrar con mayúsculas. Es un defecto funcional que además genera intentos fallidos en la auditoría. | Corregido. |
| S-07 | Baja | Identidad | La auditoría del inicio de sesión guardaba como origen la dirección de APISIX, no la del cliente. Dificulta investigar intentos de adivinación. | Corregido: se toma el primer salto de `X-Forwarded-For`, como en Donación. |
| S-08 | Baja | Identidad | El secreto de la cookie de renovación se formaba con dos UUID. Se cambia por 32 bytes del generador criptográfico del sistema. Es un endurecimiento, no una falla aprovechable. | Corregido. |
| S-09 | Baja | `frontend` | `node_modules` y `dist` estaban versionados (3.417 archivos) aunque figuran en `.gitignore`. Agrandan la superficie de los escaneos y permiten que entre al repositorio código que nadie revisa. | Corregido: salen del índice. |
| S-10 | Baja | `frontend` | `npm audit` reportaba un aviso de severidad alta en `vite`, que solo afecta al servidor de desarrollo. | Corregido: se actualiza dentro del rango declarado. |
| S-11 | Media | Integración continua | SR-34 y SR-35 piden detección de secretos y escaneo de vulnerabilidades. Solo `campaign`, `institutional` y `frontend` tienen el flujo `security.yml`; en `frontend` el escaneo no bloquea (`fail-build: false`). `donation` y `notifications` no tienen ningún flujo. | Abierto (P-04). |
| S-12 | Media | Despliegue | Entre la VM de borde y la VM de servicios el tráfico va por HTTP sin cifrar: APISIX reenvía a los puertos 8080 a 8084. El cuerpo del inicio de sesión cruza esa red en claro. El firewall limita quién llega a esos puertos, pero no protege contra quien pueda observar la red privada. | Abierto (P-01). |
| S-13 | Baja | Identidad | El alta de cuentas responde 409 cuando el correo ya tiene cuenta, lo que permite saber si un correo está registrado. El límite de 10 altas por minuto y origen lo frena, no lo impide. | Abierto (P-02). |
| S-14 | Baja | Identidad, gateway | La defensa contra la adivinación de credenciales es solo el límite por origen (10 por minuto) y se cuenta por nodo de APISIX. No hay límite por cuenta. | Abierto (P-03). |
| S-15 | Baja | Kafka | El listener que usa la VM de Tools es `SASL_PLAINTEXT` en QA y `SASL_SSL` solo en producción. | Aceptable en QA; se deja constancia. |

## 4. Propuestas que requieren decisión

Cambian contratos, infraestructura o reglas de negocio. No se aplican sin acuerdo del equipo.

### P-01 Cifrar el tramo entre el borde y los servicios

TLS entre APISIX y los cuatro servicios, con la CA del ambiente que ya firma el certificado de PostgreSQL. Implica certificados por servicio en `generar-secretos.sh`, que Kestrel y Spring escuchen con TLS, y `scheme: https` con verificación en los upstreams de APISIX. Costo: un secreto más por servicio y su rotación. Alternativa menor: una red privada cifrada entre las VM, que no toca los servicios.

### P-02 Verificar el correo antes de usarlo

Hoy el correo de contacto del perfil no se verifica y puede ser distinto del correo de la cuenta. La propuesta es enviar un enlace de confirmación de un solo uso y no emitir avisos a un correo sin confirmar. Con ese paso el alta puede responder siempre lo mismo, exista o no la cuenta, y se cierra S-13. Cambia el contrato de `POST /api/v1/cuentas-donante` y la interfaz, y agrega un tipo de aviso en EV-02.

### P-03 Límite de intentos por cuenta

Un contador de intentos fallidos por cuenta en Identidad, con una espera creciente en lugar de un bloqueo indefinido para que no sirva para dejar a alguien sin acceso. No cambia el contrato: responde el mismo 401 o un 429. Requiere una migración en `db_identidad`.

### P-04 Escaneos en todos los repositorios

Llevar `security.yml` a `identity`, `donation`, `notifications`, `databases`, `tools` y `Despliegue_RedVital`, agregar un flujo de compilación y pruebas a `donation` y `notifications`, y hacer que el escaneo de `frontend` bloquee ante hallazgos críticos. Conviene confirmar antes que el secreto `GITLEAKS_LICENSE` está definido para toda la organización.

### P-05 Proteger las ramas

Reglas de protección en `develop` y `main` de todos los repositorios: Pull Request obligatorio, al menos una aprobación y los flujos de integración continua como comprobaciones requeridas. Sin ellas, lo corregido en S-02 depende de que cada quien espere el resultado.

## 5. Lo que se revisó sin hallazgos

- Secretos: no hay credenciales, llaves ni tokens en los repositorios. Llegan como archivos en `/run/secrets` y nunca como variables de entorno.
- Tokens: RS256, con emisor, audiencia, vigencia de quince minutos y algoritmo fijo. El gateway valida y cada servicio revalida con la llave pública (EC-03). La llave de prueba no se registra fuera de `Development`.
- Sesión de renovación: rotación en cada uso y revocación de la sesión ante la reutilización de un secreto.
- Autorización: compuerta por rol en APISIX y de nuevo en cada operación de los servicios; la jurisdicción se filtra en el servicio.
- Consultas a la base: parametrizadas en todos los servicios. No se encontró SQL armado con datos de entrada.
- Errores: las respuestas no incluyen trazas ni los valores recibidos.
- Correos: el contenido que viene de datos se escapa en la plantilla HTML y los dominios reservados no reciben envíos.
- Rutas internas: `/internal` responde 404 en Caddy y en APISIX.
- Contenedores: sin root, sin capacidades, sin escalar privilegios y con sistema de archivos de solo lectura.
- Bases: una por servicio, TLS con verificación de la CA, SCRAM y permisos por rol.
- Kafka: SCRAM por servicio, ACL sin permisos por defecto y el tema con datos de contacto legible solo por Notificaciones.
