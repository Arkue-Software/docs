# Documentación de Infraestructura RedVital

**Repositorio:** `Arkue-Software/Despliegue_RedVital`
**Versión de Infraestructura:** V3.1
**Fecha de actualización:** 2 de noviembre de 2026
**Responsable:** DevOps

## Control de versiones

| Versión | Fecha | Descripción |
|---|---|---|
| 3.0 | Octubre 2026 | Versión inicial del documento de infraestructura en el repositorio de documentación. |
| 3.1 | 02/11/2026 | Conciliación con el repositorio `Despliegue_RedVital` (22/10/2026) y con su guía de QA: se incorporan APISIX, el trabajador de Notificaciones, la topología de siete VM, la matriz de puertos y orígenes del firewall, los controles de seguridad implementados y el procedimiento real de despliegue y verificación. Se registra la base propia de Notificaciones en construcción. |

## 1. Arquitectura del Sistema

El sistema RedVital está diseñado bajo una arquitectura de microservicios desplegada en múltiples Máquinas Virtuales (VMs) temáticas para garantizar la separación de responsabilidades, seguridad y escalabilidad. La orquestación se realiza mediante contenedores (Docker Compose), un archivo de composición por tipo de VM.

La topología completa tiene **siete VM**: tres por ambiente (QA y Producción) y una VM de Tools compartida. QA y Producción no comparten bases, secretos, volúmenes, certificados ni archivos de configuración; cada ambiente tiene su propio archivo `ambientes/<ambiente>.env`.

| VM | Qué corre | Para qué sirve |
|---|---|---|
| **VM Borde (`vm-borde`)** | Caddy, la aplicación web (archivos estáticos de React) y Apache APISIX 3.19 | Única puerta pública. Caddy termina HTTPS y reparte: `/api/*` va a APISIX y lo demás a la web. APISIX valida el token y el rol de la ruta antes de enrutar al servicio. |
| **VM Servicios (`vm-servicios`)** | Identidad (.NET 10), Campañas (.NET 10), Donación (Java 25), el trabajador de Notificaciones (Java 25) y Apache Kafka 4.3.1 con su trabajo de inicialización `kafka-init` | Lógica de negocio y mensajería. Campañas y Donación solo se comunican por Kafka. |
| **VM Datos (`vm-datos`)** | Tres PostgreSQL 16: `db_identidad`, `db_campana` y `db_donacion`, con sus migraciones Flyway y semillas de QA | Persistencia. Una base por servicio; ningún servicio lee la base de otro. |
| **VM Tools (`vm-tools`)**, compartida | Prometheus, Grafana y Kafbat | Monitoreo y lectura de Kafka. Solo escucha en `127.0.0.1` y se accede por túnel SSH. |

En QA, las cuatro VM del ambiente son:

| VM | IP |
|---|---|
| Datos | `10.43.99.183` |
| Servicios | `10.43.99.174` |
| Borde | `10.43.98.220` |
| Tools | `10.43.100.121` |

**Servicios pendientes.** El Servicio Institucional y su base `db_institucional` forman parte de la arquitectura objetivo, pero no tienen contenedor en el despliegue actual. La base propia del Servicio de Notificaciones está en construcción en el repositorio `databases` (rama `feature/db-notifications`); su esquema aún no está publicado, y su despliegue, puerto y regla de firewall se agregarán a este documento cuando exista y esté registrada en un ADR.

Recorrido de una petición:

```
Navegador ──HTTPS 443──► Caddy (Borde)
                           ├─ /api/*  ──► APISIX ──valida token y rol──► servicio (VM Servicios)
                           └─ lo demás ─► web (archivos estáticos de React)

Identidad / Campañas / Donación ──TLS──► su propia base (VM Datos)

Campañas ──EV-03 campaña publicada──► Kafka ──► Donación   (proyección local de campañas)
Donación ──EV-01 donación registrada──► Kafka ──► Campañas (contador de donaciones)
Donación ──EV-02 aviso solicitado──► Kafka ──► Notificaciones ──SMTP──► proveedor de correo
```

Para visualizar el diagrama, ver imagen `infra_2.drawio` dentro del repositorio de documentos en la carpeta de infraestructura.

