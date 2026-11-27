# 14: Ventas y margen por servicio

**What to build:** En el Dashboard, el administrador ve ventas por servicio en personas atendidas y en monto neto, por día o mes, y el margen por servicio (ventas netas del servicio menos comisiones de ese servicio). El descuento de cada venta se reparte entre sus ítems en proporción al bruto, por mayor resto, de modo que la suma por servicio es exactamente el neto. El abono retenido no entra en el margen.

**Blocked by:** 13

**Status:** ready-for-agent

- [ ] Personas atendidas = personas que bucearon (sin inasistencias).
- [ ] Pruebas del reparto: la suma siempre es el descuento; bruto 0; un solo ítem; empates de resto.
- [ ] La suma de ventas netas por servicio es igual al total de ventas netas del período.
- [ ] El margen por servicio coincide con el dataset de referencia; el dueño aporta comisión 0.
