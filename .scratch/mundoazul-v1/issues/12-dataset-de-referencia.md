# 12: Dataset de referencia

**What to build:** Un escenario de unas dos semanas, armado con las funciones de servicio, que sirve de verdad de referencia para las remuneraciones y para todos los reportes del dashboard. Sus totales esperados se calculan a mano y quedan documentados junto a la prueba.

**Blocked by:** 05, 06, 10, 11

**Status:** ready-for-agent

- [ ] Incluye cambios de vigencia de precio, comisión y sueldo dentro del período.
- [ ] Incluye inasistencias parciales y una venta con todas sus personas inasistentes.
- [ ] Incluye descuentos sobre ventas de varios servicios, con resto no divisible.
- [ ] Incluye al dueño con asignaciones, un día descuadrado, bonos (uno a un guía sin asignaciones), costos y otros ingresos.
- [ ] Los totales esperados (por persona, por servicio, por categoría, utilidad) están calculados a mano y documentados, y el administrador los revisa antes de cerrar el ticket.
- [ ] La prueba de remuneraciones contra este dataset pasa.
- [ ] El dataset es reutilizable por las pruebas de los reportes del dashboard.
