# Documento de Infraestructura V1.0
## RedVital — Arkhé Software S.A.S.

**Versión:** 1.0  
**Estado:** Borrador técnico para validación  
**Fecha:** 2026-09-22  
**Proyecto:** RedVital  

---

# 1. Visión general

## 1.1 Propósito

Este documento define la infraestructura técnica de RedVital y consolida las decisiones necesarias para describir cómo se despliega, comunica, protege, observa y recupera el sistema.

La configuración técnica detallada se mantiene en este documento para evitar duplicaciones o contradicciones con el SAD, el SDD y el DD.

## 1.2 Alcance

Este documento cubre:

- topología general;
- zonas de infraestructura;
- conectividad y matriz de alcanzabilidad;
- composición de contenedores;
- propiedad y aislamiento de datos;
- seguridad y accesos;
- gestión de secretos;
- endurecimiento de contenedores;
- controles de integración continua;
- observabilidad;
- respaldo y restauración;
- configuración por ambiente;
- procedimientos de verificación.

Los ambientes considerados son:

- Desarrollo;
- QA;
- Producción prevista.

Producción se documenta como arquitectura prevista, pero no se considera desplegada dentro del alcance actual del proyecto académico.

## 1.3 Restricciones y supuestos

- La solución se ejecuta mediante contenedores.
- La composición prevista contiene catorce contenedores.
- Existen cinco servicios de aplicación.
- Existen cuatro bases de datos independientes.
- Las diferencias entre ambientes se suministran mediante configuración externa.
- Las bases de datos no se exponen públicamente.
- Cada base tiene un único servicio propietario.
- La configuración detallada vive en este documento.
- El SDD referencia esta infraestructura y no debe duplicarla.

## 1.4 Principios de infraestructura

La infraestructura de RedVital se rige por los siguientes principios:

1. mínimo privilegio;
2. separación por zonas;
3. propiedad única de datos;
4. ausencia de acceso directo desde borde hacia datos;
5. configuración externa por ambiente;
6. secretos fuera del repositorio y de las imágenes;
7. imágenes de contenedor endurecidas;
8. observabilidad centralizada;
9. respaldo independiente de cada base;
10. coherencia entre documentación y composición real.

---

# 2. Topología y red

## 2.1 Topología general

RedVital adopta una topología distribuida compuesta por:

- un proxy de borde;
- una aplicación web;
- un API Gateway;
- cinco servicios independientes;
- cuatro bases de datos;
- dos componentes de observabilidad.

El flujo general de acceso es:

```text
Usuario / Internet
        |
        v
      Caddy
        |
        v
+--------------------------+
| Presentación / Entrada   |
|                          |
| - Aplicación Web         |
| - YARP API Gateway       |
+--------------------------+
        |
        v
+---------------------------------------+
| Servicios de aplicación              |
|                                       |
| - Servicio de Identidad               |
| - Servicio Institucional              |
| - Servicio de Campañas                |
| - Servicio de Donación                |
| - Worker / Servicio de Notificaciones |
+---------------------------------------+
        |
        v
+---------------------------------------+
| Bases de datos                        |
|                                       |
| - BD Identidad                        |
| - BD Institucional                    |
| - BD Campañas                         |
| - BD Donación                         |
+---------------------------------------+
```

La observabilidad se implementa mediante Prometheus y Grafana.

## 2.2 Zonas de infraestructura

La arquitectura se divide en cinco zonas.

### Zona 1 — Borde

**Componente:**

- Caddy.

**Responsabilidades:**

- recibir tráfico externo;
- realizar terminación TLS;
- aplicar límite de tasa;
- reenviar tráfico hacia la zona de entrada.

**Restricción:**

La zona de borde no tiene acceso directo a la zona de datos.

---

### Zona 2 — Presentación / Entrada

**Componentes:**

- Aplicación Web;
- YARP API Gateway.

**Responsabilidades:**

- servir la aplicación;
- recibir solicitudes provenientes del borde;
- validar token, ruta y rol de forma inicial;
- enrutar solicitudes hacia servicios internos.

**Restricción:**

La zona de entrada no accede directamente a bases de datos.

---

### Zona 3 — Aplicación

**Componentes:**

