# ADR-[nnn]: [Elección de API GATEWAY]

- **Estado:** Propuesta | Aceptada | Rechazada | Reemplazada por ADR-[nnn]
- **Versión:** 1.0
- **Fecha:** 2026-09-27
- **Autor:** Samuel Velandia Del Castillo DevOps
- **Responsable de implementar:** Sara Munoz-desarrolladora, Samuel Velandia-DevOps
## Historial de cambios

| Versión | Fecha      | Descripción del cambio          | Autor                             |
| ------- | ---------- | ------------------------------- | --------------------------------- |
| 1.0     | 2026-09-27 | Creación inicial de la decisión | Samuel Jose Velandia Del Castillo |

## Contexto

A medida que el sistema escala para soportar múltiples canales de acceso de usuarios, la interacción directa entre los clientes externos y los servicios internos de la red privada representa un riesgo operativo y de rendimiento. Permitir acoplamientos directos genera fragmentación en las políticas de seguridad, dificulta la trazabilidad de las peticiones y expone los componentes internos a posibles vulnerabilidades o sobrecargas de tráfico.

Para proteger la fiabilidad del servicio, la continuidad del negocio y el prestigio de la marca, la arquitectura requiere una capa intermedia de orquestación.

## Opciones consideradas

Mínimo dos alternativas evaluadas.

1. Kong Gateway
2. Amazon API Gateway
3. Apache APISIX
4. Tyk
5. Traefik Proxy
6. Spring Cloud Gateway 

## Decisión

Se decidio por el uso de la herramienta apache APISIX.

## Justificación

La adopción de Apache APISIX se justifica por ser una solución 100% gratuita y de código abierto que elimina costos de licencias y permite a cualquier equipo crear extensiones en su lenguaje favorito (como Java, Go, Python o Lua) sin complicaciones. Destaca por su capacidad para manejar múltiples formas de comunicación en un solo lugar, desde tráfico web tradicional (HTTP) e Internet de las Cosas (MQTT) hasta sistemas de microservicios avanzados (gRPC y Dubbo). Además, su modo sin base de datos (DB-Less) permite que el sistema funcione de forma directa mediante simples archivos de configuración, sin necesidad de instalar bases de datos auxiliares que consuman memoria. Esto reduce al mínimo el uso de procesador y RAM, siendo la respuesta idónea cuando se tiene un límite de máquinas virtuales o recursos de servidor reducidos, pues aprovecha cada equipo al máximo con una operación ligera, económica y fácil de gestionar.

## Trade-off evaluado

Elegir Apache APISIX implica un _trade-off_ estratégico entre rendimiento, flexibilidad y responsabilidad operativa. Por un lado, se obtiene una plataforma de código abierto 100% gratuita que ofrece una latencia mínima, soporte multiprotocolo y la libertad de crear plugins en múltiples lenguajes de programación, además de contar con un modo DB-Less que maximiza los recursos en entornos con un límite de máquinas virtuales. Por otro lado, la contrapartida es que la organización debe asumir la gestión directa de la infraestructura: en el modo DB-Less se renuncia a la modificación dinámica mediante interfaz gráfica (Dashboard) y se exige orquestar flujos de CI/CD o integrar Redis para controlar cuotas globales de tráfico, mientras que en el modo tradicional se debe asumir la sobrecarga operativa de mantener y respaldar un clúster de `etcd`. En definitiva, se sacrifica la simplicidad "llave en mano" de un servicio completamente administrado en la nube a cambio de obtener velocidad extrema, control total de la arquitectura y la eliminación de costos por volumen de peticiones.

## Consecuencias

- Implicaciones Operativas y de Gestión. los cambios en las rutas o políticas de acceso exigen pasar obligatoriamente por pipelines de integración continua (GitOps)
- El equipo deberá contar o adquirir competencias en herramientas como Kubernetes, archivos YAML y conceptos del motor NGINX/Lua. A cambio, la arquitectura gana total inmunidad al bloqueo de proveedor (_vendor lock-in_), permitiendo trasladar las mismas configuraciones de APIs entre entornos locales (_on-premise_), cualquier nube pública o nodos _edge_ sin reescribir la lógica de negocio.
- Se logra una predictibilidad presupuestaria absoluta, ya que los costos operativos quedan vinculados únicamente al consumo fijo de cómputo (servidores o máquinas virtuales) y no al volumen de peticiones.