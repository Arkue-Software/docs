
# Informe de pruebas unitarias - Sprint 4

## Control del documento

| Campo | Información |
|---|---|
| Código del documento | TST-UNIT-04 |
| Versión | 0.1 |
| Estado | Borrador |
| Sprint o entrega | 4 |
| Responsable del informe | Tomás Silva |
| Revisado por | Pendiente |
| Aprobado por | Pendiente |
| Fecha de creación | Pendiente de confirmar |
| Última actualización | Pendiente de confirmar |
| Plan de pruebas relacionado | Pendiente de asociar |
| Versión o commit probado | Pendiente de identificar por componente |

## Historial de cambios

| Versión | Fecha | Descripción del cambio | Autor |
|---|---|---|---|
| 0.1 | 17/10/2026 | Creación inicial del informe con los resultados disponibles de Donación, Identidad y Campañas. | Tomás Silva |

---

## 1. Resumen ejecutivo

Durante el Sprint 4 se ejecutaron pruebas automatizadas de los componentes de Donación, Identidad y Campañas de RedVital.

Las ejecuciones registradas muestran resultados favorables en Donación e Identidad. En Campañas, 21 de las 24 pruebas finalizaron correctamente y tres fallaron durante la preparación de la base de datos utilizada por el entorno de pruebas.

Los resultados consolidados son:

- Donación: 181 pruebas aprobadas.
- Identidad: 20 pruebas aprobadas.
- Campañas: 21 pruebas aprobadas y 3 fallidas.
- Total: 225 pruebas registradas, 222 aprobadas y 3 fallidas.

El porcentaje global de aprobación de estas ejecuciones es del 98,67 %. Este indicador representa los resultados registrados y no equivale a una medición de cobertura total de requisitos.

La causa registrada para los tres fallos de Campañas está relacionada con la búsqueda de un archivo SQL en una ruta no disponible. Se requiere corregir o validar la preparación del entorno y volver a ejecutar las pruebas afectadas.

## 2. Información de ejecución

| Campo | Información |
|---|---|
| Periodo de ejecución | Octubre 16 a 17 de 2026|
| Ambiente utilizado | Pruebas / QA, según la ejecución |
| Datos utilizados | Datos sintéticos o dependencias de prueba, según corresponda |
| Responsable de ejecución | Tomás Silva |
| Componentes evaluados | Donación, Identidad y Campañas |
| Herramientas | Maven, JUnit 5, Mockito y .NET Test Platform |
| Plan de pruebas | Pendiente de asociar |

### 2.1 Alcance

Este informe documenta las ejecuciones automatizadas disponibles de los componentes mencionados.

No se asume que todas las pruebas sean unitarias en sentido estricto: las suites pueden incluir pruebas de componentes o de arquitectura. La clasificación definitiva debe contrastarse con el contenido de cada suite.

### 2.2 Evidencias

Las evidencias previstas para respaldar los resultados son:

- Comandos ejecutados.
- Resúmenes de las pruebas.
- Mensajes de error de las ejecuciones fallidas.
- Identificación de las suites utilizadas.
- Versión o commit correspondiente a cada componente, cuando se confirme.

## 3. Resumen de resultados

| Componente | Ejecutadas | Aprobadas | Fallidas | Omitidas |
|---|---:|---:|---:|---:|
| Donación | 181 | 181 | 0 | 0 |
| Identidad | 20 | 20 | 0 | 0 |
| Campañas | 24 | 21 | 3 | 0 |
| **Total** | **225** | **222** | **3** | **0** |

- Porcentaje de aprobación: 98,67 %.
- Porcentaje de fallos: 1,33 %.

Los porcentajes se calcularon sobre las 225 pruebas registradas. Los casos fallidos se contabilizan como fallos de ejecución, pero su causa debe analizarse antes de clasificarlos como defectos funcionales.

## 4. Resultados por caso de prueba

### 4.1 Donación: pruebas de transiciones, políticas y arquitectura

**Suites ejecutadas**

- `MotorTransicionesTest`
- `PoliticasTest`
- `ArquitecturaTest`

**Objetivo**

Verificar los comportamientos implementados en las suites de transiciones, políticas y arquitectura del componente de Donación.

**Procedimiento ejecutado**

1. Se abrió una terminal en el entorno de desarrollo o pruebas utilizado para ejecutar el proyecto.
2. Se ejecutó Maven seleccionando explícitamente las tres suites.
3. Se esperó a que finalizara el proceso.
4. Se revisó el resumen generado por Maven para identificar el total de pruebas, los fallos, los errores y los casos omitidos.
5. Se registró el resultado final de la ejecución.

**Comando registrado**

```bash
mvn -Dtest=MotorTransicionesTest,PoliticasTest,ArquitecturaTest test
```

**Resultado esperado**

Las suites seleccionadas deben terminar sin fallos ni errores, de acuerdo con las verificaciones implementadas.

**Resultado obtenido**

- Pruebas aprobadas: 181.
- Pruebas fallidas: 0.
- Errores: 0.
- Pruebas omitidas: 0.

**Veredicto:** Aprobado para las suites ejecutadas.

**Evidencia:** resumen de ejecución de Maven.

**Limitaciones**

