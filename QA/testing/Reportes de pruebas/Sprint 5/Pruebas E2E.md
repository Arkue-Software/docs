# Reporte de pruebas E2E – RedVital

**Código:** QA-API-001\
**Herramienta:** Playwright\
**Tipo de prueba:** Pruebas de humo (Smoke Testing)\
**Fecha de ejecución:** 5 de noviembre de 2026\
**Resultado general:** 3 pruebas exitosas de 3 ejecutadas.

---

## 1. Objetivo

Verificar la disponibilidad básica de los servicios de RedVital mediante pruebas automatizadas con Playwright, comprobando que sus endpoints de salud respondan correctamente mediante solicitudes HTTP.

## 2. Resultados de ejecución

Se ejecutaron tres pruebas automatizadas, utilizando un único trabajador (*worker*).

| Servicio | Prueba realizada  | Resultado |
| -------- | ----------------- | --------- |
| Donation | Endpoint de salud | Aprobada  |
| Campaign | Endpoint de salud | Aprobada  |
| Identity | Endpoint de salud | Aprobada  |

### Resumen de resultados

- **Pruebas ejecutadas:** 3
- **Pruebas aprobadas:** 3
- **Pruebas fallidas:** 0
- **Tasa de éxito:** 100 %
- **Tiempo total de ejecución:** 1,2 segundos
- **Workers utilizados:** 1

## 3. Evidencia de ejecución

Salida obtenida en la terminal:

```text
Running 3 tests using 1 worker

✓ 1 services.api.spec.js:31:5
  › API - donation
  › responde correctamente en el endpoint de salud (53ms)

✓ 2 services.api.spec.js:31:5
  › API - campaign
  › responde correctamente en el endpoint de salud (35ms)

✓ 3 services.api.spec.js:31:5
  › API - identity
  › responde correctamente en el endpoint de salud (34ms)

3 passed (1.2s)
```

## 4. Análisis de resultados

Los resultados muestran que los tres servicios evaluados respondieron satisfactoriamente a las solicitudes realizadas por Playwright.

Los tiempos individuales registrados fueron:

- Donation: 53 ms.
- Campaign: 35 ms.
- Identity: 34 ms.

El servicio Identity presentó el menor tiempo de respuesta, mientras que Donation registró el mayor.

No se presentaron errores durante la ejecución y todas las pruebas finalizaron correctamente.

## 5. Alcance y limitaciones

Es importante precisar que estas pruebas corresponden a verificaciones de disponibilidad (*Smoke Tests*).

Por lo tanto, los resultados permiten afirmar que los endpoints de salud de Donation, Campaign e Identity respondieron correctamente durante la ejecución.

Sin embargo, no demuestran por sí solos:

- El correcto funcionamiento de todas las operaciones de cada API.
- La autenticación y autorización de usuarios.
- La persistencia correcta de información.
- La comunicación entre microservicios mediante Kafka.
- El procesamiento de eventos y las notificaciones.
- El comportamiento bajo condiciones de carga.

Los servicios Institutional y Notifications, no aparecen en esta ejecución y no deben considerarse aprobados.

## 6. Conclusión

La ejecución de las pruebas automatizadas con Playwright fue satisfactoria. Los servicios Donation, Campaign e Identity superaron las verificaciones básicas de disponibilidad, alcanzando una tasa de éxito del 100 % en los casos ejecutados.

Como siguiente paso, se recomienda ampliar la suite de pruebas para validar operaciones funcionales, autenticación, autorización e integración entre servicios, especialmente los flujos que utilizan Kafka.

**Conclusión general:** Los tres servicios evaluados presentan disponibilidad satisfactoria en el momento de la prueba.
