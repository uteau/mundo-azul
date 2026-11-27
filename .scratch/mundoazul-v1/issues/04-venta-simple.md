# 04: Venta simple

**What to build:** El administrador registra una venta (una por reserva) para la fecha de la actividad, con uno o más servicios y la cantidad de personas de cada uno, y una nota opcional. El precio unitario se propone desde el precio vigente a esa fecha, se recalcula si cambia la fecha antes de guardar y queda fijo al guardar. Un resumen en vivo muestra el monto bruto, el neto y el ingreso. En la pantalla Ventas se listan las ventas filtrando por rango y servicio, y se pueden editar o eliminar con confirmación. Los montos de la venta se calculan a partir de sus ítems y no se guardan.

**Blocked by:** 02

**Status:** ready-for-agent

- [ ] Venta e ítems se guardan en una sola transacción; un error no deja la venta a medias.
- [ ] Si el servicio no tiene precio vigente a la fecha, la venta se rechaza.
- [ ] Una venta con fecha posterior a hoy se rechaza.
- [ ] Una venta con dos ítems del mismo servicio se rechaza.
- [ ] El precio unitario no se edita en el formulario.
- [ ] Una prueba verifica que cambiar el precio del catálogo no altera una venta ya guardada.
- [ ] Editar una venta vuelve a validar todas las reglas.
- [ ] Al editar, un ítem que conserva su servicio conserva su precio aunque el catálogo cambió; un ítem nuevo o con otro servicio toma el vigente; cambiar la fecha hace que todos los ítems tomen el precio vigente a la nueva fecha, y el formulario lo avisa antes de guardar.
- [ ] Una venta con un servicio que después se desactivó se puede editar y guardar conservándolo.
- [ ] El listado filtra por rango de fechas y servicio, y muestra los montos derivados.
- [ ] El formulario se puede usar entero con teclado. (Verificación manual del administrador: una venta de 1 a 3 ítems se registra en menos de 30 segundos.)
