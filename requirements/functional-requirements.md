# Requerimientos Funcionales — Red Vital

## 1. Propósito

Este documento consolida los **requerimientos funcionales vigentes de Red Vital**.

Su objetivo es mantener una fuente de consulta clara y navegable para las capacidades funcionales que debe proporcionar el sistema.

Los requerimientos aquí documentados provienen del SRS vigente y deben mantenerse sincronizados con:

- historias de usuario;
- backlog;
- módulos funcionales;
- contratos;
- diseño;
- arquitectura;
- casos de prueba.

---

# 2. Convención de redacción

Los requerimientos funcionales utilizan sintaxis controlada **EARS** para reducir ambigüedad y facilitar su verificación.

Los patrones utilizados son:

| Patrón | Forma |
|---|---|
| Ubicuo | El sistema deberá `<respuesta>`. |
| Dirigido por evento | Cuando `<disparador>`, el sistema deberá `<respuesta>`. |
| Dirigido por estado | Mientras `<estado>`, el sistema deberá `<respuesta>`. |
| Comportamiento no deseado | Si `<condición>`, entonces el sistema deberá `<respuesta>`. |
| Complejo | Combinación de estado, disparador y respuesta. |

Cada requerimiento debe expresar una capacidad verificable y mantener el verbo modal **deberá**.

---

# 3. Prioridad

Los requerimientos utilizan priorización MoSCoW:

| Nivel | Significado |
|---|---|
| Must | Obligatorio para la línea base. |
| Should | Importante, pero no indispensable para la primera entrega. |
| Could | Deseable si existe capacidad. |
| Won't | Fuera de la línea base actual. |

---

# 4. Módulos relacionados

Los requerimientos se distribuyen entre los siguientes módulos y servicios transversales:

| ID | Área |
|---|---|
| M1 | Gestión del donante |
| M2 | Campañas |
| M3 | Trazabilidad |
| M4 | Inventario |
| M5 | Coordinación y transferencias |
| M6 | Administración institucional y territorial |
| M7 | Analítica e indicadores |
| ST1 | Notificaciones |
| ST2 | Auditoría |
| ST3 | Identidad y sesión |

---

# 5. Catálogo de requerimientos funcionales

## RF-01 — Registro voluntario de donante

**Módulo:** M1  
**Prioridad:** Must  

Cuando una persona solicite registrarse como donante, el sistema deberá ofrecer:

- una modalidad anónima sin campos obligatorios de identificación personal;
- una modalidad con perfil completo.

---

## RF-02 — Cálculo de elegibilidad

**Módulo:** M1  
**Prioridad:** Must  

El sistema deberá calcular el estado de elegibilidad de cada donante registrado a partir de:

- su tipo de sangre;
- la fecha de su última donación aceptada.

---

## RF-03 — Registro de donación

**Módulo:** M3  
**Prioridad:** Must  

Cuando un operador de banco registre una donación efectuada, el sistema deberá:

- asociarla al donante correspondiente;
- asociarla al banco correspondiente;
- asociarla a la campaña correspondiente;
- generar la unidad resultante.

---

## RF-04 — Trazabilidad de origen

**Módulo:** M3  
**Prioridad:** Must  

El sistema deberá conservar para cada unidad:

- origen;
- fecha de captación;
- secuencia completa de estados;
- autor de cada cambio;
- marca temporal de cada cambio.

---

## RF-05 — Marcado de unidad no apta

**Módulo:** M3  
**Prioridad:** Must  

Cuando el veredicto de tamizaje de una unidad sea no apto, el sistema deberá marcarla como no apta.

El sistema no deberá registrar la causa clínica en ningún campo del modelo de datos.

---

## RF-06 — Protocolo de disposición final

**Módulo:** M3  
**Prioridad:** Must  

Mientras una unidad esté marcada como:

- no apta; o
- vencida;

el sistema deberá:

- mantenerla dentro del flujo de disposición final;
- bloquear su despacho;
- mantener el bloqueo hasta que se registre su desecho.

---

## RF-07 — Consulta de existencias

**Módulo:** M4  
**Prioridad:** Must  

El sistema deberá presentar al usuario las existencias de unidades disponibles según:

- componente sanguíneo;
- tipo de sangre;
- alcance de datos autorizado para el usuario.

---

## RF-08 — Publicación de campañas

**Módulo:** M2  
**Prioridad:** Must  

Cuando un administrador de banco publique una campaña, el sistema deberá hacerla visible a los donantes cuya jurisdicción coincida con la sede.

La campaña deberá presentar:

- fecha;
- sede;
- cupo disponible.

---

## RF-09 — Administración de la jerarquía territorial

**Módulo:** M6  
**Prioridad:** Must  

El sistema deberá mantener:

- jerarquía territorial nacional;
- jerarquía departamental;
- jerarquía municipal;
- instituciones adscritas a cada nivel.

---

