# 11: Remuneraciones

**What to build:** En la pantalla Remuneraciones, el administrador elige un rango y ve cuánto ganó cada guía por día (personas por servicio, comisión aplicada, bonos y total) y cuánto ganó cada trabajador por día (sueldo del día, bonos y total). Al final ve los totales por persona y el total general. El dueño aparece con remuneración 0. El sistema no registra si ya se pagó.

**Blocked by:** 06, 08

**Status:** ready-for-agent

- [ ] Pago de un guía en un día = Σ (comisión guardada en la asignación × personas asignadas) + bonos.
- [ ] El dueño tiene remuneración 0 aunque tenga asignaciones.
- [ ] Pago de un trabajador en un día = monto del día trabajado + bonos.
- [ ] Las inasistencias y los descuentos no afectan las comisiones.
- [ ] Prueba con cambio de vigencia de comisión dentro del rango: las asignaciones registradas antes del cambio conservan su comisión.
- [ ] Totales por persona y general correctos.