- Servicio de Identidad;
- Servicio Institucional;
- Servicio de Campañas;
- Servicio de Donación;
- Worker / Servicio de Notificaciones.

**Responsabilidades:**

- ejecutar lógica de negocio;
- aplicar autorización contextual;
- acceder únicamente a dependencias autorizadas;
- exponer métricas;
- comunicarse mediante contratos definidos.

---

### Zona 4 — Datos

**Componentes:**

- BD Identidad;
- BD Institucional;
- BD Campañas;
- BD Donación.

**Responsabilidades:**

- persistencia;
- aislamiento por servicio propietario;
- uso de credenciales independientes.

**Regla:**

> Cada base pertenece a un único servicio y ningún servicio puede acceder directamente a la base de otro.

---

### Zona 5 — Observabilidad

**Componentes:**

- Prometheus;
- Grafana.

**Responsabilidades:**

- recolección de métricas;
- consulta del estado del sistema;
- visualización operativa.

## 2.3 Matriz de alcanzabilidad

Toda conexión dibujada en la topología debe aparecer en esta matriz y toda fila permitida debe corresponder a una conexión real.

> Los puertos internos deben validarse contra la composición real antes de emitir la versión final.

| ID | Origen | Destino | Puerto | Protocolo | Permitido | Justificación |
|---|---|---|---:|---|---|---|
| A-01 | Internet | Caddy | 443 | HTTPS | Sí | Punto de entrada público |
| A-02 | Caddy | Aplicación Web | Por validar | HTTP/HTTPS interno | Sí | Entrega de interfaz web |
| A-03 | Caddy | YARP | Por validar | HTTP/HTTPS interno | Sí | Enrutamiento hacia API Gateway |
| A-04 | YARP | Servicio de Identidad | Por validar | HTTP/HTTPS interno | Sí | Operaciones de identidad |
| A-05 | YARP | Servicio Institucional | Por validar | HTTP/HTTPS interno | Sí | Operaciones institucionales |
| A-06 | YARP | Servicio de Campañas | Por validar | HTTP/HTTPS interno | Sí | Operaciones de campañas |
| A-07 | YARP | Servicio de Donación | Por validar | HTTP/HTTPS interno | Sí | Operaciones de donación |
| A-08 | YARP | Servicio de Notificaciones | Por validar | HTTP/HTTPS interno | Solo si aplica | Operaciones expuestas por contrato |
| A-09 | Servicio de Identidad | BD Identidad | 5432 | PostgreSQL/TCP | Sí | Persistencia propia |
| A-10 | Servicio Institucional | BD Institucional | 5432 | PostgreSQL/TCP | Sí | Persistencia propia |
| A-11 | Servicio de Campañas | BD Campañas | 5432 | PostgreSQL/TCP | Sí | Persistencia propia |
| A-12 | Servicio de Donación | BD Donación | 5432 | PostgreSQL/TCP | Sí | Persistencia propia |
| A-13 | Servicio A | Base de Servicio B | — | — | No | Acceso cruzado prohibido |
| A-14 | Zona de Borde | Zona de Datos | — | — | No | Invariante borde-datos |
| A-15 | Zona de Entrada | Zona de Datos | — | — | No | Las bases no son accesibles desde frontend o gateway |
| A-16 | Prometheus | Servicios | Por validar | HTTP | Sí | Recolección de métricas |
| A-17 | Grafana | Prometheus | 9090 | HTTP | Sí | Consulta de métricas |
| A-18 | Internet | Bases de datos | — | — | No | Las bases no se exponen públicamente |

## 2.4 Invariantes de red

### Invariante borde-datos

No existe comunicación directa entre la Zona de Borde y la Zona de Datos.

```text
Borde  X------> Datos
```

### Invariante entrada-datos

La aplicación web y YARP no pueden acceder directamente a las bases.

```text
Presentación / Entrada  X------> Datos
```

### Invariante de propiedad de datos

Cada servicio puede acceder únicamente a su propia base.

```text
Servicio Identidad      ---> BD Identidad
Servicio Institucional  ---> BD Institucional
Servicio Campañas       ---> BD Campañas
Servicio Donación       ---> BD Donación
```

### Invariante de exposición

Las bases de datos no publican puertos al exterior.

## 2.5 Fronteras de confianza

