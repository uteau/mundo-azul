# 13: Dashboard: filtros y utilidad

**What to build:** En la pantalla Dashboard, el administrador usa una barra de filtros con atajos de rango (este mes, mes anterior, este año, año anterior, personalizado) y agrupación por día o mes. Ve tarjetas con ventas netas, abonos retenidos, otros ingresos, remuneraciones, costos y utilidad, más un gráfico de utilidad por período. Todo se imputa por la fecha del hecho.

**Blocked by:** 12

**Status:** ready-for-agent

- [ ] Ingresos = ventas netas + abonos retenidos + otros ingresos; utilidad = ingresos − remuneraciones − costos.
- [ ] Los períodos sin datos aparecen como buckets vacíos en el gráfico.
- [ ] El reporte de utilidad coincide con los totales del dataset de referencia.
- [ ] La barra de filtros es reutilizable y admite filtros contextuales (servicio, persona, categoría).