## RF-10 — Transferencia entre bancos

**Módulo:** M5  
**Prioridad:** Should  

Cuando:

1. un banco registre déficit de un componente sanguíneo; y
2. otro banco disponga de excedente compatible;

el sistema deberá:

- sugerir una transferencia;
- someterla a aprobación del administrador del banco cedente.

---

## RF-11 — Reconocimiento no monetario

**Módulo:** M1  
**Prioridad:** Should  

Cuando un donante registrado complete una donación aceptada, el sistema deberá:

- actualizar sus insignias;
- actualizar su nivel de progresión.

El sistema no deberá otorgar contraprestación económica por este reconocimiento.

---

## RF-12 — Notificación al donante

**Servicio transversal:** ST1  
**Prioridad:** Should  

El sistema deberá enviar una notificación al donante cuando ocurra alguno de los siguientes eventos:

- recupere su elegibilidad;
- se publique una campaña en su jurisdicción;
- alcance un nuevo reconocimiento.

---

## RF-13 — Tablero de indicadores

**Módulo:** M7  
**Prioridad:** Should  

El sistema deberá presentar indicadores agregados según la jurisdicción del usuario sobre:

- captación;
- vencimiento;
- unidades no aptas;
- cobertura territorial.

---

## RF-14 — Movilización de donantes

**Módulo:** M5  
**Prioridad:** Could  

Si la red de bancos no dispone de excedente compatible para cubrir un déficit registrado, entonces el sistema deberá convocar a los donantes:

- compatibles;
- elegibles;
- pertenecientes a la jurisdicción afectada.

---

## RF-15 — Reserva de cupo con expiración

**Módulo:** M2  
**Prioridad:** Could  

Mientras una reserva de cupo esté vigente, cuando transcurra el plazo de expiración sin confirmación del donante, el sistema deberá:

- liberar el cupo;
- ponerlo nuevamente a disposición.

---

# 6. RF-16 — Identificador retirado

El identificador `RF-16` no pertenece a la línea base funcional vigente.

En una versión anterior estaba relacionado con la extensión hacia otros tipos de donación.

Al no describir una capacidad funcional ejecutable, su contenido fue trasladado al tratamiento de requisitos no funcionales y restricciones.

Por esta razón, la numeración continúa desde `RF-17`.

---

# 7. Continuación del catálogo

## RF-17 — Consulta de historial propio

**Módulo:** M1  
**Prioridad:** Must  

El sistema deberá presentar a cada donante registrado:

- su propio historial de donaciones;
- su estado de elegibilidad vigente.

---

## RF-18 — Exclusión de unidades vencidas

**Módulo:** M4  
**Prioridad:** Must  

Cuando una unidad supere su fecha de vencimiento, el sistema deberá:

- retirarla del inventario disponible;
- derivarla automáticamente al flujo de disposición final.

La operación no deberá requerir intervención manual.

---

## RF-19 — Alerta de escasez

**Módulo:** M4  
**Prioridad:** Must  

Cuando las existencias de una combinación de:

- componente sanguíneo;
- tipo de sangre;

desciendan por debajo del umbral configurado por la institución, el sistema deberá generar una alerta de escasez dirigida al administrador del banco.

---

## RF-20 — Alerta de vencimiento próximo

**Módulo:** M4  
**Prioridad:** Must  

Cuando una unidad alcance el umbral de días previos a su vencimiento configurado por la institución, el sistema deberá generar una alerta de vencimiento próximo.

---

## RF-21 — Consulta de bitácora

**Servicio transversal:** ST2  
**Prioridad:** Must  

El sistema deberá presentar al auditor la bitácora de auditoría en modo de solo lectura.

El sistema no deberá ofrecer al auditor operaciones que permitan modificar la bitácora.

---

## RF-22 — Asignación de jurisdicción de usuario

**Módulo:** M6  
**Prioridad:** Must  

Cuando un administrador nacional asigne o modifique la jurisdicción de un usuario territorial, el sistema deberá:

1. registrar el ámbito asignado;
2. aplicar el nuevo ámbito a partir del siguiente token de acceso emitido para ese usuario;
3. registrar el cambio en la bitácora de auditoría.

---

## RF-23 — Inicio de sesión

**Servicio transversal:** ST3  
**Prioridad:** Must  

Cuando un usuario registrado presente credenciales válidas, el sistema deberá:

- iniciar una sesión;
- establecer una vigencia máxima de ocho horas para la sesión;
- emitir un token de acceso;
- incluir en el token el rol del usuario;
- incluir en el token su jurisdicción.

Este requerimiento aplica a los usuarios registrados y no al donante anónimo.

---

## RF-24 — Término de sesión

**Servicio transversal:** ST3  
**Prioridad:** Must  

Cuando:

- un usuario cierre su sesión; o
- la sesión alcance su vigencia máxima;

el sistema deberá:

