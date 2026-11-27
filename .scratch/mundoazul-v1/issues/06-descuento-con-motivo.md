# 06: Descuento con motivo

**What to build:** El administrador aplica a una venta un descuento en pesos sobre lo que pagan las personas que bucearon, nunca sobre el abono retenido. Puede ingresar un porcentaje como ayuda para proponer el monto y luego ajustarlo. Todo descuento > 0 lleva un motivo, elegido de una lista o creado en el momento con el selector creable (construido en 09). Los motivos con ventas asociadas se desactivan en lugar de borrarse. El listado de ventas filtra por "con descuento".

**Blocked by:** 05, 09

**Status:** ready-for-agent

- [ ] Se valida 0 ≤ descuento ≤ bruto (de quienes bucearon); un descuento mayor se rechaza.
- [ ] Se exige motivo si y solo si el descuento es > 0.
- [ ] Editar inasistencias vuelve a validar el descuento (prueba: subir inasistencias deja el descuento > bruto → rechazo).
- [ ] Se pueden crear motivos desde el formulario; uno sin ventas se puede eliminar y uno con ventas solo se desactiva.
- [ ] Una venta con un motivo desactivado se puede editar y guardar conservándolo.
