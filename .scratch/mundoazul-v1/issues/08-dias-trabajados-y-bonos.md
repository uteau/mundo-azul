# 08: Días trabajados y bonos

**What to build:** En la pantalla Día, el administrador marca qué trabajadores trabajaron. El monto se precarga desde el sueldo vigente y se puede editar. También registra bonos del día para guías o trabajadores (monto y nota opcional), aunque el guía no tenga asignaciones ese día. El dueño no recibe bonos. Un listado general muestra los bonos por rango y permite editarlos o eliminarlos.

**Blocked by:** 07

**Status:** ready-for-agent

- [ ] Un trabajador tiene como máximo un día trabajado por fecha; un duplicado se rechaza.
- [ ] El monto propuesto es el sueldo vigente a la fecha; se guarda el monto editado.
- [ ] Sin sueldo vigente, el día trabajado se rechaza.
- [ ] Un bono queda imputado a su fecha aunque se ingrese después; un guía puede tener bono sin asignaciones ese día.
- [ ] Un bono al dueño se rechaza, y el dueño no se ofrece en el formulario de bonos.
- [ ] Marcar o desmarcar como dueño a un guía con asignaciones o bonos se rechaza (completa el criterio de 03).
- [ ] Un día trabajado o un bono con fecha posterior a hoy se rechaza.
- [ ] Se pueden editar y eliminar días trabajados y bonos, con confirmación.
