# Análisis financiero de RedVital

> **Documento de apoyo para la Mesa de Arquitectura**  
> **Complemento al Dossier de Propuesta**  
> **Versión:** 1.0

## 1. Contexto y alcance

Este documento complementa el dossier de propuesta de RedVital mediante tres preguntas: cómo funciona económicamente un banco de sangre, cómo podría sostenerse RedVital como software y qué oportunidades y riesgos financieros implica.

El análisis es cualitativo e ilustrativo. Sirve para sustentar una propuesta académica; no constituye un modelo financiero formal con cifras de mercado auditadas ni reemplaza el criterio de un asesor financiero si el proyecto se llevara a un contexto real.

## 2. El problema en cifras reales

La urgencia detrás de RedVital no es hipotética. Colombia enfrenta una alerta nacional del Instituto Nacional de Salud por niveles críticos de reservas de sangre, con una velocidad de uso de ciertos grupos sanguíneos superior a la capacidad de reposición de los bancos.

El país consolida cerca de un millón de donaciones anuales. Estas se concentran en 26 bancos de sangre de alta complejidad que reciben más de 12.000 donaciones al año y abastecen, en promedio, a 43 Instituciones Prestadoras de Salud.

## 3. Modelo de negocio de los bancos de sangre

Los bancos de sangre no venden la sangre. La donación es voluntaria y no remunerada, conforme al estándar ético y legal vigente. El ingreso proviene de una tarifa por unidad procesada, que cubre tamizaje de enfermedades infecciosas, almacenamiento, transporte y control de calidad, no el precio de la sangre donada.

La tarifa varía de acuerdo con los convenios entre instituciones y el número de unidades. Esto genera una dificultad para desarrollar redes entre bancos: el costo, determinado en gran parte por el transporte, puede tener mayor peso en la decisión que la cercanía geográfica o la oportunidad de la transferencia.

Una revisión histórica de 2006 mostró tarifas promedio entre $144.000 y $220.000 por unidad de glóbulos rojos, según se tratara de un banco público o privado. Aunque los valores absolutos están desactualizados, el patrón de tarifa de procesamiento variable continúa siendo relevante.

Desde 2025, las tarifas reguladas del sector salud pasaron de un esquema indexado al salario mínimo o a la Unidad de Valor Tributario a la Unidad de Valor Básico, actualizada anualmente según el Índice de Precios al Consumidor. Cualquier modelo de precios futuro debe considerar que estas tarifas se ajustan de forma regulada y periódica.

Los bancos de sangre están sujetos al control de calidad del INVIMA y a la política nacional de sangre del Ministerio de Salud, coordinada por el INS.

## 4. Modelo de sostenibilidad económica de RedVital

### 4.1 Fase académica

La fase académica puede operar con un costo prácticamente nulo mediante niveles gratuitos de hosting, notificaciones por correo o push, evitando SMS por su costo variable, y una API de mapas gratuita para el bajo volumen de un prototipo.

### 4.2 Continuidad después del curso

La opción más realista no es cobrar una cuota a los bancos de sangre desde el primer día, pues operan con márgenes ajustados por tarifas reguladas. Las alternativas más viables son:

1. **Patrocinio institucional:** adopción o patrocinio por parte de la Cruz Roja Colombiana o el INS, entidades que ya coordinan la red nacional de bancos de sangre.
2. **Financiamiento público:** acceso a financiación estatal o cooperación en salud pública por su alineación con la política nacional de sangre.
3. **Modelo freemium:** acceso gratuito para bancos pequeños y soporte pagado, como integraciones con sistemas legados, para instituciones grandes que lo requieran.

## 5. Estructura de costos estimada

| Rubro | Descripción | Costo en fase MVP |
|---|---|---|
| Hosting e infraestructura | Servidor y base de datos para el prototipo. | Nivel gratuito |
| Notificaciones | Correo y notificaciones push; se evita SMS por su costo variable. | Nivel gratuito |
| Servicio de mapas | Geolocalización entre donante, banco y solicitud. | Nivel gratuito para bajo volumen |
| Mantenimiento posterior al semestre | Dominio y hosting continuo si el proyecto sigue activo. | Bajo, pero no cero |

## 6. Oportunidades financieras

| Oportunidad | Detalle |
|---|---|
| Menor desperdicio de sangre | La vida útil de los glóbulos rojos es de aproximadamente 42 días. Coordinar inventario entre bancos puede reducir unidades vencidas sin usar y la pérdida económica asociada. |
| Menor gasto en campañas de emergencia | Movilizar donantes mediante redes sociales o llamadas implica costos de gestión. Usar primero el inventario disponible en la red puede reducir esa necesidad. |
| Alineación con política pública | La relación con la política nacional de sangre abre una vía de financiación estatal o de cooperación, no solo comercial. |
| Urgencia nacional vigente | Las alertas por reservas críticas hacen más relevante una solución de coordinación entre bancos y facilitan su justificación ante posibles patrocinadores. |

## 7. Riesgos financieros

| Riesgo | Detalle |
|---|---|
| Sostenibilidad posterior al semestre | Sin un dueño institucional después del curso, el software puede quedar abandonado. Es el riesgo financiero más alto y más fácil de ignorar por ser lejano. |
| Fricción de incentivos entre bancos | Si transferir sangre reduce ingresos para un banco, este podría resistirse a participar, sin importar la calidad técnica del sistema. |
| Costos variables de terceros | SMS y APIs de mapas cobran por uso; si la red crece, el costo escala con el volumen. |
| Cumplimiento regulatorio en un escenario real | INVIMA y la Ley de Habeas Data implicarían costos de auditoría y certificación que no existen en la fase de prototipo. |
| Dependencia de un sistema tarifario cambiante | La migración del sistema tarifario de UVT a UVB exige que cualquier modelo de precios futuro se indexe correctamente para evitar desactualización. |

## 8. Conclusión

RedVital responde a un problema con urgencia real y verificable, y su fase académica no requiere una inversión financiera significativa. El reto financiero principal no está en construir el prototipo, sino en sostenerlo después del semestre y diseñar reglas de incentivos que no entren en conflicto con el modelo tarifario de los bancos de sangre aliados.
