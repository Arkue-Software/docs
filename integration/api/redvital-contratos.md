# RedVital — Contratos de API y eventos

| Campo | Información |
|---|---|
| Proyecto | RedVital |
| Estado | Vigente |
| Refleja | Rama `develop` de `tools`, `identity`, `campaign`, `donation`, `institutional`, `notifications` y `Despliegue_RedVital` |
| Fuentes | `tools/APISIX-config/config/apisix.yaml`, `contratos/asyncapi.yaml` de cada servicio, `Despliegue_RedVital/kafka/catalogo-temas.conf`, DD V5 (secciones 14 y 15) |

Este documento resume los contratos que están implementados. La especificación normativa sigue siendo el DD: si este resumen y el DD difieren, manda el DD y este archivo debe corregirse.

## Control de versiones

| Versión | Fecha | Descripción |
|---|---|---|
| 1.0 | 2026-09 | Borrador por épicas (E1 a E5), anterior a la separación en cinco servicios. |
| 2.0 | 2026-11-16 | Se reemplaza el borrador por los contratos implementados en `develop`: rutas en español bajo `/api/v1`, roles de la matriz de autorización y eventos EV-01, EV-02, EV-03, EV-07, EV-09 y EV-10. |

## 1. Reglas comunes

- El navegador solo habla con el borde. Toda ruta pública empieza por `/api/v1`; APISIX quita `/api` y reenvía `/v1/...` al servicio dueño de la ruta.
- `/api/internal/*` responde 404 en Caddy y en APISIX. Las rutas `/internal/v1/*` de Identidad solo las usa el gateway dentro de la red de servicios.
- Los cuerpos van en JSON con nombres en `snake_case`. Donación, Campañas e Institucional rechazan con 400 las propiedades que no están en el contrato.
- Los errores siguen RFC 9457 con los campos `tipo`, `titulo`, `estado`, `detalle` y `correlacion_id`.
- Toda petición lleva `X-Correlacion-Id`; si falta, el gateway lo genera. Vuelve en la respuesta y queda en la auditoría.
- Las escrituras que una pantalla puede reenviar llevan `Idempotency-Key` (1 a 64 caracteres visibles).
- El token de acceso es un JWT RS256 de quince minutos con `sub`, `role`, `jurisdiction`, `iss`, `aud`, `iat`, `exp` y `jti` (ADR-008). El gateway valida firma, emisor, audiencia y vigencia; cada servicio lo valida de nuevo con la llave pública de Identidad y aplica la jurisdicción.
- La sesión de renovación dura ocho horas y viaja en la cookie `redvital_renovacion` (`HttpOnly`, `Secure`, `SameSite=Strict`, ruta `/api/v1/sesiones`). El secreto rota en cada renovación.

### Roles

| Código en el token | Perfil |
|---|---|
| `donante` | U2, donante registrado |
| `operador` | U3, operador de banco |
| `admin_banco` | U4, administrador de banco |
| `coordinador` | U5, coordinador territorial |
| `admin_nacional` | U6, administrador nacional |
| `auditor` | U7, auditor |

Las rutas públicas tienen límite de tasa por origen: 60 peticiones por minuto, y 10 por minuto en el inicio de sesión y en el alta de cuentas.

## 2. Rutas

La columna «Roles en el gateway» es la compuerta por ruta de APISIX. El servicio puede ser más estricto y siempre filtra por jurisdicción.

### 2.1 Identidad

| Método y ruta | Roles en el gateway | Descripción |
|---|---|---|
| `POST /api/v1/sesiones` | Pública (10/min) | Inicia sesión con `correo` y `credencial`. Devuelve `token_acceso`, `tipo` y `expira_en`, y fija la cookie de renovación. El 401 no dice cuál de los dos datos falló. |
| `POST /api/v1/sesiones/renovacion` | Pública, con la cookie | Emite un token nuevo y rota el secreto de la cookie. Un secreto ya usado revoca la sesión. |
| `DELETE /api/v1/sesiones/actual` | Todo perfil con cuenta | Cierra la sesión de quien presenta el token. Responde 204. |
| `POST /api/v1/cuentas-donante` | Pública (10/min) | Alta de la cuenta de un donante con `correo`, `credencial` y `nombre`. Idempotente. 201 cuenta nueva, 200 reenvío, 409 si no es posible crearla. |
| `GET /api/v1/usuarios/me` | Todo perfil con cuenta | Datos de la propia cuenta: `id`, `correo`, `nombre`, `rol` y `ambitos`. |
| `GET /.well-known/jwks.json` | Solo red de servicios | Llaves públicas de firma. |

