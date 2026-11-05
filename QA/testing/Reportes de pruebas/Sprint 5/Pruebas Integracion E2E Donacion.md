# Informe de pruebas - Servicio de Donación

## Control del documento

| Campo | Información |
|---|---|
| Código del documento | TST-DONACION |
| Versión | 0.1 |
| Estado | Borrador |
| Responsable del informe | Tomás Silva |
| Ejecutado por | Tomás Silva |
| Fecha de ejecución | Octubre de 2026 |
| Ambiente | Pruebas |
| Rama | release |

## Historial de cambios

| Versión | Fecha | Responsable | Descripción |
|---|---|---|---|
| 0.1 | 05/11/2026 | Tomás Silva | Consolidación de las pruebas de integración y flujo de donación ejecutadas. |

---

## 1. Resumen ejecutivo

Se realizaron pruebas sobre el servicio de Donación para verificar el procesamiento de solicitudes de donación y su integración con los demás componentes de RedVital.

Se documentaron dos escenarios principales: el flujo de registro de una intención de donación y posterior creación de una donación, y una ejecución anterior de integración a través del gateway. Los resultados registrados fueron satisfactorios, incluyendo respuestas HTTP exitosas y persistencia de información en el escenario correspondiente.

## 2. Información de ejecución

| Campo | Información |
|---|---|
| Componente | Donación |
| Tipo de prueba | Pruebas de integración y E2E |
| Alcance | Registro de intenciones, creación de donaciones e integración |
| Ambiente | Pruebas |
| Ejecutor | Tomás Silva |
| Dependencias | Gateway, servicio de Donación, base de datos y mensajería según el escenario |
| Tipo de dependencia | Real, dentro del ambiente de pruebas |

## 3. Ejecución de pruebas

### 3.1 Flujo de intención y creación de donación

**Identificador:** TST-E2E-02.

**Descripción:** Se ejecutó el flujo de una solicitud anónima mediante el endpoint de intenciones y posteriormente se realizó la solicitud de creación de una donación.

**Flujo evaluado:**

1. Envío de solicitud a `POST /v1/intenciones`.
2. Procesamiento de la intención por el servicio correspondiente.
3. Envío de solicitud a `POST /v1/donaciones`.
4. Generación del evento `donation.completed`.
5. Publicación del evento en el tópico `redvital.donacion.donation-completed.v1`.

**Resultado esperado:** El sistema debe permitir procesar el flujo de donación y generar el evento correspondiente al completarse la operación.

**Resultado obtenido:** El flujo fue ejecutado satisfactoriamente y se verificó la generación del evento de donación completada.

**Veredicto:** Aprobado.

### 3.2 Flujo de donación mediante gateway

**Identificador:** TC-020M.

**Fecha de ejecución:** 08/10/2026.

**Descripción:** Se ejecutó una prueba de integración del flujo de donación a través de Caddy y APISIX, verificando la respuesta del servicio y la persistencia de la información.

**Resultado esperado:** La solicitud debe atravesar el gateway, ser procesada por el servicio de Donación y generar la información correspondiente en la base de datos.

**Resultado obtenido:** Se obtuvo HTTP 201 y se verificó la persistencia de la donación y de tres unidades asociadas.

**Veredicto:** Aprobado.

## 4. Resumen de resultados

| ID | Prueba | Resultado obtenido | Estado |
|---|---|---|---|
| TST-E2E-02 | Intención y creación de donación | Flujo completado y evento `donation.completed` generado | Aprobado |
| TC-020M | Flujo de donación mediante Caddy y APISIX | HTTP 201 y persistencia de tres unidades | Aprobado |

**Total de escenarios registrados:** 2  
**Escenarios aprobados:** 2  
**Escenarios fallidos:** 0

## 5. Defectos encontrados

No se identificaron defectos durante las ejecuciones documentadas.

## 6. Conclusiones

El servicio de Donación presentó resultados satisfactorios en los escenarios de integración ejecutados. Se verificó el procesamiento de una intención y la posterior creación de una donación, así como la generación del evento de finalización. También se cuenta con una ejecución anterior satisfactoria del flujo mediante Caddy y APISIX, con respuesta HTTP 201 y persistencia de la información asociada.

## 7. Pendientes y observaciones

- Ampliar las pruebas con diferentes escenarios de creación de donaciones.
- Validar otros casos de error y rechazo de solicitudes.
- Verificar el comportamiento ante fallos de dependencias.
- Confirmar la versión del servicio asociada a cada ejecución antes de comparar resultados entre fechas.
- Los dos escenarios corresponden a ejecuciones diferentes y no deben interpretarse como una única corrida continua.

## 8. Evidencias

Se cuenta con las respuestas HTTP, los registros de ejecución y las verificaciones de persistencia disponibles para los escenarios documentados. Los identificadores internos de donaciones y demás datos sensibles deben redactarse antes de compartir las evidencias.

## 9. Revisión y aprobación

| Rol | Responsable | Estado |
|---|---|---|
| Responsable del informe | Tomás Silva | Elaborado |
| Revisado por | Pendiente | Pendiente |
| Aprobado por | Pendiente | Pendiente |