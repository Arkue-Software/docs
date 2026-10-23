# Documentación de Infraestructura RedVital

**Repositorio:** `sjvdc/redvital` 
**Versión de Infraestructura:** V2 **
Fecha de Actualización:** Octubre 2026 
**Responsable:** DevOps

## 1. Arquitectura del Sistema

El sistema RedVital está diseñado bajo una arquitectura orientada a microservicios y desplegada en múltiples Máquinas Virtuales (VMs) temáticas para garantizar la separación de responsabilidades, seguridad y escalabilidad. La orquestación local se realiza mediante contenedores (Docker Compose).

La topología se divide en los siguientes módulos principales:

- **VM Borde (`vm-borde`)**: Punto de entrada a la red. Actúa como Reverse Proxy, gestiona terminación SSL/TLS y enruta el tráfico hacia los servicios internos.
    
- **VM Servicios (`vm-servicios`)**: Capa de cómputo donde residen las aplicaciones core y microservicios del negocio de RedVital.
    
- **VM Datos (`vm-datos`)**: Capa de persistencia segura, alojando motores de bases de datos relacionales (PostgreSQL).
    
- **Kafka (`kafka`)**: Clúster/Instancia de mensajería y transmisión de eventos asíncronos para la comunicación desacoplada entre microservicios.
    
- **VM Tools (`vm-tools`)**: Stack de observabilidad, monitorización y herramientas operativas (Grafana, Prometheus).
    
Para visualizar el diagrama, ver imagen `infra_2.drawio` dentro del repositorio de documentos en la carpeta de infraestructura.
## 2. Componentes Base