### 2.2 Campañas

| Método y ruta | Roles en el gateway | Descripción |
|---|---|---|
| `GET /api/v1/campanias` | Pública, token opcional | Lista campañas. Filtros `estado`, `publicada_desde` y `territorio`. Un token inválido responde 401, no se trata como anónimo. |
| `GET /api/v1/campanias/{id}` | `donante`, `operador`, `admin_banco`, `coordinador`, `admin_nacional` | Detalle de una campaña dentro del alcance de quien consulta. |
| `POST /api/v1/campanias` | `admin_banco` | Crea una campaña en borrador para la institución del token. |
| `PUT /api/v1/campanias/{id}` | `admin_banco` | Edita una campaña en borrador. |
| `POST /api/v1/campanias/{id}/publicacion` | `admin_banco` | Publica la campaña y emite EV-03. |
| `POST /api/v1/campanias/{id}/cierre` | `admin_banco` | Cierra la campaña. |

### 2.3 Institucional

| Método y ruta | Roles en el gateway | Descripción |
|---|---|---|
| `GET /api/v1/territorios` | `admin_banco`, `coordinador`, `auditor`, `admin_nacional` | Jerarquía territorial (DIVIPOLA). Filtros `nivel` y `padre_id`, paginada. |
| `POST /api/v1/territorios` | `admin_nacional` | Alta de un territorio. |
| `GET /api/v1/instituciones` | `admin_banco`, `auditor`, `coordinador`, `admin_nacional` | Lista de instituciones del alcance. |
| `POST /api/v1/instituciones` | `coordinador`, `admin_nacional` | Alta de una institución. Emite EV-07. |
| `GET /api/v1/instituciones/{id}` | Todo perfil con cuenta | Resuelve una institución por su identificador. |
| `PUT /api/v1/instituciones/{id}` | `coordinador`, `admin_nacional` | Cambia una institución. Emite EV-07. |
| `GET /api/v1/indicadores` | `admin_banco`, `coordinador`, `admin_nacional` | Indicadores de M7, compuestos con EV-09. Solo lectura. |
| `GET /api/v1/auditoria` | `auditor` | Bitácora compuesta con EV-10. Solo lectura, paginada. |

### 2.4 Donación

| Método y ruta | Roles en el gateway | Descripción |
|---|---|---|
| `POST /api/v1/intenciones` | Pública | Registro anónimo: `grupo_sanguineo` y `municipio_ruta`, ambos opcionales. Devuelve `codigo` y `expira_en`. |
| `GET /api/v1/intenciones/{codigo}` | Pública | Estado de la intención. Con token de operador incluye el grupo autodeclarado y el municipio. |
| `POST /api/v1/donantes` | `donante` | Crea el perfil del donante enlazado a la cuenta del token. El documento se guarda como resumen HMAC y nunca se devuelve. Emite EV-02 `cuenta_creada`. |
| `GET /api/v1/donantes/me` | `donante` | Perfil propio. |
| `PUT /api/v1/donantes/me` | `donante` | Actualiza nombre, contacto y municipio. |
| `GET /api/v1/donantes/me/elegibilidad` | `donante` | Si puede donar y desde cuándo. |
| `GET /api/v1/donantes/me/donaciones` | `donante` | Historial propio. |
| `GET /api/v1/donantes/me/reconocimientos` | `donante` | Reconocimientos obtenidos y por obtener. |
| `GET /api/v1/donantes/me/consentimientos` | `donante` | Consentimientos vigentes. |
| `POST /api/v1/donantes/me/consentimientos` | `donante` | Otorga o retira un consentimiento. |
| `POST /api/v1/donantes/busqueda` | `operador` | Identifica al donante presente por su documento, que va en el cuerpo. Devuelve `id`, `nombre` y `elegibilidad`. |
| `POST /api/v1/donaciones` | `operador` | Registra una donación. Emite EV-01. |
| `GET /api/v1/donaciones/{id}` | `operador`, `admin_banco`, `coordinador`, `admin_nacional` | Donación con sus unidades. |
| `POST /api/v1/donaciones/{id}/fraccionamiento` | `operador` | Fracciona la donación en unidades. |
| `GET /api/v1/unidades` | `operador`, `admin_banco`, `coordinador`, `admin_nacional` | Unidades del alcance, paginadas (`estado`, `componente`, `pagina`, `tamano` hasta 100). |
| `GET /api/v1/unidades/{id}` | Los anteriores y `auditor` | Detalle de una unidad. Ninguna respuesta lleva la causa de una unidad no apta. |
| `GET /api/v1/unidades/{id}/eventos` | Los anteriores y `auditor` | Historial de estados de la unidad. |
| `POST /api/v1/unidades/{id}/ingreso-tamizaje` | `operador` | Envía la unidad a tamizaje. |
| `POST /api/v1/unidades/{id}/tamizaje` | `operador` | Registra el resultado: apta o no apta. |
| `POST /api/v1/unidades/{id}/reserva` | `operador` | Reserva la unidad. |
| `DELETE /api/v1/unidades/{id}/reserva` | `operador` | Libera la reserva. |
| `POST /api/v1/unidades/{id}/despacho` | `operador` | Despacha la unidad. Exige confirmación e `Idempotency-Key`. |
| `POST /api/v1/unidades/{id}/disposicion-final` | `operador` | Desecha la unidad. Exige confirmación e `Idempotency-Key`. |
| `GET /api/v1/inventario` | `operador`, `admin_banco`, `coordinador`, `admin_nacional` | Existencias disponibles por institución, componente y grupo. |
| `GET /api/v1/inventario/umbrales` | `operador`, `admin_banco` | Umbrales vigentes. |
| `PUT /api/v1/inventario/umbrales` | `admin_banco` | Define los umbrales de la institución. |
| `GET /api/v1/alertas` | `operador`, `admin_banco`, `coordinador` | Alertas de inventario. Filtro `estado`. |
| `POST /api/v1/alertas/{id}/atencion` | `admin_banco` | Marca una alerta como atendida. |

