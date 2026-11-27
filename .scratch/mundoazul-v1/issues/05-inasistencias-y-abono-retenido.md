# 05: Inasistencias y abono retenido

**What to build:** En el formulario de venta, el administrador indica por ítem cuántas personas no se presentaron. El sistema propone un abono retenido del 50 % de (inasistencias × precio unitario), que el administrador puede editar. Las inasistencias no cuentan como personas que bucearon. Una venta en que nadie se presentó se registra con todas sus personas como inasistencias; no existe el estado "cancelada" (ADR-0002). El listado de ventas filtra por "con inasistencias".

**Blocked by:** 04

**Status:** ready-for-agent

- [ ] Se valida 0 ≤ inasistencias ≤ cantidad.
- [ ] Un abono retenido > 0 sin inasistencias se rechaza.
- [ ] El resumen en vivo muestra los abonos retenidos, y el ingreso es neto + abonos retenidos.
- [ ] Prueba: venta con todas sus personas inasistentes, cuyo bruto y neto son 0 y cuyo ingreso es solo el abono.
- [ ] El filtro con/sin inasistencias funciona en el listado.
