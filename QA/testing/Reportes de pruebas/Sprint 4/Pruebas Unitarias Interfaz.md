# Informe de pruebas E2E de interfaz - Sprint 4

## Control del documento

| Campo | Información |
|---|---|
| Código del documento | TST-E2E-UI-04 |
| Versión | 0.1 |
| Estado | Borrador |
| Sprint o entrega | 4 |
| Responsable del informe | Tomás Silva |
| Revisado por | Pendiente |
| Aprobado por | Pendiente |
| Fecha de creación | 20/10/2026 |
| Última actualización | 20/10/2026 |
| Plan de pruebas relacionado | Pendiente de asociar |
| Versión o commit probado | Pendiente de identificar |

## Historial de cambios

| Versión | Fecha | Descripción del cambio | Autor |
|---|---|---|---|
| 0.1 | 20/10/2026 | Creación inicial del informe con los resultados disponibles de 40 pruebas automatizadas de interfaz de RedVital. | Tomás Silva |

---

## 1. Resumen ejecutivo

Durante el Sprint 4 se ejecutaron pruebas automatizadas de interfaz de usuario de RedVital mediante Playwright, utilizando el navegador Chromium y el entorno QA accesible desde `https://10.43.98.220`.

El objetivo fue comprobar comportamientos visibles de la aplicación relacionados con la navegación, el acceso a rutas protegidas, los formularios públicos, las validaciones de campos y las restricciones para continuar con información incompleta o inválida.

La ejecución conjunta registrada finalizó con los siguientes resultados:

- Pruebas ejecutadas: 40.
- Pruebas aprobadas: 40.
- Pruebas fallidas: 0.
- Pruebas omitidas: 0.
- Duración total: 26,8 segundos.

El porcentaje de aprobación de la ejecución consolidada fue del 100 %.

Este resultado indica que las verificaciones automatizadas incluidas en los 40 casos finalizaron satisfactoriamente en las condiciones de la ejecución registrada. No demuestra, por sí solo, que todos los requisitos de RedVital estén cubiertos ni que los servicios de backend, las bases de datos o las integraciones externas funcionen correctamente.

## 2. Información de ejecución

| Campo | Información |
|---|---|
| Periodo de ejecución | Octubre de 2026; fecha exacta según los registros disponibles: pendiente de confirmar |
| Ambiente utilizado | QA / Pruebas |
| Aplicación evaluada | RedVital |
| URL utilizada | `https://10.43.98.220` |
| Equipo de ejecución | NBIG26 |
| Sistema operativo | Ubuntu 22.04.5 |
| Herramienta de automatización | Playwright 1.64.1 |
| Entorno de ejecución | Node.js 22.23.0 |
| Navegador | Chromium |
| Responsable de ejecución | Tomás Silva |
| Total de casos funcionales | 40 |
| Plan de pruebas relacionado | Pendiente de asociar |
| Versión o commit | Pendiente de identificar |

### 2.1 Alcance

El alcance comprende pruebas automatizadas de interfaz de usuario que verifican:

- Visualización de formularios y elementos de navegación.
- Acceso directo a páginas públicas.
- Navegación mediante enlaces.
- Redirección al inicio de sesión desde rutas protegidas cuando no existe una sesión autenticada.
- Validación de campos de formularios.
- Restricciones de longitud y formato de datos.
- Habilitación y deshabilitación de botones según el estado de los formularios.
- Corrección de errores de validación.
- Comportamiento de navegación entre pasos de un formulario.
- Respuesta de la interfaz ante una ruta inexistente.

Las pruebas se ejecutaron contra la aplicación desplegada en el entorno QA. No se modificó el código de la aplicación para ejecutar estos casos.

No se realizaron envíos finales de formularios destinados a crear cuentas, registrar donaciones o generar otros registros reales.

### 2.2 Exclusiones y limitaciones

Las pruebas de este informe son pruebas automatizadas de interfaz con Playwright. Aunque algunas verifican recorridos de varios pasos dentro de la aplicación, no se consideran evidencia suficiente de una prueba E2E integral que abarque frontend, backend y base de datos.