| ID | Frontera | Descripción |
|---|---|---|
| F-01 | Internet ↔ Borde | Entrada de tráfico no confiable hacia Caddy |
| F-02 | Borde ↔ Presentación/Entrada | Paso desde Caddy hacia Web/YARP |
| F-03 | Presentación/Entrada ↔ Aplicación | Paso desde YARP hacia los servicios |
| F-04 | Aplicación ↔ Datos | Acceso de servicios propietarios a sus bases |
| F-05 | Aplicación ↔ Observabilidad | Exposición y recolección de métricas |

---

# 3. Cómputo y contenedores

## 3.1 Composición

La composición prevista contiene catorce contenedores.

| # | Componente | Tipo | Función |
|---:|---|---|---|
| 1 | Caddy | Proxy de borde | Entrada externa, TLS y límite de tasa |
| 2 | Aplicación Web | Frontend | Interfaz de usuario |
| 3 | YARP | API Gateway | Enrutamiento y validación inicial |
| 4 | Servicio de Identidad | Servicio | Autenticación, identidad y emisión de tokens |
| 5 | Servicio Institucional | Servicio | Gestión institucional y territorial |
| 6 | Servicio de Campañas | Servicio | Gestión de campañas |
| 7 | Servicio de Donación | Servicio | Donaciones, inventario y transferencias |
| 8 | Servicio de Notificaciones | Worker/Servicio | Procesamiento asíncrono |
| 9 | BD Identidad | Base de datos | Persistencia de identidad |
| 10 | BD Institucional | Base de datos | Persistencia institucional |
| 11 | BD Campañas | Base de datos | Persistencia de campañas |
| 12 | BD Donación | Base de datos | Persistencia de donaciones |
| 13 | Prometheus | Observabilidad | Recolección de métricas |
| 14 | Grafana | Observabilidad | Visualización |

## 3.2 Arranque

La composición debe poder levantarse mediante un único comando.

El arranque debe respetar dependencias de salud, evitando que un servicio inicie antes de que su dependencia esté disponible.

**Estado de verificación:** pendiente de medición sobre la composición real.

## 3.3 Endurecimiento

Los contenedores deben ejecutarse con el menor privilegio posible.

Controles previstos:

- usuario no root;
- sistema de archivos de solo lectura cuando aplique;
- `no-new-privileges`;
- retiro de capacidades Linux innecesarias;
- límites de CPU;
- límites de memoria;
- ausencia de puertos innecesarios.

Ejemplo conceptual:

```yaml
read_only: true
security_opt:
  - no-new-privileges:true
cap_drop:
  - ALL
```

Los valores concretos de CPU y memoria deben ajustarse después de medir la composición real.

## 3.4 Construcción segura de imágenes

Las imágenes deben utilizar construcción multietapa.

La imagen final de ejecución no debe contener herramientas innecesarias, como:

- compiladores;
- gestores de paquetes;
- herramientas de desarrollo;
- intérpretes de comandos, cuando la tecnología lo permita.

**Estado de verificación:** pendiente de inspección de imágenes finales.

---

# 4. Almacenamiento y datos

## 4.1 Bases de datos

RedVital utiliza cuatro bases independientes:

- BD Identidad;
- BD Institucional;
- BD Campañas;
- BD Donación.

## 4.2 Propiedad

| Base | Servicio propietario |
|---|---|
| BD Identidad | Servicio de Identidad |
| BD Institucional | Servicio Institucional |
| BD Campañas | Servicio de Campañas |
| BD Donación | Servicio de Donación |

## 4.3 Reglas de aislamiento

- cada base tiene una credencial diferente;
- no existe un superusuario compartido por servicios;
- cada servicio accede únicamente a su base;
- una base no se utiliza como mecanismo de integración;
- los servicios se comunican mediante contratos.

## 4.4 Validación esperada

Debe intentarse que un servicio acceda a la base de otro y comprobar que la operación falla.

**Estado de verificación:** pendiente de ejecución.

---

# 5. Seguridad y accesos

## 5.1 Estrategia por capas

La seguridad no depende de un único componente.

El flujo general es:

```text
Caddy
  ↓
Servicio de Identidad
  ↓
YARP
  ↓
Servicios propietarios
  ↓
Bases propietarias
```

Responsabilidades:

