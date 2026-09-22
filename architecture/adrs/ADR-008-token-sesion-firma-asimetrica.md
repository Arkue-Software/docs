# ADR-008 — Token de sesión firmado asimétricamente y validación distribuida

| Campo | Contenido |
|---|---|
| **Identificador y título** | ADR-008. Token de sesión firmado asimétricamente y validación distribuida. |
| **Estado** | Propuesta para deliberación y aprobación en Mesa de Arquitectura. |
| **Restricciones aplicables** | RD-08, RN-01 (Ley 1581 de 2012), RI-03, RI-04. |
| **Fecha** | 22 de septiembre de 2026. |

---

## Contexto

RedVital necesita identificar al usuario que realiza cada operación y transmitir de forma segura la información mínima necesaria para aplicar las reglas de autorización por rol y jurisdicción.

La arquitectura distribuida no debería depender de una consulta central al componente responsable de identidad en cada petición, porque eso convertiría a ese componente en una dependencia síncrona para todas las operaciones del sistema y afectaría la disponibilidad.

Al mismo tiempo, confiar únicamente en que el API Gateway identifique al usuario sería insuficiente. Una solicitud que alcanzara directamente un servicio interno podría intentar evitar ese control. Cada servicio propietario debe poder comprobar de forma independiente que la identidad presentada es auténtica y que el token fue emitido por un componente autorizado.

El token debe contener únicamente la información necesaria para autenticación y autorización. No debe transportar información clínica ni otros datos personales que no sean necesarios para aplicar el control de acceso.

El proyecto es académico. Por ello, se prioriza una solución que pueda implementarse con capacidades estándar de las plataformas ya adoptadas, sin introducir una plataforma adicional de identidad cuyo costo principal sería de aprendizaje, configuración, operación y sustentación.

También se requiere que la clave utilizada para firmar tokens pueda rotarse sin invalidar de forma inmediata todas las sesiones activas.

---

## Opciones consideradas

### Opción A — Sesión central almacenada en servidor

Cada petición contiene un identificador de sesión y los servicios consultan al componente responsable de identidad para conocer el usuario, el rol y la jurisdicción correspondientes.

La alternativa facilita una revocación inmediata, pero convierte al componente de identidad en una dependencia de disponibilidad para prácticamente todas las operaciones.

También introduce una llamada adicional en el camino crítico de cada petición.

**Descartada.**

### Opción B — Token firmado con clave simétrica compartida

Todos los componentes que validan tokens comparten la misma clave utilizada para firmarlos.

La solución es sencilla, pero cualquier componente que posea esa clave puede tanto validar como emitir tokens válidos. Si un servicio es comprometido, la autoridad de emisión completa del sistema también queda comprometida.

**Descartada.**

### Opción C — Token firmado asimétricamente y validado mediante clave pública

El componente responsable de identidad conserva la clave privada y firma los tokens. El API Gateway y los servicios reciben únicamente las claves públicas necesarias para validarlos.

La publicación de las claves públicas mediante JWKS permite mantener simultáneamente claves anteriores y nuevas durante una rotación.

La solución puede implementarse con las capacidades JWT/JWKS de ASP.NET Core y las bibliotecas equivalentes de las plataformas utilizadas por los demás servicios, sin incorporar una plataforma adicional de identidad.

**Seleccionada.**

### Opción D — Plataforma completa de identidad

Una plataforma especializada como proveedor de identidad podría administrar autenticación, sesiones, recuperación de contraseña, federación y emisión de tokens.

Estas capacidades son útiles en escenarios de producción de mayor alcance, pero introducen una plataforma adicional que el equipo debe desplegar, configurar, aprender y mantener.

Para el alcance académico actual, ese costo operativo no se justifica.

**Descartada.**

---

## Compromiso evaluado

**Se gana.** Los servicios pueden verificar la autenticidad de una solicitud sin consultar un servicio central en cada operación. La clave privada permanece únicamente en el componente autorizado para emitir tokens, mientras los demás reciben solo material público de verificación.

La validación local reduce el acoplamiento de disponibilidad y evita que el componente de identidad se convierta en un cuello de botella para cada petición.

La rotación mediante JWKS permite cambiar la clave de firma manteniendo temporalmente disponibles las claves públicas anteriores, evitando invalidar sesiones activas solo por realizar una rotación.

La solución tampoco introduce un costo relevante de licenciamiento para el proyecto académico.

**Se sacrifica.** Un token ya emitido continúa siendo válido durante su periodo de vigencia aunque posteriormente cambien determinadas condiciones del usuario.

También se introduce la responsabilidad de proteger la clave privada, publicar correctamente las claves públicas y mantener una configuración coherente de validación en todos los componentes.

El principal costo para el proyecto no es monetario sino operativo: configuración, pruebas, mantenimiento y conocimiento del equipo.

---

## Decisión

1. **RedVital utiliza tokens de sesión firmados mediante criptografía asimétrica.**

2. **El Servicio Institucional es responsable de emitir los tokens**, por ser el contexto que mantiene la identidad, el rol y la jurisdicción del usuario.

3. **La clave privada utilizada para firmar tokens permanece exclusivamente en el componente emisor.** El API Gateway y los servicios de dominio no reciben esa clave.

4. **El API Gateway y los servicios validan los tokens utilizando claves públicas publicadas mediante JWKS.**

5. **El gateway realiza una primera validación del token, pero cada servicio vuelve a validarlo antes de autorizar operaciones sobre sus recursos.**