No se ha confirmado mediante estos resultados:

- La persistencia de registros en la base de datos.
- La ejecución correcta de todas las operaciones de los servicios backend.
- La autorización efectiva en el servidor para cada rol.
- La integración completa con servicios externos.
- La compatibilidad con Firefox, WebKit u otros navegadores.
- La cobertura total de requisitos funcionales y no funcionales.
- El comportamiento de la aplicación bajo carga o concurrencia.

La configuración de las pruebas permite ignorar errores de validación del certificado HTTPS en el contexto de Playwright. Esto facilita el acceso al entorno de QA, pero no constituye una validación de la confianza o configuración del certificado.

### 2.3 Evidencias

Las evidencias disponibles para respaldar el resultado son:

- Salida de consola de la ejecución conjunta de los 40 casos.
- Salidas de las ejecuciones individuales de los archivos de pruebas.
- Archivos de prueba ubicados en `~/Downloads/RedVital/Pruebas_E2E_UI/tests/`.
- Resúmenes de Playwright con identificación de los casos y sus resultados.

La salida consolidada registra `40 passed (26.8s)`.

Queda pendiente centralizar las capturas de pantalla, trazas, reportes HTML y demás evidencias individuales que se consideren necesarias. No se afirma que todas estas evidencias estén almacenadas actualmente.

## 3. Resumen de resultados

| Indicador | Cantidad |
|---|---:|
| Pruebas ejecutadas | 40 |
| Aprobadas | 40 |
| Fallidas | 0 |
| Omitidas | 0 |
| Porcentaje de aprobación | 100 % |
| Porcentaje de fallos | 0 % |
| Duración de la ejecución conjunta | 26,8 segundos |

El porcentaje de aprobación se calculó mediante la siguiente expresión:

**Porcentaje de aprobación = (pruebas aprobadas / pruebas ejecutadas) × 100**

En esta ejecución:

**(40 / 40) × 100 = 100 %**

Los resultados corresponden a la ejecución conjunta registrada con Chromium. No representan una medición de cobertura total de requisitos ni permiten concluir que no existan defectos fuera del alcance evaluado.

## 4. Resultados por caso de prueba

### 4.1 Clasificación general

Los casos se agrupan por su objetivo principal. Una misma prueba puede verificar más de un comportamiento, pero se le asigna una categoría principal para facilitar la lectura del informe.

| Tipo principal | Casos | Cantidad | Objetivo |
|---|---|---:|---|
| Happy path y comportamiento válido | TST-UI-01, 03, 04, 07, 14, 15, 16, 20, 21, 22, 34, 36, 37, 38 | 14 | Comprobar que las acciones o los datos válidos permiten el comportamiento esperado. |
| Validaciones negativas y límites | TST-UI-02, 05, 06, 08, 09, 10, 11, 17, 18, 19, 23, 24, 40 | 13 | Comprobar que la interfaz restringe datos inválidos, incompletos o que no cumplen las condiciones requeridas. |
| Navegación y recorridos de interfaz | TST-UI-12, 35, 39 | 3 | Comprobar la navegación entre pasos, el tratamiento de rutas inexistentes y la consulta pública sin envío. |
| Control de acceso y rutas protegidas | TST-UI-13, 25, 26, 27, 28, 29, 30, 31, 32, 33 | 10 | Comprobar la redirección al inicio de sesión al acceder sin autenticación a rutas protegidas. |
| **Total** | **TST-UI-01 a TST-UI-40** | **40** | |

### 4.2 TST-UI-01 a TST-UI-08: formularios y validaciones iniciales

**Objetivo**

Verificar que los formularios de inicio de sesión, registro de donación anónima y creación de cuenta muestran sus elementos esperados y aplican las primeras restricciones de entrada.

**Casos ejecutados**