- **Proxy Inverso y Web Server:** [Caddy](https://caddyserver.com/ "null") (Se prefiere por su auto-renovación de certificados Let's Encrypt y configuración simple en `Caddyfile`).
    
- **Base de Datos Relacional:** PostgreSQL (Configurado con conexiones seguras vía TLS `entrypoint-tls.sh` y control de accesos estricto en `pg_hba.conf`).
    
- **Event-Streaming:** Apache Kafka (Configurado con scripts propios de inicialización `inicializar.sh` y exportadores JMX `jmx-exportador.yaml` para métricas).
    
- **Monitorización:** Grafana & Prometheus (Provisionados vía código en `vm-tools/grafana/provisioning`).
    
- **Orquestación de Contenedores:** Docker y Docker Compose.
    

## 3. Variables de Entorno y Configuración

Los secretos y configuraciones dependen del ambiente de despliegue. Existen plantillas base ubicadas en el directorio `ambientes/`:

- `ambientes/produccion.env.example`: Configuración endurecida para entornos productivos.
    
- `ambientes/qa.env.example`: Configuración para entornos de Staging/QA.
    

La generación y empaquetado de secretos reales (no versionados) se gestiona mediante herramientas internas:

- `scripts/generar-secretos.sh`
    
- `scripts/empaquetar-secretos.sh`
    

_(Nota: Los secretos reales deben estar alojados en un Vault seguro y ser inyectados en tiempo de despliegue)_.

## 4. Dependencias y Matriz de Puertos

A continuación, se detalla la matriz de puertos esperados basados en la arquitectura contenerizada estándar (sujetos a políticas del firewall).

|   |   |   |   |
|---|---|---|---|
|**Componente/Servicio**|**Puerto Expuesto (Interno/Externo)**|**Protocolo**|**Descripción**|
|**Caddy (Borde)**|`80`, `443`|TCP / HTTP(S)|Tráfico público web y terminación SSL.|
|**PostgreSQL (Datos)**|`5432`|TCP|Acceso interno a la BD (restringido por red).|
|**Kafka (Broker)**|`9092`|TCP|Comunicación de eventos inter-servicios.|
|**Grafana (Tools)**|`3000`|TCP / HTTP|Dashboard de monitorización.|
|**Prometheus (Tools)**|`9090`|TCP / HTTP|Recolección de métricas.|
|**Servicios Core**|`Varios (ej. 8080)`|TCP / HTTP|APIs y backends (detallados en `vm-servicios/docker-compose.yml`).|

## 5. Seguridad y Redes

La postura de seguridad (DevSecOps) del proyecto incluye múltiples capas de protección:

1. **Reglas de Firewall:** Definidas de forma declarativa mediante el script `scripts/firewall.sh`. Bloquea el tráfico no autorizado inter-VMs.
    
2. **Cifrado en Tránsito:** Tráfico externo cifrado mediante HTTPS provisto por Caddy (`vm-borde/caddy/Caddyfile`). Las conexiones a BD obligan el uso de TLS (`entrypoint-tls.sh`).
    
3. **Protección de Secretos:** Los secretos nunca se versionan en Git. Se gestionan dinámicamente mediante scripts customizados.
    
4. **Aislamiento de BD:** El archivo `pg_hba.conf` restringe estrictamente qué IPs/Subredes pueden autenticarse en PostgreSQL.
    

## Playbook QA

Este procedimiento estandariza el paso a paso para realizar un despliegue confiable en el ambiente de **QA / Staging** utilizando los recursos del repositorio.

### Prerrequisitos

- **Accesos:** Permisos SSH habilitados y configurados para las VMs del entorno de QA (`vm-borde`, `vm-servicios`, `vm-datos`, `vm-tools`, y nodo `kafka`).
    
- **Herramientas CLI:** `docker`, `docker-compose`, `bash`, `python3` (para tests).
    
- **Secretos:** Acceso al gestor de secretos o contraseñas para inicializar el `.env` de QA.
    

### Paso a paso de Despliegue

1. **Preparación del Código y Secretos:**
    

```
    # 1. Copiar código al servidor de QA
    bash scripts/copiar-codigo.sh qa

    # 2. Generar y empaquetar secretos para QA
    cp ambientes/qa.env.example .env
    bash scripts/generar-secretos.sh --env qa
    bash scripts/empaquetar-secretos.sh

```

2. **Despliegue de la Capa de Datos y Semillas:**
    

```
    cd vm-datos
    docker-compose up -d
    
    # Aplicar semillas (mocks de datos) para QA
    cat ../semillas/qa/campanas.sql | docker exec -i postgres_db psql -U usuario_qa -d redvital_qa
    cat ../semillas/qa/donacion.sql | docker exec -i postgres_db psql -U usuario_qa -d redvital_qa
    cd ..

```

3. **Despliegue de Kafka (Mensajería):**
    

```
    cd kafka
    bash scripts/arrancar.sh
    bash scripts/inicializar.sh # Crea tópicos base según catalogo-temas.conf
    cd ..

```

4. **Despliegue de Servicios y Borde:**
    

```
    # Desplegar backend/frontend
    cd vm-servicios
    docker-compose up -d --build
    cd ..

    # Levantar Reverse Proxy (último paso para aceptar tráfico)
    cd vm-borde
    docker-compose up -d --build
    cd ..

```

5. **Despliegue de Observabilidad:**
    

```
    cd vm-tools
    bash ../scripts/renderizar-tools.sh
    docker-compose up -d
    cd ..

```

### Verificación / Smoke Tests

Una vez que los contenedores están arriba, se deben validar los servicios utilizando los scripts automatizados de la carpeta `pruebas/`.

1. **Verificación Base de Contenedores:**
    

```
    docker ps -a | grep Exited # No debe retornar ningún contenedor fallido

```

2. **Ejecutar Suite de Pruebas de QA:**
    

```
    cd pruebas/
    chmod +x correr-pruebas.sh
    ./correr-pruebas.sh

```

_Nota: Este script orquesta internamente `extremo_a_extremo.py` (simulación de flujo de usuario completo) y `resiliencia_kafka.py` (comprobación de colas y reconexión)._

3. **Revisión de Logs en vivo (Opcional):**
    

```
    docker logs -f <nombre_contenedor_servicio_principal>

```

### Estrategia de Rollback

En caso de que los `Smoke Tests` fallen o la versión desplegada introduzca defectos críticos que bloqueen al equipo de QA:

1. **Bajar contenedores afectados:**
    

```
    cd vm-servicios && docker-compose down
    cd ../vm-borde && docker-compose down

```

2. **Revertir base de datos (Si hubo migraciones destructivas):**
    
    - Ejecutar script de restauración del último snapshot de BD de QA. _(Si solo fueron inserciones de semillas, se puede hacer un drop de la base de datos y recrearla)._
        
3. **Checkout a la versión anterior:**
    

```
    git checkout <TAG_O_COMMIT_ANTERIOR>

```

4. **Redesplegar:** Repetir los pasos del 4 y 5 del **Paso a paso de Despliegue** usando las imágenes/código del commit funcional anterior.