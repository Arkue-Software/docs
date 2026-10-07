## EC-16 — Concurrencia durante el pico de una campaña

*Atributo de calidad:* Eficiencia de desempeño

*HU relacionadas:* RF-08, RF-01, RF-03 — M1, M2, M3

*Subcaracterística:* Capacidad

*Prioridad:* Media

### Escenario estímulo–respuesta

| Elemento | Descripción |
|---|---|
| Fuente del estímulo | Donantes y operadores de banco de forma concurrente |
| Estímulo | Un grupo de usuarios consulta campañas, se registra y registra donaciones al mismo tiempo, durante el pico de una campaña publicada |
| Contexto | Campaña activa, ambiente de QA con ambos servicios desplegados |
| Artefacto afectado | API Gateway, Servicio Institucional, Servicio de Donación, ambas bases de datos |
| Respuesta | El sistema atiende la carga sin perder peticiones. Si la carga excede su capacidad, degrada de forma controlada rechazando con un código explícito, en lugar de agotar recursos y fallar de manera imprevisible |
| Medida de respuesta | 50 usuarios concurrentes durante 10 minutos con tasa de error inferior al 1% y percentil 95 igual o menor a 3 segundos. El uso de CPU y memoria de cada contenedor se mantiene por debajo del 80% de su límite asignado. Cero peticiones perdidas sin respuesta |

### Verificación

- La cifra de 50 usuarios concurrentes corresponde a la escala de demostración del proyecto, no a la del sistema nacional real. Se declara como tal ante el cliente y se ajusta si el cliente fija otra.
- La contingencia declarada, si la medida no se alcanza, es levantar una segunda instancia del Servicio de Donación detrás del gateway sin cambiar código. Que eso sea posible es una consecuencia directa de la arquitectura distribuida y conviene demostrarlo aunque no haga falta.
- Perder una petición en silencio es peor que rechazarla: el donante cree haberse registrado y no lo está.
- La prueba de carga se ejecuta contra datos sintéticos y nunca contra el ambiente que se usa para la demostración al cliente.
