## EC-20 — Diagnóstico de un fallo en el ambiente de QA

*Atributo de calidad:* Mantenibilidad

*HU relacionadas:* HT-18 — arquitectura de infraestructura

*Subcaracterística:* Analizabilidad

*Prioridad:* Media

### Escenario estímulo–respuesta

| Elemento | Descripción |
|---|---|
| Fuente del estímulo | Desarrollador o responsable de infraestructura, ante un error reportado por un compañero o por el cliente |
| Estímulo | Una petición falló en QA y hay que determinar en qué componente y por qué, sin poder reproducir el caso |
| Contexto | Sistema desplegado, sin acceso a la máquina de quien reportó el fallo |
| Artefacto afectado | Registros del gateway y de ambos servicios, Prometheus, Grafana |
| Respuesta | La petición se sigue de extremo a extremo mediante un identificador de correlación que aparece en el registro de todos los componentes que la atendieron |
| Medida de respuesta | El 100% de las peticiones que cruzan el gateway lleva identificador de correlación propagado a los servicios. El componente donde ocurrió el fallo se identifica en 15 minutos o menos sin reproducir el error. Ningún registro contiene datos personales, causa clínica ni contenido de token |

### Verificación

- En una arquitectura distribuida, un registro por componente sin identificador común obliga a adivinar qué línea de un servicio corresponde a qué línea del otro. El identificador de correlación es lo que hace analizable el sistema.
- El identificador se genera en el gateway si el cliente no lo trae, y se propaga en la cabecera de toda llamada entre servicios.
- Los registros son parte de la superficie de exposición de datos. Un volcado de la petición completa en el registro filtra exactamente lo que EC-02 y EC-03 protegen.
- El escenario justifica que la observabilidad entre en el Sprint 4: antes no hay sistema desplegado que observar, y después ya hace falta para sostener las demostraciones.
