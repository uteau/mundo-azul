# 09: Costos

**What to build:** En la pantalla Costos, el administrador registra costos con fecha de pago, categoría, monto y descripción opcional. Las categorías fijas (luz, agua, bencina) vienen precargadas y puede crear otras desde el mismo formulario con un selector creable, que este ticket construye como componente reutilizable (lo usan también 06 y 10). El listado filtra por rango y categoría y muestra el total. Un costo cuenta completo en su fecha de pago, sin prorrateo.

**Blocked by:** 01

**Status:** ready-for-agent

- [ ] Las categorías luz, agua y bencina existen desde la primera migración y están marcadas como fijas.
- [ ] Se valida monto > 0 y categoría obligatoria.
- [ ] Se puede crear una categoría desde el formulario y renombrarla; una sin costos se puede eliminar y una con costos solo se desactiva.
- [ ] Las categorías fijas no se eliminan ni se renombran, pero se pueden desactivar.
- [ ] Un costo con fecha posterior a hoy se rechaza.
- [ ] El selector creable es un componente reutilizable.
- [ ] El listado filtra por rango y categoría con un total correcto (prueba).
- [ ] Se pueden editar y eliminar costos, con confirmación.