## 2. Componentes Base

- **Proxy de borde y servidor web:** [Caddy](https://caddyserver.com/) (ADR-013). Se prefiere por su gestión automática de certificados (Let's Encrypt en producción con dominio público; CA local en QA, `TLS_MODO=internal`) y su configuración simple en `vm-borde/caddy/Caddyfile`. Termina TLS, agrega cabeceras de seguridad, limita el cuerpo de las peticiones a 1 MiB y responde 404 a `/internal`.

- **API Gateway:** Apache APISIX 3.19 en modo sin base de datos (ADR-019), con configuración declarativa del repositorio `tools` (`APISIX-config`). Valida firma, emisor y vigencia del token contra el JWKS de Identidad (plugin `openid-connect`), aplica la autorización por ruta y rol (plugin propio `redvital-autorizacion`), reporta las denegaciones a Identidad, aplica el límite de tasa (`limit-count`) y bloquea `/api/internal/*`.

- **Base de Datos Relacional:** PostgreSQL 16, con TLS obligatorio (`entrypoint-tls.sh`) y control de accesos en `pg_hba.conf` (`hostssl` + SCRAM; sin TLS se rechaza). Cada base tiene un usuario propietario (migraciones) y un usuario de servicio con permisos mínimos. Las migraciones se aplican con Flyway 11 como trabajos efímeros (`migracion-*`).

- **Event-Streaming:** Apache Kafka 4.3.1 en modo KRaft (imagen `redvital/kafka:4.3.1`), con SASL/SCRAM-SHA-512 por servicio, autorizador sin permisos por defecto y ACL por servicio. Los temas los crea solo el trabajo `kafka-init` a partir de `kafka/catalogo-temas.conf` (el broker no crea temas automáticamente). Métricas mediante el exportador JMX (`jmx-exportador.yaml`).

- **Notificaciones:** trabajador sin puertos publicados que consume EV-02 de Kafka, consulta el contacto del donante a Donación y envía el correo por SMTP (en QA, `smtp.gmail.com:587` con STARTTLS). Es el único contenedor con salida a internet hacia un proveedor externo.

- **Monitorización:** Prometheus 3.4 y Grafana 11.6 (provisionados vía código en `vm-tools/grafana/provisioning`), Kafbat 1.2 para inspeccionar Kafka con una cuenta de solo lectura, y node-exporter y cAdvisor en cada VM (perfil `metricas`).

- **Orquestación de Contenedores:** Docker Engine con el plugin `docker compose`.

## 3. Variables de Entorno y Configuración

Los secretos y configuraciones dependen del ambiente de despliegue. Existen plantillas base ubicadas en el directorio `ambientes/`, que se copian como `ambientes/qa.env` y `ambientes/produccion.env`:

- `ambientes/produccion.env.example`: Configuración endurecida para entornos productivos.

- `ambientes/qa.env.example`: Configuración para el ambiente de QA.

Cada archivo `.env` contiene, sin secretos: las IP de las VM del ambiente (`IP_VM_DATOS`, `IP_VM_SERVICIOS`, `IP_VM_BORDE`, `IP_VM_TOOLS`), el `DOMINIO`, el `TLS_MODO`, la `VERSION` de las imágenes, la ruta `REPOS` de los repositorios hermanos, la configuración SMTP de Notificaciones y los perfiles de Compose (`semillas`, que nunca se usa en producción, y `metricas`).

La generación y empaquetado de secretos reales (no versionados) se gestiona mediante herramientas internas:

- `scripts/generar-secretos.sh <qa|produccion|tools>`: genera los secretos del ambiente en `secretos/<ambiente>/`, con permisos `0700`, fuera de Git.

- `scripts/empaquetar-secretos.sh <ambiente> <vm>`: empaqueta para cada VM solo los secretos que necesita.

_(Nota: los secretos se montan en los contenedores como archivos en `/run/secrets`; nunca van en variables de entorno, en las imágenes ni en Git. La llave de la CA de PostgreSQL y la clave de las cuentas de QA no salen de la máquina de confianza donde se generan.)_

**Repositorios que necesita cada VM.** Los archivos de composición construyen las imágenes desde los repositorios hermanos indicados por `REPOS` (por defecto `../..`), con esta estructura de carpetas:

| VM | Carpetas necesarias, una al lado de la otra |
|---|---|
| Datos | `Despliegue_RedVital`, `BDs_RedVital` |
| Servicios | `Despliegue_RedVital`, `Servicios_RedVital` |
| Borde | `Despliegue_RedVital`, `FrontEnd`, `Tools_RedVital` |
| Tools | `Despliegue_RedVital` |

_(Nota: en GitHub el código está ahora en repositorios separados (`databases`, `identity`, `campaign`, `donation`, `notifications`, `frontend` y `tools`, rama `develop`). Los compose y los scripts siguen esperando la estructura anterior: `BDs_RedVital/databases`, `Servicios_RedVital/services/<servicio>`, `FrontEnd/frontend-main` y `Tools_RedVital/tools`. Hasta que se ajusten las rutas, al clonar desde GitHub cada repositorio debe ubicarse en esa estructura de carpetas.)_

## 4. Dependencias y Matriz de Puertos

Cada puerto se publica solo en la IP de su VM, y el firewall lo abre únicamente al origen que lo necesita.

| **Componente/Servicio** | **Puerto** | **Protocolo** | **Solo desde** | **Descripción** |
|---|---|---|---|---|
| **Caddy (Borde)** | `80`, `443` | TCP / HTTP(S) | Cualquiera | Tráfico público web y terminación TLS. HTTP redirige a HTTPS. |
| **APISIX (Borde)** | `9091` | TCP / HTTP | Tools | Métricas de APISIX. APISIX no publica su puerto de tráfico: Caddy le llega por la red interna de la VM. |
| **Identidad (Servicios)** | `8080` | TCP / HTTP | Borde | API de Identidad. |
| **Campañas (Servicios)** | `8082` | TCP / HTTP | Borde | API de Campañas. |
| **Donación (Servicios)** | `8083` | TCP / HTTP | Borde | API de Donación. |
| **Donación (Servicios)** | `8183` | TCP / HTTP | Tools | Métricas `/actuator/prometheus`. |
| **Kafka (Servicios)** | `9094` | TCP | Tools | Escucha externa para Kafbat. Los servicios usan `kafka:9092` dentro de la VM. |
| **Kafka (Servicios)** | `9404` | TCP / HTTP | Tools | Exportador JMX. |
| **PostgreSQL Identidad (Datos)** | `5432` | TCP / TLS | Servicios | `db_identidad`. |
| **PostgreSQL Campañas (Datos)** | `5433` | TCP / TLS | Servicios | `db_campana`. |
| **PostgreSQL Donación (Datos)** | `5434` | TCP / TLS | Servicios | `db_donacion`. |
| **node-exporter / cAdvisor (todas)** | `9100`, `9180` | TCP / HTTP | Tools | Métricas de VM y de contenedores. |
| **Prometheus, Grafana, Kafbat (Tools)** | `9090`, `3000`, `8080` | TCP / HTTP | Solo `127.0.0.1` de Tools | Acceso por túnel SSH. |
| **Notificaciones (Servicios)** | Sin puertos publicados | SMTP saliente | — | Salida a internet hacia el proveedor de correo (QA: `smtp.gmail.com:587`). |

El trabajador de Notificaciones llama dentro de la VM de Servicios a Donación (`http://donation-service:8080`, consulta de contacto) y a Identidad (`http://identity-service:8080`, token de servicio).

## 5. Seguridad y Redes

La postura de seguridad (DevSecOps) del proyecto incluye múltiples capas de protección:

1. **Reglas de Firewall:** Definidas de forma declarativa mediante el script `scripts/firewall.sh <ambiente> <datos|servicios|borde>`. Como Docker publica los puertos con DNAT antes de la cadena `INPUT`, `ufw` no los ve: las reglas van en la cadena `DOCKER-USER` y filtran por el destino original. Se revisan con `--simular` y se instalan con `--instalar` como servicio systemd (`redvital-firewall`), que las reaplica tras cada arranque de Docker. El SSH no lo gestiona este script.

2. **Cifrado en Tránsito:** Tráfico externo cifrado mediante HTTPS provisto por Caddy (`vm-borde/caddy/Caddyfile`). Las conexiones a las bases obligan TLS 1.2 o superior (`entrypoint-tls.sh`) y los servicios verifican la CA del ambiente (`verify-ca`).

3. **Cabeceras y límites en el borde:** HSTS, CSP `default-src 'self'`, `X-Frame-Options: DENY`, `nosniff`, `Referrer-Policy: no-referrer`, sin cabecera `Server` y cuerpo máximo de 1 MiB (Caddy). Límite de tasa de 60 peticiones por minuto en las rutas públicas y de 10 por minuto en el inicio de sesión (APISIX). `/api/internal/*` responde 404 en Caddy y en APISIX.

4. **Protección de Secretos:** Los secretos nunca se versionan en Git. Se generan con `scripts/generar-secretos.sh`, se reparten por VM con `scripts/empaquetar-secretos.sh` y se montan como archivos en `/run/secrets`.

5. **Aislamiento de BD:** El archivo `pg_hba.conf` solo admite conexiones `hostssl` con SCRAM; cada base tiene usuario propietario y usuario de servicio con permisos por columna.

6. **Kafka:** Usuario SCRAM por servicio, autorizador sin permisos por defecto y ACL: cada servicio solo escribe y lee sus temas. Kafbat usa una cuenta de solo lectura (`kafka_inspeccion`).

7. **Contenedores:** Sin root, con `cap_drop: ALL`, `no-new-privileges` y sistema de archivos de solo lectura en los servicios, la web y Caddy.

## Playbook QA

Este procedimiento estandariza el paso a paso para realizar un despliegue confiable en el ambiente de **QA** utilizando los recursos del repositorio. Todos los comandos se ejecutan desde la raíz de `Despliegue_RedVital`.

### Prerrequisitos

- **Accesos:** Permisos SSH habilitados para las VMs del ambiente de QA (`vm-datos`, `vm-servicios`, `vm-borde` y `vm-tools`).

- **Herramientas:** Docker Engine con el plugin `docker compose` y el backend de firewall por defecto (iptables, que crea la cadena `DOCKER-USER`), `bash`, `rsync` y `python3` (para las pruebas). Reloj sincronizado en cada VM (chrony/NTP: el token admite 30 s de desfase) y salida a internet para construir las imágenes.

- **Secretos:** Una máquina de confianza para generar los secretos del ambiente.

### Paso a paso de Despliegue

1. **Preparación de la configuración y los secretos** (en la máquina de confianza, una sola vez):

```
    # 1. Crear el archivo del ambiente y ajustar las IP de la red privada, DOMINIO y TLS_MODO
    cp ambientes/qa.env.example ambientes/qa.env

    # 2. Generar los secretos del ambiente
    scripts/generar-secretos.sh qa

    # 3. Empaquetar para cada VM solo lo que necesita
    for vm in datos servicios borde tools; do scripts/empaquetar-secretos.sh qa $vm; done

    # En cada VM, desde la raíz de Despliegue_RedVital:
    tar -xzf secretos-qa-<vm>.tar.gz
```

2. **Copia del código a las VM** (desde la máquina de trabajo, con los repositorios uno al lado del otro):

```
    scripts/copiar-codigo.sh datos=10.43.99.183 servicios=10.43.99.174 borde=10.43.98.220 tools=10.43.100.121
```

   El script usa `rsync` por SSH, envía a cada VM solo los repositorios de su rol y nunca envía ni borra secretos, archivos `*.env`, la configuración generada de Tools ni la CA.

3. **Despliegue de la Capa de Datos, migraciones y semillas** (VM Datos):

```
    docker compose -f vm-datos/docker-compose.yml --env-file ambientes/qa.env up -d
```

   Las migraciones Flyway (`migracion-*`), las credenciales de servicio (`credenciales-identidad`) y las semillas de QA (`semilla-*`, perfil `semillas`) se ejecutan como trabajos del mismo compose.

4. **Despliegue de Servicios y Kafka** (VM Servicios):

```
    docker compose -f vm-servicios/docker-compose.yml --env-file ambientes/qa.env up -d --build
```

   Levanta Identidad, Campañas, Donación, el trabajador de Notificaciones, Kafka y el trabajo `kafka-init`, que crea los temas del catálogo y sus ACL.

5. **Despliegue del Borde** (VM Borde; último paso para aceptar tráfico):

```
    docker compose -f vm-borde/docker-compose.yml --env-file ambientes/qa.env up -d --build
```

   Cada paso termina cuando `docker compose ... ps -a` muestra los servicios `healthy` y los trabajos (`migracion-*`, `credenciales-identidad`, `semilla-*`, `kafka-init`) en `Exited (0)`.

6. **Firewall** (en Datos, Servicios y Borde):

```
    # Revisar las reglas
    scripts/firewall.sh qa <datos|servicios|borde> --simular

    # Instalarlas como servicio
    sudo scripts/firewall.sh qa <datos|servicios|borde> --instalar
```

7. **Despliegue de Observabilidad** (VM Tools):

```
    scripts/generar-secretos.sh tools
    scripts/renderizar-tools.sh
    docker compose -f vm-tools/docker-compose.yml up -d

    # Acceso desde el equipo de trabajo, por túnel
    ssh -L 3000:127.0.0.1:3000 -L 9090:127.0.0.1:9090 -L 8080:127.0.0.1:8080 usuario@vm-tools
```

### Verificación / Smoke Tests

Una vez que los contenedores están arriba, se validan los servicios con los scripts de la carpeta `pruebas/`.

1. **Verificación Base de Contenedores:**

```
    docker compose -f vm-<rol>/docker-compose.yml --env-file ambientes/qa.env ps -a
    # Nada en Restarting, unhealthy ni Exited (1)
```

2. **Cadena completa desde el equipo de trabajo** (Caddy → APISIX → Campañas → base):

```
    curl -s --cacert ~/redvital-qa-raiz.crt https://10.43.98.220/api/v1/campanias | head -c 200; echo
```

3. **Prueba de extremo a extremo** (VM Servicios, donde están la CA y la clave de las cuentas):

```
    python3 pruebas/extremo_a_extremo.py --base https://10.43.98.220 --ca redvital-qa-raiz.crt --secretos secretos/qa
```

   Recorre QA solo por el borde: seguridad del borde, visitante, donante, operador, eventos de Kafka y límite de tasa. Resultado esperado: 46/46 comprobaciones sobre una base nueva y 45/45 desde la segunda corrida. Entre dos corridas se espera un minuto por el límite de 10 inicios de sesión por minuto.

4. **Resiliencia ante la caída de Kafka** (VM Servicios; detiene el broker 60 s y lo vuelve a encender):

```
    python3 pruebas/resiliencia_kafka.py --base https://10.43.98.220 --ca redvital-qa-raiz.crt --secretos secretos/qa
```

5. **Pruebas de los componentes** (en el equipo de trabajo, antes de desplegar; no requiere ambiente):

```
    pruebas/correr-pruebas.sh                 # todas
    pruebas/correr-pruebas.sh donacion web    # solo esas
```

   Corre en contenedores las pruebas de bases, Identidad, Campañas, Donación, Notificaciones, web, gateway, contratos y secretos.

6. **Revisión de Logs en vivo (Opcional):**

```
    docker logs -f <nombre_contenedor_servicio_principal>
```

   Los contenedores del despliegue se llaman `redvital-qa-<rol>-<servicio>-1` o `redvital-tools-<servicio>-1`.

### Estrategia de Rollback

En caso de que los `Smoke Tests` fallen o la versión desplegada introduzca defectos críticos que bloqueen al equipo de QA:

1. **Volver el código a la versión anterior** en el equipo de trabajo:

```
    git checkout <TAG_O_COMMIT_ANTERIOR>
```

2. **Copiar y redesplegar** repitiendo los pasos 2 a 5 del **Paso a paso de Despliegue** sobre las VM afectadas.

3. **Base de datos:** las migraciones Flyway no se deshacen solas y no se editan una vez aplicadas. Si la versión nueva aplicó una migración, la versión anterior debe tolerar ese esquema; si no, se crea una migración correctiva. _(Si solo se cargaron semillas en QA, se puede eliminar el volumen de la base y recrearla.)_