- revocar la sesión;
- exigir nuevamente las credenciales para toda operación que requiera sesión.

---

# 8. Reglas complementarias

## 8.1 Umbrales de inventario

Los umbrales utilizados por:

- RF-19 — alerta de escasez;
- RF-20 — alerta de vencimiento próximo;

son configurables por institución.

---

## 8.2 Sesiones

Para RF-23 y RF-24:

- la vigencia máxima de sesión es de ocho horas;
- el token de acceso tiene una vigencia independiente definida por la arquitectura de identidad;
- las credenciales incorrectas no deben generar una respuesta que revele qué dato específico fue inválido.

---

## 8.3 Donante anónimo

El donante anónimo:

- no inicia sesión;
- no recibe acceso a módulos internos;
- mantiene únicamente las capacidades explícitamente permitidas para su perfil.

---

# 9. Resumen de requerimientos

| ID | Requerimiento | Área | Prioridad |
|---|---|---|---|
| RF-01 | Registro voluntario de donante | M1 | Must |
| RF-02 | Cálculo de elegibilidad | M1 | Must |
| RF-03 | Registro de donación | M3 | Must |
| RF-04 | Trazabilidad de origen | M3 | Must |
| RF-05 | Marcado de unidad no apta | M3 | Must |
| RF-06 | Protocolo de disposición final | M3 | Must |
| RF-07 | Consulta de existencias | M4 | Must |
| RF-08 | Publicación de campañas | M2 | Must |
| RF-09 | Administración territorial | M6 | Must |
| RF-10 | Transferencia entre bancos | M5 | Should |
| RF-11 | Reconocimiento no monetario | M1 | Should |
| RF-12 | Notificación al donante | ST1 | Should |
| RF-13 | Tablero de indicadores | M7 | Should |
| RF-14 | Movilización de donantes | M5 | Could |
| RF-15 | Reserva con expiración | M2 | Could |
| RF-17 | Consulta de historial propio | M1 | Must |
| RF-18 | Exclusión de unidades vencidas | M4 | Must |
| RF-19 | Alerta de escasez | M4 | Must |
| RF-20 | Alerta de vencimiento próximo | M4 | Must |
| RF-21 | Consulta de bitácora | ST2 | Must |
| RF-22 | Asignación de jurisdicción | M6 | Must |
| RF-23 | Inicio de sesión | ST3 | Must |
| RF-24 | Término de sesión | ST3 | Must |

---

# 10. Distribución por prioridad

La línea base funcional contiene:

- **Must:** funcionalidades indispensables;
- **Should:** funcionalidades importantes;
- **Could:** funcionalidades sujetas a capacidad y prioridad del proyecto.

Las prioridades deben mantenerse sincronizadas con el backlog vigente.

---

# 11. Trazabilidad

Cada requerimiento funcional debe poder relacionarse con:

    Requerimiento funcional
              ↓
        Historia de usuario
              ↓
          Tarea / Issue
              ↓
        Implementación
              ↓
         Caso de prueba
              ↓
           Evidencia

La matriz de trazabilidad debe utilizar los mismos identificadores `RF-*`.

---

# 12. Relación con Testing

Los requerimientos funcionales constituyen una de las principales fuentes para el diseño de pruebas.

Cada RF implementado debe contar con uno o más casos de prueba que validen:

- flujo positivo;
- condiciones negativas relevantes;
- permisos;
- resultado esperado.

Consultar:

[Test Design](../testing/test-design.md)

---

# 13. Relación con requisitos no funcionales

Los requerimientos de este documento especifican **qué capacidades debe proporcionar el sistema**.

Las condiciones relacionadas con:

- rendimiento;
- disponibilidad;
- seguridad;
- mantenibilidad;
- confiabilidad;
- extensibilidad;

se documentan separadamente en:

[Non-Functional Requirements](./non-functional-requirements.md)

---

# 14. Relación con seguridad

Los requisitos específicamente relacionados con:

- autenticación;
- autorización;
- control de acceso;
- protección de datos;
- sesiones;
- auditoría;

se complementan con:

[Security Requirements](./security-requirements.md)

---

# 15. Fuente de verdad

Este documento funciona como vista especializada de los requerimientos funcionales dentro de la wiki.

Debe permanecer sincronizado con el SRS.

Ante una modificación funcional:

1. debe actualizarse el requerimiento correspondiente;
2. debe revisarse el backlog;
3. debe revisarse la trazabilidad;
4. debe revisarse el diseño de pruebas;
5. debe revisarse cualquier contrato afectado.

---

# 16. Regla de mantenimiento

Un requerimiento funcional no debe modificarse únicamente para ajustarse a una implementación existente.

Cuando cambie una necesidad funcional debe evaluarse su impacto sobre:

- SRS;
- backlog;
- arquitectura;
- diseño;
- datos;
- integración;
- testing.

Los identificadores existentes deben conservarse para mantener la trazabilidad histórica.