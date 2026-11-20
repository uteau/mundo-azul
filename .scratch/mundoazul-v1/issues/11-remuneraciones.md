# 11: Remuneraciones

**What to build:** En la pantalla Remuneraciones, el administrador elige un rango y ve cuánto ganó cada guía por día (personas por servicio, comisión aplicada, bonos y total) y cuánto ganó cada trabajador por día (sueldo del día, bonos y total). Al final ve los totales por persona y el total general. El dueño aparece con comisión 0 y con sus bonos. El sistema no registra si ya se pagó.

**Blocked by:** 08

**Status:** ready-for-agent

- [ ] Pago de un guía en un día = Σ (comisión vigente × personas asignadas) + bonos.
- [ ] El dueño tiene comisión 0 aunque tenga asignaciones; sus bonos sí cuentan.
- [ ] Pago de un trabajador en un día = monto del día trabajado + bonos.
- [ ] Las inasistencias y los descuentos no afectan las comisiones.
- [ ] Prueba con cambio de vigencia de comisión dentro del rango.
- [ ] Totales por persona y general correctos.