| ID | Qué busca comprobar | Tipo principal | Resultado |
|---|---|---|---|
| TST-UI-01 | El formulario de inicio de sesión muestra el encabezado y los enlaces esperados. | Interfaz / happy path | Aprobado |
| TST-UI-02 | No se permite avanzar en el registro anónimo sin seleccionar el grupo sanguíneo o indicar que se desconoce. | Validación negativa | Aprobado |
| TST-UI-03 | Seleccionar «No lo sé» permite avanzar al siguiente paso. | Happy path | Aprobado |
| TST-UI-04 | El municipio puede dejarse vacío durante el registro anónimo y se puede llegar al paso de confirmación sin finalizarlo. | Happy path / campo opcional | Aprobado |
| TST-UI-05 | El botón de consulta requiere un código con la longitud mínima establecida. | Validación de longitud | Aprobado |
| TST-UI-06 | Un documento con longitud inferior a la permitida muestra la validación correspondiente. | Validación negativa | Aprobado |
| TST-UI-07 | Un correo con formato inválido muestra un mensaje de validación. | Validación negativa | Aprobado |
| TST-UI-08 | Una contraseña que no cumple los requisitos muestra la validación correspondiente. | Validación negativa | Aprobado |

**Procedimiento general**

1. Se abrió la ruta correspondiente en el navegador Chromium.
2. Se verificó la presencia de los elementos de interfaz.
3. Se introdujeron los datos de prueba necesarios para cada caso.
4. Se comprobó el estado de los botones, mensajes o pasos de navegación.
5. Se comparó el comportamiento observado con las expectativas del caso.

**Resultado obtenido**

Los ocho casos finalizaron correctamente.

**Veredicto:** Aprobado para los comportamientos de interfaz evaluados.

### 4.3 TST-UI-09 a TST-UI-12: validaciones adicionales y navegación entre pasos

**Objetivo**

Comprobar la validación de la confirmación de contraseña, las restricciones del documento, los campos iniciales obligatorios y la posibilidad de regresar al paso anterior de un formulario.

| ID | Qué busca comprobar | Tipo principal | Resultado |
|---|---|---|---|
| TST-UI-09 | Se detecta una contraseña de confirmación que no coincide con la contraseña principal. | Validación negativa | Aprobado |
| TST-UI-10 | Un documento que contiene espacios no cumple la validación establecida. | Validación negativa | Aprobado |
| TST-UI-11 | No se puede continuar con el registro cuando faltan los datos iniciales requeridos. | Campos obligatorios | Aprobado |
| TST-UI-12 | Es posible regresar al paso anterior del registro anónimo. | Navegación | Aprobado |

**Resultado obtenido**

Los cuatro casos finalizaron correctamente.

**Veredicto:** Aprobado para los comportamientos evaluados.

### 4.4 TST-UI-13 a TST-UI-16: navegación y acceso inicial

**Objetivo**

Verificar la redirección desde una ruta protegida sin sesión y el funcionamiento de los enlaces principales de navegación.

| ID | Qué busca comprobar | Tipo principal | Resultado |
|---|---|---|---|
| TST-UI-13 | Una ruta protegida redirige al inicio de sesión cuando no hay una sesión autenticada. | Control de acceso | Aprobado |
| TST-UI-14 | El enlace «Crea tu cuenta» abre el formulario de registro. | Navegación / happy path | Aprobado |
| TST-UI-15 | El enlace de registro anónimo abre el flujo de donación anónima. | Navegación / happy path | Aprobado |
| TST-UI-16 | El enlace «Volver al inicio» abre la página principal. | Navegación / happy path | Aprobado |

**Resultado obtenido**

Los cuatro casos finalizaron correctamente.

**Veredicto:** Aprobado para los recorridos de navegación y el comportamiento de acceso comprobados.

### 4.5 TST-UI-17 a TST-UI-24: validaciones de campos y recuperación ante errores

**Objetivo**

Comprobar límites de longitud, restricciones de formato, validación de fechas, aceptación de datos válidos y actualización de mensajes cuando se corrigen los campos.

