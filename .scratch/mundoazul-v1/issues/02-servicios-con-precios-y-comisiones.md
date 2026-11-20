# 02: Servicios con precios y comisiones

**What to build:** En la pantalla Servicios, el administrador crea servicios, los desactiva y les agrega vigencias de precio y de comisión, que puede ver y corregir. El listado muestra el precio y la comisión vigentes hoy. Este ticket construye el mecanismo de vigencias que después usan los sueldos: el valor aplicable a una fecha es el de la vigencia más reciente cuya fecha de inicio es ≤ a esa fecha.

**Blocked by:** 01

**Status:** ready-for-agent

- [ ] Se puede crear, renombrar y desactivar un servicio; uno desactivado no se ofrece en formularios nuevos, pero conserva su historia.
- [ ] Se puede agregar, editar y eliminar vigencias de precio y de comisión; agregar una nueva no borra las anteriores.
- [ ] El listado muestra el precio y la comisión vigentes hoy.
- [ ] Pruebas de vigencia a la fecha exacta de inicio, entre dos vigencias y antes de la primera (sin valor: error que pide definirlo).
- [ ] El editor de vigencias es un componente reutilizable.