- **Caddy:** entrada, TLS y límite de tasa;
- **Identidad:** autenticación y emisión de tokens;
- **YARP:** validación inicial de token, ruta y rol;
- **Servicios:** autorización contextual;
- **Bases:** aislamiento por credenciales y propiedad.

## 5.2 Modelo STRIDE

| Letra | Categoría | Descripción |
|---|---|---|
| S | Spoofing | Suplantación de identidad |
| T | Tampering | Modificación no autorizada |
| R | Repudiation | Negación de una acción |
| I | Information Disclosure | Exposición de información |
| D | Denial of Service | Saturación o indisponibilidad |
| E | Elevation of Privilege | Obtención indebida de privilegios |

### Matriz STRIDE

| Frontera | Spoofing | Tampering | Repudiation | Information Disclosure | Denial of Service | Elevation of Privilege |
|---|---|---|---|---|---|---|
| F-01 Internet ↔ Borde | Suplantación de cliente legítimo | Manipulación de solicitudes | Negación del origen de una solicitud | Exposición de cabeceras o contenido | Saturación del punto de entrada | Intento de alcanzar rutas restringidas |
| F-02 Borde ↔ Entrada | Suplantación de origen interno | Alteración de cabeceras reenviadas | Falta de correlación de acciones | Filtración de tráfico interno | Saturación de YARP o frontend | Uso indebido de cabeceras |
| F-03 Entrada ↔ Aplicación | Token falso o robado | Modificación de token o parámetros | Negación de operaciones ejecutadas | Exposición de claims o datos de negocio | Saturación de servicios | Acceso con rol o jurisdicción indebidos |
| F-04 Aplicación ↔ Datos | Uso de credenciales de otro servicio | Modificación no autorizada de datos | Negación de escrituras | Lectura de datos o esquema ajeno | Saturación de conexiones o consultas | Uso de credenciales excesivas |
| F-05 Aplicación ↔ Observabilidad | Fuente falsa de métricas | Manipulación de métricas | Falta de trazabilidad operativa | Métricas o logs con datos sensibles | Saturación de observabilidad | Acceso administrativo indebido |

## 5.3 Controles asociados

| Área | Control | Objetivo | Estado |
|---|---|---|---|
| Borde | TLS en Caddy | Proteger tráfico externo | Pendiente de evidencia |
| Borde | Rate limiting | Reducir abuso automatizado | Pendiente de evidencia |
| Identidad | Tokens firmados | Prevenir suplantación y manipulación | Diseño definido |
| Gateway | Validación de token, ruta y rol | Compuerta inicial | Pendiente de evidencia |
| Servicios | Autorización contextual | Validar jurisdicción y permisos | Diseño definido |
| Datos | Credencial independiente por servicio | Impedir acceso cruzado | Pendiente de evidencia |
| Datos | Sin superusuario compartido | Mínimo privilegio | Pendiente de evidencia |
| Red | Separación por zonas | Limitar movimiento lateral | Diseño definido |
| Contenedores | Usuario no privilegiado | Reducir impacto de compromiso | Pendiente de evidencia |
| Secretos | Configuración externa | Evitar secretos en imágenes | Diseño definido |
| CI | Detección de secretos | Bloquear secretos antes de fusionar | Pendiente de evidencia |
| CI | Escaneo de imágenes | Detectar vulnerabilidades | Pendiente de evidencia |
| Auditoría | Identificador de correlación | Mejorar trazabilidad | Diseño definido |
| Observabilidad | Acceso restringido | Proteger métricas | Pendiente de evidencia |

## 5.4 Gestión de secretos

Los secretos incluyen:

- credenciales de bases;
- claves de firma;
- certificados;
- tokens internos;
- otros valores sensibles.

Reglas:

- no se almacenan en Git;
- no se incluyen en imágenes;
- no se escriben en Dockerfiles;
- no se imprimen en logs;
- se suministran externamente.

### Origen por ambiente

| Ambiente | Origen de secretos |
|---|---|
| Desarrollo | Variables locales o archivo no versionado |
| QA | Configuración externa específica del ambiente |
| Producción | Mecanismo seguro por definir |

### Rotación de clave de firma

La arquitectura debe permitir rotar la clave sin invalidar inmediatamente sesiones vigentes.

Procedimiento previsto:

