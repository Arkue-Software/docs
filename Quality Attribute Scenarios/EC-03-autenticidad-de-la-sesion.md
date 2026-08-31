## EC-03 — Autenticidad de la sesión

*Atributo de calidad:* Seguridad

*HU relacionadas:* RF-09 — servicio transversal de autenticación y autorización

*Subcaracterística:* Autenticidad

*Prioridad:* Alta

### Escenario estímulo–respuesta

| Elemento | Descripción |
|---|---|
| Fuente del estímulo | Cliente cualquiera: la aplicación web, una herramienta externa o un agente no identificado |
| Estímulo | Envía una petición con el token ausente, expirado, alterado en su contenido o firmado por un emisor que el sistema no reconoce |
| Contexto | Sistema en operación normal, endpoint que requiere sesión |
| Artefacto afectado | API Gateway, emisor del token en el Servicio Institucional, verificación en el Servicio de Donación |
| Respuesta | El gateway rechaza la petición antes de enrutarla. La lógica de negocio no se ejecuta y no se consulta la base de datos. El servicio destino vuelve a verificar el token por su cuenta y no confía en que el gateway ya lo hizo |
| Medida de respuesta | Los cuatro casos —ausente, expirado, alterado, emisor desconocido— se rechazan en el 100% de los intentos. Ninguna petición sin token válido alcanza un servicio, verificado en la traza de los registros. La vigencia del token no supera los 60 minutos |

### Verificación

- El emisor del token es el Servicio Institucional, que es el dueño de los usuarios y de la jerarquía territorial. Situar la identidad donde ya vive el dato territorial evita una llamada entre servicios en cada verificación de jurisdicción.
- La doble verificación —gateway y servicio— es deliberada: un servicio alcanzable por otra ruta no queda desprotegido.
- El token transporta el rol y la jurisdicción, que son la entrada de EC-01.
- Se prueba invocando un endpoint interno saltando el gateway: debe rechazar igual.
