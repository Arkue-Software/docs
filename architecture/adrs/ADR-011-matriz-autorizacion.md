# ADR-011 — Matriz de autorización por grupo de operaciones como especificación normativa del control de acceso

| Campo | Contenido |
|---|---|
| **Identificador y título** | ADR-011. Matriz de autorización por grupo de operaciones como especificación normativa única del control de acceso. |
| **Estado** | Aceptada. Deliberada y aprobada en Mesa de Arquitectura del 21 de septiembre de 2026. No reemplaza a ningún registro anterior. |
| **Restricciones aplicables** | RD-08, RN-01 (Ley 1581 de 2012), RI-03, RI-04. |
| **Fecha** | 21 de septiembre de 2026. |

---

## Contexto

El control de acceso es el atributo de calidad de mayor peso del sistema: RD-08 lo declara de primer orden y la Ley 1581 de 2012 lo sustenta, porque el sistema trata datos sensibles de salud. Su cumplimiento se verifica probando cada perfil contra una especificación, y esa especificación debe ser única.

El proyecto produce dos representaciones del mismo control, con granularidades distintas. La especificación de requerimientos publica una matriz de **usuarios contra módulos**: siete perfiles por siete módulos. El documento de diseño publica una matriz de **perfiles contra grupos de operaciones**: siete perfiles por trece grupos, noventa y una combinaciones.

Las dos granularidades no son equivalentes, y la diferencia tiene consecuencia operativa. Dentro de un mismo módulo conviven operaciones cuyo perfil autorizado no coincide: en inventario y alertas, consultar existencias, configurar umbrales y atender una alerta corresponden a perfiles distintos. Una matriz que solo puede expresar el acceso al módulo obliga a resolver esas diferencias con una única celda, y toda resolución de ese tipo concede de más o de menos.

Es necesario declarar cuál de las dos representaciones es normativa, y en consecuencia contra cuál se prueba el escenario de control de acceso por jurisdicción.

---

## Opciones consideradas

### Opción A — La matriz por módulo es normativa

Conserva la jerarquía documental habitual, en la que la especificación de requerimientos es la fuente única de verdad del alcance funcional.

Se descarta por una razón de expresividad, no de conveniencia: la granularidad de módulo no puede describir lo que el sistema realmente hace cumplir. El gateway autoriza por ruta y rol, y cada servicio propietario filtra por jurisdicción a nivel de registro; ninguno de los dos opera sobre módulos. Una especificación que no se corresponde con el punto donde se aplica el control no es verificable.

**Descartada.**

### Opción B — La matriz por grupo de operaciones es normativa

La matriz del documento de diseño se corresponde uno a uno con las operaciones que los contratos publican, y por tanto con lo que el gateway y los servicios pueden verificar. La representación por módulo se conserva como vista derivada, por su valor de lectura funcional.

**Seleccionada.**

### Opción C — Conservar ambas como normativas

Dos especificaciones de una misma regla de seguridad divergen con el tiempo, y la divergencia se descubre cuando alguna de las dos se usa para probar. Mantener dos fuentes normativas del mismo control equivale a no tener ninguna.

**Descartada.**

---

## Compromiso evaluado

**Se gana.** Una sola especificación verificable, con noventa y un casos de prueba que se corresponden con operaciones existentes en el contrato. Capacidad de expresar diferencias de autorización dentro de un mismo módulo, que es donde reside el riesgo real bajo RD-08. Correspondencia directa entre lo que la especificación declara y lo que el gateway y los servicios aplican.

**Se sacrifica.** La matriz de usuarios contra módulos es más legible para el cliente y para la sustentación, y deja de ser normativa. Se acepta el costo y se mitiga conservándola como vista derivada declarada. Se acepta también que la especificación de requerimientos deje de ser fuente única de verdad en esta materia concreta: la autorización por operación es una especificación de diseño y no un enunciado de alcance.

---

## Decisión

