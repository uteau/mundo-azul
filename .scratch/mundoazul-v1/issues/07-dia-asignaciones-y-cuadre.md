# 07: Día: asignaciones y cuadre

**What to build:** En la pantalla Día, el administrador elige una fecha y ve sus ventas y las personas que bucearon por servicio. En un editor guía × servicio registra cuántas personas llevó cada guía (ADR-0001: no hay salidas ni tope por guía). Un panel de cuadre compara, para cada servicio, las personas asignadas con las que bucearon y resalta sobrantes y faltantes. Un día descuadrado se puede guardar igual, con un aviso. Hay un listado de días descuadrados en un rango.

**Blocked by:** 03, 04

**Status:** ready-for-agent

- [ ] Las asignaciones del día se guardan como reemplazo transaccional del conjunto completo.
- [ ] Validaciones, cada una con un caso aceptado y uno rechazado: cantidad ≥ 1, rol guía, una fila por guía/servicio/día, comisión vigente a la fecha.
- [ ] Un guía puede tener asignaciones de varios servicios el mismo día; el dueño puede tener asignaciones.
- [ ] El cuadre detecta sobrantes y faltantes por servicio, incluso cuando un servicio tiene ventas sin asignaciones o asignaciones sin ventas.
- [ ] El listado de días descuadrados devuelve los días correctos en un rango.