| ID | Qué busca comprobar | Tipo principal | Resultado |
|---|---|---|---|
| TST-UI-17 | El campo de documento limita la longitud máxima a 20 caracteres. | Límite de entrada | Aprobado |
| TST-UI-18 | Un documento con signos no supera la validación de formato. | Validación negativa | Aprobado |
| TST-UI-19 | Una fecha de nacimiento futura impide continuar con el registro. | Validación negativa | Aprobado |
| TST-UI-20 | Un correo con formato válido supera la validación correspondiente. | Happy path / validación positiva | Aprobado |
| TST-UI-21 | Una contraseña que cumple los requisitos deja de mostrar el error de contraseña inválida. | Recuperación ante error | Aprobado |
| TST-UI-22 | Corregir la confirmación de contraseña elimina el mensaje de discrepancia. | Recuperación ante error | Aprobado |
| TST-UI-23 | Los espacios adicionales no completan la longitud mínima requerida para el código de consulta. | Validación de longitud | Aprobado |
| TST-UI-24 | El botón de consulta permanece deshabilitado cuando el código está vacío. | Campo obligatorio | Aprobado |

**Resultado obtenido**

Los ocho casos finalizaron correctamente.

**Veredicto:** Aprobado para las restricciones y respuestas de interfaz verificadas.

### 4.6 TST-UI-25 a TST-UI-33: acceso a rutas protegidas

**Objetivo**

Verificar que el acceso directo a distintas rutas protegidas, sin una sesión autenticada, redirige al inicio de sesión.

**Procedimiento ejecutado**

1. Se abrió directamente cada ruta desde un navegador sin una sesión autenticada.
2. Se comprobó la URL final.
3. Se verificó la visualización del encabezado «Iniciar sesión».
4. Se registró el resultado de cada caso.

| ID | Ruta evaluada | Qué busca comprobar | Resultado |
|---|---|---|---|
| TST-UI-25 | `/donantes/historial` | El historial del donante requiere iniciar sesión. | Aprobado |
| TST-UI-26 | `/donantes/reconocimientos` | Los reconocimientos del donante requieren iniciar sesión. | Aprobado |
| TST-UI-27 | `/donantes/consulta` | La consulta de donantes requiere iniciar sesión. | Aprobado |
| TST-UI-28 | `/ciclovida/registro-donacion` | El registro de donación requiere iniciar sesión. | Aprobado |
| TST-UI-29 | `/ciclovida/unidades` | La consulta de unidades requiere iniciar sesión. | Aprobado |
| TST-UI-30 | `/inventario` | El inventario requiere iniciar sesión. | Aprobado |
| TST-UI-31 | `/inventario/alertas` | Las alertas de inventario requieren iniciar sesión. | Aprobado |
| TST-UI-32 | `/campanias` | La sección de campañas requiere iniciar sesión. | Aprobado |
| TST-UI-33 | `/sin-modulos` | La ruta protegida redirige al inicio de sesión sin autenticación. | Aprobado |

**Resultado obtenido**

Las nueve rutas evaluadas redirigieron al inicio de sesión según las verificaciones automatizadas.

**Veredicto:** Aprobado para la redirección de interfaz comprobada.

**Limitación**

Estos resultados no prueban por sí solos que los servicios backend apliquen correctamente los permisos o roles. Para esa conclusión se requieren pruebas específicas de autorización en los servicios.

### 4.7 TST-UI-34 a TST-UI-40: rutas públicas y acceso directo

**Objetivo**

Comprobar la disponibilidad de las páginas públicas principales, el tratamiento de una ruta inexistente y algunas restricciones iniciales de formularios.

