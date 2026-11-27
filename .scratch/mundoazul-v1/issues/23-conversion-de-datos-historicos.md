# 23: Conversión de datos históricos

**What to build:** Los datos históricos que reciba el administrador se cargan una sola vez con un conversor fuera de la aplicación. Si llegan antes del primer uso, el conversor escribe en una base nueva, que se instala con el procedimiento de restauración. Si llegan después, escribe en una copia respaldada de la base en uso, mientras no se registra nada en la aplicación. Se guardan los precios y comisiones realmente cobrados y pagados, y no se inventa lo que falta: las ventas sin asignaciones quedan como días descuadrados y los totales sin detalle diario no se importan. Lo que no calce con las reglas se lista para que el administrador decida. No hay pantalla de importación.

**Blocked by:** 22, y la muestra de datos históricos (pregunta abierta P-07)

**Status:** needs-info

- [ ] Con la muestra, un `/grill-with-docs` corto fija el mapeo de cada dato al modelo y completa estos criterios.
- [ ] El conversor produce una base con el esquema vigente que la aplicación abre sin errores.
- [ ] Los totales del período convertido coinciden con los totales de origen, y el administrador los revisa.
- [ ] Los datos históricos no se versionan.