El resultado no demuestra por sí solo que todos los requisitos funcionales y no funcionales de Donación estén cubiertos. La asociación con requisitos específicos está pendiente de consolidación.

### 4.2 Identidad: pruebas automatizadas

**Objetivo**

Verificar el comportamiento de los componentes de Identidad cubiertos por la suite automatizada ejecutada.

**Procedimiento ejecutado**

1. Se ejecutó la solución de pruebas de Identidad mediante la herramienta de pruebas de .NET.
2. Se utilizó la opción `--no-restore`, por lo que la ejecución dependió de que los paquetes necesarios ya estuvieran restaurados.
3. Se solicitó una salida detallada mediante `--logger "console;verbosity=normal"`.
4. Se revisó el resumen de ejecución para identificar las pruebas aprobadas y los posibles fallos.

**Comando registrado**

```bash
dotnet test services/identity/RedVital.Identidad.sln --no-restore --logger "console;verbosity=normal"
```

**Resultado esperado**

Las pruebas de la solución deben finalizar correctamente, sin fallos ni errores.

**Resultado obtenido**

- Pruebas totales: 20.
- Aprobadas: 20.
- Fallidas: 0.
- Omitidas: 0.

**Veredicto:** Aprobado para la ejecución registrada.

**Evidencia:** resumen de pruebas de .NET.

**Limitaciones**

No se dispone aquí del detalle individual de cada prueba ni de la correspondencia exacta entre los casos ejecutados y los requisitos. Estos datos deberán incorporarse si se recuperan de la salida original.

### 4.3 Campañas: pruebas automatizadas

**Objetivo**

Verificar los comportamientos cubiertos por la suite automatizada del componente de Campañas.

**Procedimiento ejecutado**

1. Se inició la ejecución de las pruebas automatizadas de Campañas.
2. La configuración de pruebas intentó preparar la base de datos mediante archivos SQL.
3. Durante la ejecución, tres pruebas fallaron al buscar un archivo de preparación en una ruta no disponible.
4. Se registró el resultado de la ejecución: 24 pruebas en total, de las cuales 21 fueron aprobadas y 3 fallaron.

**Ruta mencionada en el error registrado**

```text
/home/estudiante/Downloads/RedVital/BDs_RedVital/databases/campanas/00_roles.sql
```

**Resultado esperado**

La suite debe preparar sus dependencias y ejecutar las verificaciones previstas sin errores de configuración.

**Resultado obtenido**

- Pruebas totales: 24.
- Aprobadas: 21.
- Fallidas: 3.

**Veredicto:** Ejecución parcialmente aprobada; requiere corregir o validar la configuración de pruebas.

**Clasificación provisional**

La información disponible apunta a un problema de preparación del entorno o de resolución de rutas. No se considera confirmado un defecto funcional del producto por estos tres fallos.

**Acciones requeridas**

1. Revisar la ruta configurada en el mecanismo de preparación de la base de datos.
2. Confirmar que el archivo SQL exista y sea accesible desde el entorno de ejecución.
3. Comprobar que la configuración funcione desde la ubicación prevista del repositorio.
4. Volver a ejecutar la suite completa.
5. Comparar los resultados antes y después de corregir la configuración.

**Evidencia:** resumen de ejecución y ruta del archivo SQL reportada.

## 5. Defectos encontrados

| ID | Descripción | Severidad | Estado | Responsable | Enlace |
|---|---|---|---|---|---|
| Pendiente | Tres pruebas de Campañas fallaron durante la preparación de la base de datos debido a una ruta SQL no disponible. | Por evaluar | Pendiente de corrección y reejecución | Por asignar | No disponible |

La incidencia se mantiene separada de los defectos funcionales hasta que se determine su causa definitiva.

## 6. Riesgos y pendientes

| Elemento | Impacto | Acción requerida | Responsable |
|---|---|---|---|
| Tres pruebas de Campañas fallidas | Medio | Revisar la ruta SQL y repetir la ejecución. | Por asignar |
| Trazabilidad incompleta de las pruebas | Medio | Asociar las suites con requisitos y casos de prueba. | QA / equipo técnico |
| Versiones probadas sin consolidar | Bajo | Registrar el commit de cada componente. | QA |
| Evidencias originales no centralizadas | Bajo | Conservar los resúmenes de consola en la carpeta de evidencias. | QA |

## 7. Conclusión

Las pruebas registradas de Donación e Identidad finalizaron correctamente. La ejecución de Campañas obtuvo 21 resultados aprobados de 24, con tres fallos asociados provisionalmente a la preparación del entorno.

- **Recomendación:** mantener pendiente la validación completa de Campañas hasta corregir o explicar los tres fallos.
- **Riesgos aceptados:** ninguno formalmente documentado.
- **Acciones pendientes:** revisar la configuración SQL, repetir las pruebas afectadas y completar la trazabilidad de los resultados.

Los resultados de este informe corresponden a las ejecuciones registradas y no deben interpretarse como evidencia de cobertura completa del producto.

## 8. Revisión y aprobación

| Actividad | Persona | Fecha | Resultado |
|---|---|---|---|
| Revisión de resultados | Carlos | Pendiente | Pendiente |
| Aprobación final | Tomás Silva | Pendiente | Pendiente |

El documento permanece en borrador hasta completar la revisión de las evidencias y las aprobaciones correspondientes.
