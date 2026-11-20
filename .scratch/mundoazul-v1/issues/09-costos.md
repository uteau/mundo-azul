# 09: Costos

**What to build:** En la pantalla Costos, el administrador registra costos con fecha de pago, categoría, monto y descripción opcional. Las categorías fijas (luz, agua, bencina) vienen precargadas y puede crear otras desde el mismo formulario. El listado filtra por rango y categoría y muestra el total. Un costo cuenta completo en su fecha de pago, sin prorrateo.

**Blocked by:** 01

**Status:** ready-for-agent

- [ ] Las categorías luz, agua y bencina existen desde la primera migración y están marcadas como fijas.
- [ ] Se valida monto > 0 y categoría obligatoria.
- [ ] Se puede crear una categoría desde el formulario y renombrarla; una con costos se desactiva en lugar de borrarse.
- [ ] El listado filtra por rango y categoría con un total correcto (prueba).
- [ ] Se pueden editar y eliminar costos, con confirmación.
