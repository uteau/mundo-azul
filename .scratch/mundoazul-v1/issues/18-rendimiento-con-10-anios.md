# 18: Rendimiento con 10 años de datos

**What to build:** Una prueba genera unos 10 años de datos sintéticos (hasta 50.000 ventas, con asignaciones, días trabajados, bonos, costos y otros ingresos) y verifica que cada reporte del dashboard y de remuneraciones responde en menos de un segundo. Si alguno no cumple, se agregan índices por migración.

**Blocked by:** 14, 15, 16, 17

**Status:** ready-for-agent

- [ ] Existe un generador de datos sintéticos reproducible (semilla fija).
- [ ] Cada reporte responde en < 1 s sobre los 10 años, medido en modo release.
- [ ] Los índices agregados van en una migración nueva, sin modificar las anteriores.