1. mantener temporalmente la clave anterior;
2. publicar la nueva clave para emisión;
3. permitir validación con ambas durante transición;
4. retirar la anterior al finalizar la vigencia aplicable.

**Estado de verificación:** pendiente de ejecución en QA.

## 5.5 Controles de CI

### Detección de secretos

El pipeline debe bloquear un Pull Request si detecta un secreto o patrón sensible.

**Estado:** pendiente de prueba.

### Escaneo de vulnerabilidades

Las imágenes deben analizarse antes de aceptarse.

El pipeline debe:

1. construir o descargar la imagen;
2. ejecutar el escáner;
3. comparar contra un umbral acordado;
4. fallar si el umbral se supera.

**Herramienta:** por definir.  
**Umbral:** por definir.  
**Estado:** pendiente.

---

# 6. Observabilidad

## 6.1 Prometheus

Responsabilidades:

- recolectar métricas;
- consultar endpoints de salud o métricas;
- almacenar series temporales operativas.

## 6.2 Grafana

Responsabilidades:

- consultar Prometheus;
- visualizar métricas;
- apoyar diagnóstico.

## 6.3 Reglas

- no exponer secretos;
- no incluir tokens;
- evitar datos personales en métricas;
- restringir acceso administrativo;
- mantener correlación suficiente para diagnóstico.

## 6.4 Verificación

| Verificación | Resultado esperado | Estado |
|---|---|---|
| Prometheus consulta servicios | Métricas disponibles | Pendiente |
| Grafana consulta Prometheus | Paneles disponibles | Pendiente |
| Métricas sin secretos | Sin valores sensibles | Pendiente |
| Acceso administrativo restringido | Solo usuarios autorizados | Pendiente |

---

# 7. Respaldo y recuperación

## 7.1 Estrategia

Cada una de las cuatro bases debe poder respaldarse y restaurarse independientemente.

| Base | Respaldo | Restauración |
|---|---|---|
| Identidad | Requerido | Requerida |
| Institucional | Requerido | Requerida |
| Campañas | Requerido | Requerida |
| Donación | Requerido | Requerida |

## 7.2 Procedimiento general de respaldo

1. identificar la base;
2. ejecutar la herramienta de respaldo correspondiente;
3. almacenar el archivo fuera del contenedor;
4. registrar fecha y origen;
5. comprobar que el respaldo sea legible.

## 7.3 Procedimiento general de restauración

1. disponer de una instancia limpia;
2. restaurar el respaldo;
3. iniciar el servicio propietario;
4. verificar salud;
5. consultar datos conocidos;
6. registrar la evidencia.

## 7.4 Criterio de validación

Una persona distinta del autor del procedimiento debe poder ejecutar la restauración siguiendo únicamente este documento.

**Estado:** procedimiento definido; prueba real pendiente.

## 7.5 RTO y RPO

El backlog actual no fija valores numéricos de RTO y RPO.

Por tanto, esta versión documenta respaldo y restauración, pero no declara objetivos de recuperación que no hayan sido acordados formalmente.

---

# 8. Ambientes y configuración

## 8.1 Principio

La misma imagen de contenedor debe poder ejecutarse en todos los ambientes.

Las diferencias se suministran mediante configuración externa.

## 8.2 Comparación

| Aspecto | Desarrollo | QA | Producción |
|---|---|---|---|
| Certificados | Locales o de desarrollo | Propios de QA | Válidos de producción |
| Nivel de logs | Detallado | Controlado | Restringido |
| Secretos | Locales fuera del repo | Independientes de QA | Independientes y rotables |
| Datos | Sintéticos/locales | Sintéticos/controlados | Reales |
| Límites de recursos | Flexibles | Cercanos a operación | Según capacidad |
| Exposición | Local | Controlada | Solo borde público |
| Bases expuestas al host | Solo si se requiere | No | No |
| Estado | Construido | Construido / por validar | No construido |

## 8.3 Producción

Antes de desplegar Producción se debe contar con:

- certificados válidos;
- gestión formal de secretos;
- dimensionamiento de recursos;
- respaldo operativo;
- controles de acceso verificados;
- monitoreo;
- procedimiento de despliegue;
- procedimiento de recuperación.

---

# 9. Verificación y evidencias

## 9.1 Estado general

Este documento distingue entre:

