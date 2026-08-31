## EC-01 — Control de acceso por jurisdicción

*Atributo de calidad:* Seguridad

*HU relacionadas:* RF-09, RF-07 — M5, M4

*Subcaracterística:* Confidencialidad

*Prioridad:* Alta

### Escenario estímulo–respuesta

| Elemento | Descripción |
|---|---|
| Fuente del estímulo | Usuario territorial autenticado — coordinador territorial (U5), administrador de banco (U4) u operador (U3) |
| Estímulo | El usuario intenta consultar o modificar información de un banco, un territorio o una unidad que pertenece a una jurisdicción distinta de la suya |
| Contexto | Sistema en operación normal, usuario autenticado con token vigente |
| Artefacto afectado | API Gateway, servicio transversal de autorización, Servicio Institucional (jerarquía territorial), Servicio de Donación |
| Respuesta | El sistema resuelve la jurisdicción del usuario a partir del token, rechaza la solicitud, responde sin confirmar ni negar que el recurso exista, y deja el intento en la bitácora de auditoría |
| Medida de respuesta | El 100% de las solicitudes fuera de jurisdicción se rechazan. Ninguna respuesta de rechazo incluye datos del recurso solicitado ni permite deducir su existencia. El 100% de los intentos queda registrado con usuario, rol, recurso, marca de tiempo y resultado |

### Verificación

- La restricción alcanza a los usuarios **territoriales**. El administrador nacional (U6) tiene el territorio nacional como jurisdicción propia, de modo que una consulta agregada del país no es un acceso fuera de jurisdicción y no debe rechazarse.
- La interfaz tampoco insinúa la existencia de datos ajenos: no muestra totales nacionales, comparativos ni contadores de otras jurisdicciones a un usuario territorial.
- Se prueba con los siete perfiles contra la matriz de acceso del SRS, cubriendo tanto lectura como escritura.
- La autorización se evalúa del lado del servidor. Ocultar un elemento en la interfaz no cuenta como control cumplido.
