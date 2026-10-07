## EC-09 — Recuperación tras pérdida de datos

*Atributo de calidad:* Fiabilidad

*HU relacionadas:* HT-18 — arquitectura de infraestructura

*Subcaracterística:* Recuperabilidad

*Prioridad:* Alta

### Escenario estímulo–respuesta

| Elemento | Descripción |
|---|---|
| Fuente del estímulo | Fallo de infraestructura, error humano o corrupción del volumen de datos |
| Estímulo | Se pierde o se corrompe la base de datos de uno de los dos servicios |
| Contexto | Ambiente de QA desplegado, con datos sintéticos de referencia |
| Artefacto afectado | PostgreSQL de cada servicio, respaldos, migraciones de esquema |
| Respuesta | Se restaura el último respaldo disponible y se reaplican las migraciones pendientes. El servicio vuelve a operar con el esquema en la versión esperada, sin reconstruir nada a mano |
| Medida de respuesta | Tiempo de recuperación igual o menor a 4 horas. Pérdida máxima de datos de 24 horas. Cero diferencias entre la versión de esquema esperada y la obtenida tras restaurar. El procedimiento está documentado y se ejecutó al menos una vez por sprint desde el Sprint 3 |

### Verificación

- La restauración se ensaya de verdad. Un respaldo que nunca se ha restaurado no es un respaldo: es una suposición.
- El versionado del esquema por migraciones es lo que hace verificable la medida. Sin él, «volvió a la versión esperada» no se puede afirmar.
- La pérdida admitida de 24 horas es aceptable porque el ambiente opera con datos sintéticos. En un despliegue real del sistema, el requisito sería otro y debe declararse así ante el cliente.
- Cada servicio se recupera de forma independiente: son bases separadas, y restaurar una no obliga a tocar la otra.