El trabajador de Notificaciones no publica rutas.

## 3. Eventos

Los servicios de negocio no se llaman entre sí: se integran solo por Kafka (ADR-018). Cada productor escribe el evento en su bandeja de salida dentro de la misma transacción del cambio, y un publicador lo envía al broker. Los consumidores son idempotentes por identificador de evento.

Los temas siguen la convención `redvital.<contexto>.<evento>.v<mayor>`. El broker no crea temas: los crea `kafka-init` a partir del catálogo, con usuarios SCRAM y ACL por servicio.

| Evento | Tema | Productor | Consumidores | Efecto |
|---|---|---|---|---|
| EV-01 donación completada | `redvital.donacion.donation-completed.v1` | Donación | Campañas | Suma la donación a la campaña. |
| EV-02 aviso solicitado | `redvital.donacion.notice-requested.v1` | Donación | Notificaciones | Entrega el aviso por correo. Lleva el correo y el nombre del destinatario; solo lo lee Notificaciones y se borra a los siete días. |
| EV-03 campaña publicada | `redvital.campanias.campaign-published.v1` | Campañas | Donación | Copia local de la campaña para validar donaciones y enviar avisos. |
| EV-07 institución cambiada | `redvital.institucional.institution-changed.v1` | Institucional | Campañas, Donación | Copia local de instituciones. Tema compactado, sin vencimiento. |
| EV-09 indicadores actualizados | `redvital.donacion.indicators-updated.v1` | Donación | Institucional | Alimenta los indicadores de M7. |
| EV-10 registro de auditoría | `redvital.identidad.audit-recorded.v1`, `redvital.campanias.audit-recorded.v1`, `redvital.donacion.audit-recorded.v1` | Identidad, Campañas, Donación | Institucional | Bitácora compuesta que consulta el auditor. |

EV-04 a EV-06 están reservados y EV-08 (cierre de campaña) está propuesto: no tienen tema hasta que su contrato AsyncAPI se apruebe.

### Reintentos y mensajes muertos

| Consumidor | Tratamiento |
|---|---|
| Donación | Temas `retry-0` a `retry-2` con esperas de 2, 10 y 50 segundos, y luego `.dlt`. |
| Notificaciones | Temas `retry-0` a `retry-5`, ocho intentos con esperas de 15 segundos a 5 minutos, y luego `.dlt`. Si el canal de correo no responde, reintenta el mismo aviso sin avanzar. |
| Campañas e Institucional | Reintentan en el propio consumidor unos segundos y luego envían a `.dlt`. |

## 4. Fuera de esta versión

No están implementados y no tienen contrato vigente: transferencias y convocatorias (M5), reserva de cupo en campañas (RF-15), asignación de jurisdicción desde la interfaz (RF-22) y la integración con SIHEVI-INS (RNF-07).
