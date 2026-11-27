# 15: Comparador entre años

**What to build:** En el Dashboard, el administrador elige un rango de fechas (por ejemplo, 15-12-2025 al 28-02-2026) y un año de comparación. El sistema traslada el mismo rango a ese año, conservando día y mes, y compara personas atendidas y dinero, total y por servicio, con diferencia absoluta y variación porcentual. Sirve para comparar una temporada con la de otro año.

**Blocked by:** 14

**Status:** ready-for-agent

- [ ] El año de comparación es el del inicio del rango: 15-12-2025 al 28-02-2026 comparado con 2024 da 15-12-2024 al 28-02-2025.
- [ ] El rango trasladado conserva día y mes de ambos extremos; un rango que cruza el cambio de año (diciembre a febrero) se traslada completo.
- [ ] El 29-02 se toma como 28-02 en un año no bisiesto.
- [ ] El año de comparación puede ser anterior o posterior al del rango elegido.
- [ ] Diferencia = elegido − comparación; variación = (elegido − comparación) / comparación (120 personas contra 100 da +20 %).
- [ ] La variación porcentual con base 0 se muestra como "sin base", sin error.
- [ ] Un servicio presente en un solo año aparece con 0 en el otro.
- [ ] Prueba con datos del mismo tramo en dos años, incluido un rango que cruza el cambio de año.