1. **La matriz de autorización por grupo de operaciones del documento de diseño es la especificación normativa única del control de acceso.** Contra ella se implementan la compuerta del gateway y el filtrado por jurisdicción de cada servicio propietario, y contra ella se prueba el escenario de control de acceso por jurisdicción.

2. **La matriz de usuarios contra módulos se conserva como vista derivada**, declarada como tal en su encabezado. Se mantiene sincronizada con la normativa y no se edita de forma independiente.

3. **El alcance de lectura del perfil auditor se limita a lo que su función requiere**: trazabilidad de la unidad, jerarquía institucional y bitácora de auditoría. No alcanza al inventario ni a las transferencias, porque su alcance de datos declarado es la bitácora y no la operación.

4. **El perfil de administrador nacional no escribe en analítica e indicadores.** La analítica es de solo lectura sobre los datos que agrega, conforme a RI-03; su vista nacional consolidada es una lectura agregada. Escribe en administración institucional y territorial, donde mantiene la jerarquía, el registro de instituciones y la jurisdicción de los usuarios.

5. **El administrador nacional consulta las campañas publicadas**, de forma coherente con su jurisdicción propia nacional.

6. **El operador de banco consulta el inventario y no configura sus umbrales.** El operador modifica el inventario de forma indirecta, al cambiar el estado de una unidad; la configuración de umbrales corresponde al administrador de la institución.

7. **La verificación del escenario de control de acceso recorre las noventa y una combinaciones**, en lectura y en escritura, comprobando tanto el acceso permitido como el denegado, e incluye la invocación directa a cada servicio sin pasar por el gateway.

---

## Justificación

El criterio de decisión es la expresividad de la especificación. Una especificación de seguridad debe poder describir exactamente lo que el sistema hace cumplir, y debe formularse en la misma unidad en que se aplica el control. El gateway autoriza rutas y el servicio filtra registros; ninguno autoriza módulos.

La matriz por grupo de operaciones satisface esa condición y la matriz por módulo no. Una especificación cuya granularidad es más gruesa que la del mecanismo que la realiza obliga a que alguien resuelva la diferencia en el momento de implementar, y esa resolución queda fuera de todo documento.

El punto 7 recoge la contramedida declarada frente a la autorización aplicada solo en la interfaz: ocultar una opción del menú no constituye control cumplido, y la prueba debe ejercerse contra el servicio directamente.

---

## Vigencia de lo elegido

No aplica. La decisión fija la fuente normativa de una especificación, no selecciona una tecnología con ventana de soporte.

---

## Consecuencias

**Sobre la especificación de requerimientos.** La matriz de usuarios contra módulos incorpora el encabezado que la declara vista derivada y recoge los alcances fijados en los puntos 3 a 6.

**Sobre la verificación.** El escenario de control de acceso por jurisdicción pasa a tener noventa y un casos de prueba declarados. Es trabajo estimable y se incorpora al backlog como historia propia del Sprint 3, no como criterio de aceptación de otra.

**Sobre la administración del alcance territorial.** El requerimiento funcional RF-22 especifica la asignación de jurisdicción a un usuario, capacidad sin la cual la restricción territorial no sería administrable. Con ella, las cuestiones abiertas P-07 del documento de arquitectura y D-03 del documento de diseño quedan resueltas.

**Sobre el Tech Radar.** Ninguna entrada nace, cambia de anillo ni se retira. La política de autorización basada en requisitos personalizados de la plataforma del gateway y del Servicio Institucional, ya adoptada, es el instrumento con que se expresa la matriz normativa.

---

## Responsable y fecha de revisión

| Rol | Responsabilidad |
|---|---|
| Product Owner | Alcance de datos de los perfiles de auditor y administrador nacional. |
| Arquitecta de Software | Correspondencia entre la matriz normativa, la vista derivada y la implementación del control. |

**Próxima revisión:** cierre del Sprint 3, con las noventa y una combinaciones ejecutadas sobre el ambiente de demostración.
