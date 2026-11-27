# La comisión queda fija en la asignación

El diseño original guardaba el precio en cada ítem de venta, pero resolvía la comisión por fecha cada vez que se calculaba una remuneración. Así, corregir o borrar una vigencia de comisión cambiaba remuneraciones pasadas que quizá ya se pagaron, y podía dejar asignaciones sin comisión. Decidimos que cada **Asignación** guarde la comisión vigente a su fecha al registrarla (0 para el dueño), igual que la venta guarda el precio: un cambio de comisión rige solo para lo que se registra después.

## Consecuencias

- Las vigencias de precio, comisión y sueldo se pueden editar o borrar sin validar registros existentes; el editor avisa que solo afectan registros nuevos.
- Al volver a guardar las asignaciones de un día, un par guía × servicio que ya existía conserva su comisión; solo un par nuevo toma la vigente.
- Una comisión mal digitada se corrige en la vigencia y luego, en cada día afectado, quitando la asignación, guardando y volviendo a agregarla. No hay recálculo masivo.