| ID | Qué busca comprobar | Tipo principal | Resultado |
|---|---|---|---|
| TST-UI-34 | La página principal se abre y contiene el elemento raíz de la aplicación. | Happy path | Aprobado |
| TST-UI-35 | Una ruta inexistente muestra el mensaje esperado para una página no encontrada. | Manejo de ruta inexistente | Aprobado |
| TST-UI-36 | Se puede acceder directamente al formulario de inicio de sesión. | Navegación / happy path | Aprobado |
| TST-UI-37 | Se puede acceder directamente al registro y se muestran los campos iniciales. | Navegación / happy path | Aprobado |
| TST-UI-38 | Se puede acceder directamente al registro de donación anónima. | Navegación / happy path | Aprobado |
| TST-UI-39 | Se puede abrir la consulta de donación y el botón permanece deshabilitado con el código vacío. | Navegación / validación | Aprobado |
| TST-UI-40 | El registro no permite avanzar cuando los datos iniciales están vacíos. | Validación negativa | Aprobado |

**Resultado obtenido**

Los siete casos finalizaron correctamente.

**Veredicto:** Aprobado para los comportamientos de navegación y validación de interfaz comprobados.

## 5. Defectos encontrados

En la ejecución conjunta registrada no se presentaron casos fallidos.

| ID | Descripción | Severidad | Estado | Responsable | Enlace |
|---|---|---|---|---|---|
| No aplica | No se registraron fallos en los 40 casos ejecutados. | No aplica | Sin fallos registrados en esta ejecución | No aplica | No aplica |

La ausencia de fallos en esta ejecución no implica que la aplicación esté libre de defectos. Los resultados se limitan a los comportamientos y condiciones cubiertos por los casos descritos.

## 6. Riesgos y pendientes

| Elemento | Impacto | Acción requerida | Responsable |
|---|---|---|---|
| Trazabilidad de casos frente a requisitos | Medio | Asociar los 40 casos con requisitos funcionales y criterios de aceptación. | QA / equipo técnico |
| Versión o commit probado sin identificar | Medio | Registrar la versión exacta de la aplicación desplegada durante la ejecución. | QA / equipo técnico |
| Evidencias individuales no centralizadas | Bajo | Guardar las salidas de consola, el reporte HTML y las evidencias que se requieran. | QA |
| Cobertura limitada a Chromium | Medio | Evaluar si el plan de pruebas requiere ejecución en otros navegadores. | QA |
| Autorización de backend no comprobada por estos casos | Medio | Complementar con pruebas específicas de autorización en los servicios. | QA / equipo técnico |
| Operaciones finales de creación no ejecutadas | Bajo | Planificar pruebas con datos controlados si se requiere validar el envío y persistencia de registros. | QA / equipo técnico |

Las acciones anteriores son pendientes de mejora y ampliación del alcance. No representan defectos confirmados por la ejecución actual.

## 7. Conclusión

La ejecución conjunta de las 40 pruebas automatizadas de interfaz de RedVital finalizó satisfactoriamente, con 40 casos aprobados, 0 fallidos y una duración de 26,8 segundos.

Las verificaciones cubrieron formularios públicos, validaciones de datos, navegación entre páginas, rutas inexistentes y redirecciones desde rutas protegidas cuando no existe una sesión autenticada.

Los resultados constituyen evidencia favorable para los comportamientos de interfaz evaluados en el entorno QA y en el navegador Chromium utilizado.

**Recomendaciones**

- Mantener el resultado como aprobado para el alcance de interfaz ejecutado.
- Asociar los casos con requisitos y criterios de aceptación.
- Centralizar las evidencias de ejecución.
- Identificar la versión o commit de la aplicación evaluada.
- Complementar la validación con pruebas de servicios, autorización y persistencia cuando estén dentro del alcance del plan de pruebas.
- No interpretar el 100 % de aprobación como cobertura total de RedVital.

El informe permanece en borrador hasta completar la revisión de evidencias, la identificación de la versión probada y las aprobaciones correspondientes.

## 8. Revisión y aprobación

| Actividad | Persona | Fecha | Resultado |
|---|---|---|---|
| Revisión de resultados | Pendiente de confirmar | Pendiente | Pendiente |
| Aprobación final | Pendiente de confirmar | Pendiente | Pendiente |

El documento debe actualizarse cuando se completen la revisión, la trazabilidad de requisitos y las evidencias definitivas.