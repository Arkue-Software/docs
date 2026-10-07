## EC-23 — Puesta en marcha del sistema completo

*Atributo de calidad:* Flexibilidad

*HU relacionadas:* HT-18 — arquitectura de infraestructura

*Subcaracterística:* Instalabilidad

*Prioridad:* Alta

### Escenario estímulo–respuesta

| Elemento | Descripción |
|---|---|
| Fuente del estímulo | Integrante nuevo del equipo, o el cliente al evaluar la entrega |
| Estímulo | Clona los repositorios en una máquina limpia y quiere levantar el sistema completo para verlo funcionar |
| Contexto | Máquina sin ninguna dependencia del proyecto instalada: sin Java, sin .NET, sin Node y sin PostgreSQL. Solo Docker |
| Artefacto afectado | Definición de contenedores y de la composición del sistema: dos servicios, gateway, aplicación web, dos bases de datos y las dos piezas de observabilidad |
| Respuesta | Un único comando levanta los ocho contenedores. El sistema queda operativo, con el esquema migrado y los datos sintéticos cargados, sin ningún paso manual adicional |
| Medida de respuesta | Un comando. Diez minutos o menos hasta que los ocho sondeos de salud responden correctamente. Cero pasos manuales fuera de los documentados. Funciona igual en Windows, macOS y Linux |

### Verificación

- Esta es la comprobación operativa de RD-03. Que cada componente tenga imagen propia no basta si levantar el conjunto exige conocimiento que solo tiene quien lo construyó.
- La prueba la ejecuta alguien que no participó en la configuración, siguiendo únicamente el README. Si necesita preguntar, el escenario no se cumple.
- Los datos sintéticos se cargan como parte del arranque, no como paso posterior, y son visiblemente ficticios.
- Que funcione en los tres sistemas operativos importa porque el equipo trabaja en máquinas distintas y el cliente evalúa en la suya.
