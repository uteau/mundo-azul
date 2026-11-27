# 07: Día: asignaciones y cuadre

**What to build:** En la pantalla Día, el administrador elige una fecha y ve sus ventas y las personas que bucearon por servicio. En un editor guía × servicio registra cuántas personas llevó cada guía (ADR-0001: no hay salidas ni tope por guía). Cada asignación guarda la comisión vigente a su fecha al registrarla, 0 para el dueño (ADR-0003). Un panel de cuadre compara, para cada servicio, las personas asignadas con las que bucearon y resalta sobrantes y faltantes. Un día descuadrado se puede guardar igual, con un aviso. Hay un listado de días descuadrados en un rango.

**Blocked by:** 03, 05

**Status:** ready-for-agent

- [ ] Las asignaciones del día se guardan como reemplazo transaccional del conjunto completo.
- [ ] Validaciones, cada una con un caso aceptado y uno rechazado: cantidad ≥ 1, rol guía, una fila por guía/servicio/día, comisión vigente a la fecha, fecha no posterior a hoy.
- [ ] Cambiar, corregir o eliminar una vigencia de comisión no altera asignaciones guardadas.
- [ ] Al volver a guardar el día, un par guía × servicio que ya existía conserva su comisión, aunque cambie su cantidad; un par nuevo toma la vigente.
- [ ] Un guía desactivado con asignaciones en el día se conserva al volver a guardarlo.
- [ ] Un guía puede tener asignaciones de varios servicios el mismo día; el dueño puede tener asignaciones.
- [ ] El cuadre detecta sobrantes y faltantes por servicio, incluso cuando un servicio tiene ventas sin asignaciones o asignaciones sin ventas.
- [ ] El listado de días descuadrados devuelve los días correctos en un rango.
