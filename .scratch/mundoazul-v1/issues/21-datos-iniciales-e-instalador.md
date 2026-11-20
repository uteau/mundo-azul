# 21: Datos iniciales e instalador

**What to build:** Se cargan los datos iniciales que entregue el administrador (servicios, precios, comisiones, personal, sueldos, categorías de costo y de otros ingresos, motivos de descuento) mediante una migración de semillas idempotente. Se genera el instalador para Windows, se prueba en el equipo del administrador sin internet y se escribe el manual de uso: flujo diario, inasistencias, cuadre, correcciones, respaldo y restauración.

**Blocked by:** 18, 20, y los datos iniciales del administrador (pregunta abierta P-04)

**Status:** ready-for-human

- [ ] La migración de semillas es idempotente y carga exactamente los datos entregados.
- [ ] El instalador de Windows instala y abre la aplicación sin conexión a internet.
- [ ] Prueba real en el equipo del administrador: registrar un día completo y respaldar a la carpeta sincronizada.
- [ ] Se cumplen los 7 criterios de aceptación generales de los requerimientos.
- [ ] Existe el manual de uso.