6. **El token contiene siete reivindicaciones necesarias para autenticación y autorización:**
   - `sub`: identificador del usuario;
   - `role`: rol del usuario;
   - `jurisdiction`: jurisdicción autorizada;
   - `iss`: emisor del token;
   - `aud`: audiencia autorizada;
   - `iat`: momento de emisión;
   - `jti`: identificador único del token.

7. **El token no contiene información clínica, nombre completo, documento de identidad, correo electrónico ni otros datos personales que no sean necesarios para aplicar el control de acceso.**

8. **La vigencia inicial del token se fija en quince minutos.**

9. **La rotación de la clave de firma se realiza mediante JWKS**, manteniendo disponible la clave pública anterior durante el tiempo necesario para validar tokens todavía vigentes.

10. **Una rotación ordinaria de claves no invalida las sesiones activas.**

11. **La autorización no se decide únicamente a partir del token.** El token comunica identidad, rol y jurisdicción; el servicio propietario conserva la decisión final sobre el acceso al recurso concreto.

12. **La clave privada no se almacena en el repositorio ni dentro de la imagen del contenedor.** Su origen y mecanismo de entrega dependen del ambiente.

13. **La implementación utilizará las capacidades JWT/JWKS de las plataformas ya adoptadas**, evitando introducir una plataforma completa de identidad mientras los requisitos actuales no la justifiquen.

---

## Justificación

La firma asimétrica separa dos responsabilidades que no deben pertenecer a los mismos componentes: emitir un token y verificarlo.

El Servicio Institucional necesita la capacidad de emitir porque conoce la identidad, el rol y la jurisdicción. Los demás servicios solo necesitan comprobar que esa información proviene de una fuente legítima.

Con una clave simétrica compartida, cualquier servicio capaz de validar también tendría el secreto necesario para fabricar tokens. Con firma asimétrica, los servicios reciben únicamente una clave pública que permite verificar, pero no emitir.

La decisión también mejora la disponibilidad. Una vez emitido el token, los servicios pueden validarlo localmente sin consultar al Servicio Institucional en cada petición.

La principal limitación es que un token ya emitido no puede revocarse instantáneamente sin introducir estado adicional. La vigencia de quince minutos limita esa ventana sin reintroducir una dependencia central en todas las solicitudes.

En el contexto académico, la alternativa seleccionada permite demostrar correctamente autenticación, autorización y rotación de claves utilizando herramientas ya disponibles en las plataformas del proyecto, sin incorporar el costo de aprendizaje y operación de un proveedor de identidad completo.

---

## Vigencia de lo elegido

La decisión de utilizar firma asimétrica permanece vigente mientras varios componentes necesiten validar credenciales emitidas por un único componente responsable.

La elección concreta de biblioteca JWT/JWKS puede cambiar sin modificar esta decisión, siempre que se mantengan la separación entre emisión y validación, la protección de la clave privada y la compatibilidad con los tokens emitidos.

La vigencia de quince minutos es una configuración y puede modificarse si pruebas o requisitos posteriores justifican otro valor.

---

## Consecuencias

**Sobre la seguridad.** Solo el componente emisor conserva capacidad para generar tokens válidos. La compromisión de un servicio que únicamente dispone de claves públicas no permite emitir tokens nuevos.

**Sobre la disponibilidad.** Los servicios no necesitan consultar al Servicio Institucional para validar cada solicitud después de que el token ha sido emitido, reduciendo una dependencia síncrona común.

**Sobre la privacidad.** Los tokens transportan únicamente la información mínima necesaria para aplicar autenticación y autorización. Los datos clínicos y demás información personal permanecen en los servicios propietarios.

**Sobre la revocación.** Un token puede continuar siendo válido hasta su vencimiento. Ese riesgo se limita mediante una vigencia corta y no se introduce, por ahora, una lista central de revocación.

**Sobre la rotación de claves.** Las claves públicas se publican mediante JWKS y las claves anteriores permanecen temporalmente disponibles para no invalidar sesiones activas durante una rotación ordinaria.

**Sobre el gateway.** El API Gateway valida la firma, el emisor, la audiencia y la vigencia antes de aplicar la compuerta por ruta y rol definida en ADR-007.

**Sobre los servicios.** Cada servicio vuelve a validar el token y aplica después sus propias reglas de jurisdicción y acceso al recurso. La red interna no se considera una zona de confianza.

**Sobre las herramientas.** Se utilizan las capacidades JWT/JWKS de ASP.NET Core y bibliotecas equivalentes en los demás servicios. No se introduce una plataforma especializada de identidad para el alcance actual.

**Sobre el costo.** No se introduce costo significativo de licenciamiento. El costo asumido corresponde principalmente a configuración, protección de claves, pruebas y mantenimiento.

**Sobre la verificación.** Deben probarse, como mínimo, tokens con firma inválida, emisor incorrecto, audiencia incorrecta, token vencido, rol no autorizado y jurisdicción ajena.

**Sobre el Tech Radar.** La biblioteca o mecanismo utilizado para JWT/JWKS debe quedar registrado con su versión, anillo y justificación correspondientes.

---

## Responsable y fecha de revisión

| Rol | Responsabilidad |
|---|---|
| Arquitecta de Software | Coherencia del esquema de identidad, reivindicaciones y fronteras de validación. |
| Desarrollador | Implementación de emisión, validación y rotación de claves. |
| QA / Tester | Verificación de los casos de token válido, inválido, vencido y acceso fuera de jurisdicción. |

**Próxima revisión:** cierre del Sprint 2, con las siete reivindicaciones implementadas y el procedimiento de rotación de clave probado.
