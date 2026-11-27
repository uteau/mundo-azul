# La inasistencia reemplaza a la cancelación de ventas

El diseño original tenía ventas "canceladas" cuyo único ingreso era el abono, y un campo abono en toda venta. Pero en Mundo Azul nada se registra antes del día del buceo, el abono de quien sí bucea no es un ingreso aparte, y es común que falte solo parte de una reserva. Decidimos registrar **Inasistencias** por servicio dentro de la venta, cada una con su **abono retenido** (propuesto en 50 % del precio, editable); una venta donde no llegó nadie es simplemente una venta con todas sus personas inasistentes. No existe estado "cancelada" ni abono en ventas sin inasistencias.

## Consecuencias

- Ingreso de una venta = Σ precio × personas que bucearon − descuento + Σ abonos retenidos.
- Las inasistencias no generan comisión ni cuentan como personas atendidas.
- Una cancelación anticipada solo se registra si la escuela se queda con el abono, en la fecha que estaba reservada.
