# Informe de pruebas - Servicio de Campañas

## Control del documento

| Campo | Información |
|---|---|
| Código del documento | TST-CAMPAÑAS |
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
| 0.1 | 05/11/2026 | Tomás Silva | Registro de la prueba de publicación de campañas y propagación del evento hacia Donación. |

---

## 1. Resumen ejecutivo

Se realizó una prueba de integración sobre el servicio de Campañas para verificar que la publicación de una campaña generara el evento correspondiente y que este pudiera ser procesado por el componente de Donación.

El escenario permitió comprobar la comunicación entre el servicio de Campañas y el sistema de mensajería Kafka, así como la recepción del evento por parte del servicio de Donación.

## 2. Información de ejecución

| Campo | Información |
|---|---|
| Componente | Campañas |
| Tipo de prueba | Prueba de integración E2E |
| Alcance | Publicación de campaña y propagación de evento |
| Servicio | API de Campañas |
| Ambiente | Pruebas |
| Ejecutor | Tomás Silva |
| Dependencias | Kafka y servicio de Donación |
| Tipo de dependencia | Real, dentro del ambiente de pruebas |

## 3. Ejecución de pruebas

### 3.1 Publicación de campaña y propagación del evento

**Identificador:** TST-E2E-01.

**Descripción:** Se ejecutó el flujo de publicación de una campaña mediante la API del servicio de Campañas y se verificó la propagación del evento correspondiente hacia Kafka y el servicio de Donación.

**Flujo evaluado:**

1. Solicitud de publicación mediante la API de Campañas.
2. Procesamiento de la operación por el servicio de Campañas.
3. Publicación del evento `campaign.published`.
4. Envío del evento al tópico `redvital.campanias.campaign-published.v1`.
5. Recepción y procesamiento por parte del servicio de Donación.
6. Actualización del estado de la campaña en el contexto de Donación mediante `campania_publicada`.

**Resultado esperado:** La publicación de una campaña debe generar el evento correspondiente y permitir que el servicio de Donación reciba y procese la notificación.

**Resultado obtenido:** Se verificó la publicación del evento en Kafka y su procesamiento por parte del servicio de Donación, reflejándose la actualización correspondiente.

**Veredicto:** Aprobado.

## 4. Resumen de resultados

| ID | Prueba | Resultado obtenido | Estado |
|---|---|---|---|
| TST-E2E-01 | Publicación de campaña y propagación del evento | Evento publicado en Kafka y procesado por Donación | Aprobado |

**Total de escenarios registrados:** 1  
**Escenarios aprobados:** 1  
**Escenarios fallidos:** 0

## 5. Defectos encontrados

No se identificaron defectos durante la ejecución del escenario documentado.

## 6. Conclusiones

El servicio de Campañas presentó un comportamiento satisfactorio durante la prueba de integración ejecutada. Se verificó que la publicación de una campaña genera el evento esperado y que este puede propagarse mediante Kafka hasta el servicio de Donación, donde se procesa la notificación correspondiente.

El resultado confirma el funcionamiento del flujo evaluado entre los componentes involucrados.

## 7. Pendientes y observaciones

- Ampliar las pruebas con diferentes estados y escenarios de publicación.
- Verificar el comportamiento ante fallos de Kafka o del servicio consumidor.
- Evaluar el procesamiento de eventos repetidos, cuando corresponda.
- La prueba documentada cubre el flujo de publicación y propagación del evento; no representa una validación de todas las funcionalidades de Campañas.

## 8. Evidencias

Se cuenta con los resultados de la solicitud de publicación y con los registros de Kafka y Donación que permitieron verificar la propagación y el procesamiento del evento. Los identificadores internos deben redactarse cuando no sean necesarios para la evidencia.

## 9. Revisión y aprobación

| Rol | Responsable | Estado |
|---|---|---|
| Responsable del informe | Tomás Silva | Elaborado |
| Revisado por | Pendiente | Pendiente |
| Aprobado por | Pendiente | Pendiente |