# ADR-001: Seleccion del stack tecnologico

- **Estado:** Aceptada
- **Fecha:** 2026-08-18
- **Responsable de implementar:** Arquitecto de Software
- **Fecha de revision:** Cierre del Sprint 3

## Contexto

El proyecto debe construirse en 5 sprints con un equipo de 7 integrantes de
perfil junior y dedicacion parcial. Los requerimientos ya documentados imponen
condiciones tecnicas concretas: control de acceso jerarquico por territorio
(RNF-02), ejecucion asincrona con reintento para notificaciones (RNF-06),
tareas programadas para vencimientos y expiracion de reservas (RNF-08 y RF-15),
trazabilidad con bitacora de auditoria (RF-04 y RF-06) y un modelo de dominio
extensible a otros tipos de donacion (M9).

## Opciones consideradas

1. **Java con Spring Boot.** Estandar de facto en desarrollo empresarial.
   Spring Security cubre el control de acceso por rol y alcance; Spring
   Scheduler cubre las tareas programadas; JPA/Hibernate soporta herencia y
   polimorfismo en el modelo de dominio.
2. **Python con Django.** Produce resultados visibles mas rapido y su panel de
   administracion cubriria parte del modulo territorial. Requiere Celery para
   el procesamiento asincrono.
3. **TypeScript con NestJS.** Un solo lenguaje en frontend y backend, con una
   organizacion modular alineada al modelo C4.

## Trade-off evaluado

Velocidad de entrega frente a dominio del equipo. Django habria acelerado los
primeros sprints, pero ningun integrante lo maneja. Con un plazo de 5 sprints no
existe margen para absorber una curva de aprendizaje en el lenguaje base.

## Decision

Backend en **Java con Spring Boot**, frontend en **TypeScript con React**, y
persistencia en **PostgreSQL**.

## Justificacion

Es la unica combinacion en la que el equipo tiene experiencia previa
simultaneamente en backend, frontend y base de datos. Cada requerimiento
critico se resuelve con componentes estandar del ecosistema Spring, sin
construir mecanismos propios ni incorporar infraestructura adicional.

PostgreSQL no se somete a discusion: la trazabilidad de unidades de sangre
exige garantias transaccionales. Una unidad no puede quedar en estado
inconsistente.

## Consecuencias

- El codigo resulta mas verboso que en las alternativas evaluadas, lo que
  incrementa el tiempo de escritura por funcionalidad.
- El arranque de la aplicacion es mas lento en desarrollo.
- Se acepta la dependencia del ecosistema Spring para seguridad, persistencia y
  tareas programadas.
- El detalle completo del catalogo de herramientas y su justificacion esta en el
  Documento de Herramientas, Politicas y Lineamientos.