- **diseño definido**, cuando la decisión arquitectónica está documentada;
- **verificación pendiente**, cuando aún falta ejecutar la prueba sobre la composición real.

## 9.2 Matriz de verificaciones

| Verificación | Resultado esperado | Estado |
|---|---|---|
| Borde no alcanza bases | Conexión rechazada | Pendiente |
| Gateway no alcanza bases | Conexión rechazada | Pendiente |
| Servicio accede a base propia | Permitido | Pendiente |
| Servicio accede a base ajena | Rechazado | Pendiente |
| Contenedores no corren como root | Todos cumplen | Pendiente |
| Solo lectura donde aplique | Cumple | Pendiente |
| Secreto en Pull Request | Pipeline falla | Pendiente |
| Imagen vulnerable sobre umbral | Pipeline falla | Pendiente |
| Rotación de clave | Sesiones vigentes continúan válidas durante transición | Pendiente |
| Restore de cuatro bases | Restauración completada | Pendiente |
| Prometheus recolecta métricas | Métricas disponibles | Pendiente |
| Grafana visualiza métricas | Paneles disponibles | Pendiente |

## 9.3 Pendientes técnicos

Antes de emitir la versión final deben validarse:

- puertos internos exactos;
- composición final de redes Docker;
- límites reales de CPU y memoria;
- herramienta de detección de secretos;
- herramienta de escaneo de imágenes;
- umbral de severidad;
- prueba de aislamiento de bases;
- prueba de rotación de clave;
- prueba de respaldo y restauración;
- verificación de los catorce contenedores.

---

# 10. Relación con otros documentos

## 10.1 SAD V2

El SAD describe la estrategia arquitectónica general.

Infraestructura V1 contiene el detalle técnico de:

- zonas;
- comunicaciones;
- seguridad;
- ambientes;
- hardening;
- secretos;
- respaldo.

## 10.2 SDD V1

El SDD debe referenciar esta configuración y no duplicarla.

La vista física debe coincidir con:

- las cinco zonas;
- los catorce contenedores;
- las conexiones permitidas;
- la matriz de alcanzabilidad.

## 10.3 DD V2

El DD debe coincidir con:

- cuatro bases;
- propiedad única;
- contratos entre servicios;
- ausencia de acceso cruzado.

## 10.4 ADR

Las decisiones estructurales documentadas mediante ADR son la fuente de decisión.

Infraestructura implementa y verifica esas decisiones.

---

# 11. Criterios de cierre

Infraestructura V1 puede considerarse cerrada cuando:

- [ ] La topología refleja los catorce contenedores.
- [ ] Las cinco zonas están declaradas.
- [ ] Toda arista del diagrama aparece en la matriz de alcanzabilidad.
- [ ] Toda fila de la matriz corresponde a una conexión real.
- [ ] No existe acceso directo borde-datos.
- [ ] Cada servicio accede únicamente a su base.
- [ ] El modelo STRIDE contiene 30 celdas completas.
- [ ] Toda amenaza tiene un control o riesgo aceptado documentado.
- [ ] La tabla de ambientes está completa.
- [ ] Las diferencias entre ambientes viven en configuración externa.
- [ ] Los contenedores aplican mínimo privilegio.
- [ ] Los secretos no viven en repositorio ni imágenes.
- [ ] La detección de secretos ha sido probada.
- [ ] El escaneo de imágenes ha sido probado.
- [ ] Las cuatro bases tienen respaldo.
- [ ] Las cuatro bases pueden restaurarse.
- [ ] La restauración fue ejecutada por una persona distinta al autor.
- [ ] Prometheus y Grafana funcionan sobre la composición real.
- [ ] No existen contradicciones con SAD V2, SDD V1 ni DD V2.

---

# 12. Control de cambios y aprobaciones

## 12.1 Control de cambios

| Versión | Fecha | Cambio |
|---|---|---|
| 1.0 | 2026-09-22 | Creación del Documento de Infraestructura V1 |

## 12.2 Aprobaciones

| Rol | Estado | Fecha |
|---|---|---|
| Arquitectura | Pendiente | — |
| Infraestructura | Pendiente | — |
| Desarrollo | Pendiente | — |
| QA | Pendiente | — |
| Gestión del proyecto | Pendiente | — |
