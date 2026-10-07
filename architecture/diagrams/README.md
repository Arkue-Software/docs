# Diagramas de Arquitectura

Esta carpeta contiene los diagramas arquitectónicos de **Red Vital**.

Los diagramas documentan diferentes vistas del sistema y complementan la información definida en el SAT, SAD, SDD y los ADRs.

---

## Diagramas disponibles

### C4 – Nivel 1: Contexto

- [Red Vital C4-c1.png](./Red%20Vital%20C4-c1.png)

Representa el contexto general del sistema Red Vital, sus usuarios y las interacciones principales con sistemas externos.

---

### C4 – Nivel 2: Contenedores

- [Red Vital C4-c2- Container Diagram.png](./Red%20Vital%20C4-c2-%20Container%20Diagram.png)

Representa los principales contenedores que componen Red Vital y las relaciones entre ellos.

---

### C4 – Nivel 3: Componentes / Flujos específicos

Actualmente se encuentran documentados los siguientes diagramas:

- [Campañas](./Red%20Vital%20C4-c3-%20campa%C3%B1as.png)
- [Donación](./Red%20Vital%20C4-c3-%20donacion.png)
- [Identidad](./Red%20Vital%20C4-c3-%20Identidad.png)
- [Notificaciones](./Red%20Vital%20C4-c3-%20Notificaciones.png)
- [Institucional](./Red%20Vital%20C4-c3-%20institucional.png)

Estos diagramas detallan componentes y relaciones internas asociadas a funcionalidades específicas del sistema.

---

## Uso de los diagramas

Los diagramas contenidos en esta carpeta son utilizados como soporte visual de la documentación arquitectónica de Red Vital.

Deben ser referenciados desde los documentos correspondientes cuando ayuden a explicar:

- el contexto del sistema;
- la estructura de contenedores;
- la organización interna de los componentes;
- las interacciones entre servicios;
- los flujos principales del sistema;
- las decisiones de arquitectura;
- el despliegue de la solución.

---

## Convención de nombres

Los diagramas deben conservar una convención de nombres consistente.

Para los diagramas C4 se utilizará la siguiente estructura:

`Red Vital C4-cX-descripcion.png`

Donde:

- `c1` corresponde al diagrama de contexto;
- `c2` corresponde al diagrama de contenedores;
- `c3` corresponde a diagramas de componentes o vistas específicas;
- la descripción identifica el módulo, flujo o componente representado.

---

## Mantenimiento

Esta carpeta debe actualizarse cuando:

- se cree un nuevo diagrama;
- se modifique una vista arquitectónica existente;
- cambie la arquitectura del sistema;
- se agregue un nuevo módulo o servicio;
- un diagrama sea reemplazado por una versión actualizada.

Los diagramas deben mantenerse alineados con la arquitectura vigente y con las decisiones documentadas en los ADRs.

---

## Navegación

- [Volver a Arquitectura](../README.md)
- [Architecture Decision Records](../adrs/README.md)
- [Atributos de calidad](../quality-attributes/README.md)