# Spec: Mundo Azul v1 — registro diario y dashboard del centro de buceo

Status: ready-for-agent

Referencias: requerimientos (RN/RF/RNF), diseño y lista de tareas en `docs/`; vocabulario en `GLOSSARY.md`; decisiones en ADR-0001 (sin salidas, asignación diaria) y ADR-0002 (la inasistencia reemplaza la cancelación).

## Problem Statement

El administrador de Mundo Azul no tiene un registro ordenado de lo que pasa cada día en el centro de buceo. No sabe con certeza cuánto se vendió por servicio, cuánto ganó cada guía y trabajador, cuánto costó operar ni cuánto dejaron los ingresos que no son buceo, como el arriendo de equipos o la carga de botellas. Las comisiones se calculan a mano y los descuentos, inasistencias y abonos retenidos se pierden. Los precios, comisiones y sueldos cambian año a año, así que comparar períodos es difícil. Sin estos datos no puede tomar decisiones estratégicas: qué servicios convienen, cuándo bajar precios, cuánto personal necesita o si una temporada fue mejor que otra.

## Solution

Una aplicación de escritorio para Windows, de un solo usuario y sin internet, donde el administrador registra al final de cada día:

- las ventas de ese día, una por reserva, con sus servicios, personas, inasistencias, abonos retenidos y descuento con motivo;
- las asignaciones: cuántas personas de cada servicio llevó cada guía;
- los días trabajados y los bonos del personal;
- los costos y otros ingresos cuando ocurren.

El sistema calcula las remuneraciones con los precios, comisiones y sueldos vigentes a cada fecha. Avisa cuando las asignaciones de un día no cuadran con lo vendido. El dashboard muestra ventas, márgenes, remuneraciones, costos, otros ingresos, inasistencias, descuentos y utilidad por período, y permite comparar dos rangos de fechas. La base de datos vive en la carpeta local de la aplicación y se respalda en una carpeta sincronizada con la nube.

## User Stories

### Catálogo de servicios

1. Como administrador, quiero crear un servicio con su nombre, para poder venderlo.
2. Como administrador, quiero desactivar un servicio que ya no ofrezco, para que no aparezca al registrar ventas sin perder su historia.
3. Como administrador, quiero agregar un precio con fecha de vigencia a un servicio, para reflejar el alza de la nueva temporada sin alterar las ventas pasadas.
4. Como administrador, quiero agregar una comisión con fecha de vigencia a un servicio, para que los pagos a guías usen el valor correcto según la fecha.
5. Como administrador, quiero ver y corregir el histórico de precios y comisiones de un servicio, para arreglar un error de digitación.
6. Como administrador, quiero ver el precio y la comisión vigentes hoy en el listado de servicios, para revisar el catálogo de un vistazo.
7. Como administrador, quiero que el sistema me impida registrar una venta o asignación sin precio o comisión vigente a esa fecha, para no generar cifras incorrectas.

### Personal

8. Como administrador, quiero registrar guías, para asignarles personas y calcular sus comisiones.
9. Como administrador, quiero marcar a un guía como dueño, para que pueda tener asignaciones sin que se le calcule comisión.
10. Como administrador, quiero registrar trabajadores con un sueldo diario con vigencia, para calcular lo que ganan por día trabajado.
11. Como administrador, quiero desactivar a una persona que ya no trabaja, sin perder su historia.
12. Como administrador, quiero marcar varios trabajadores como presentes en un día con el monto precargado desde su sueldo vigente, para registrar el día rápido.
13. Como administrador, quiero editar el monto de un día trabajado, para reflejar un día pagado distinto al habitual.
14. Como administrador, quiero que el sistema impida registrar dos veces el mismo trabajador en la misma fecha, para no duplicar sueldos.
15. Como administrador, quiero asignar un bono a un guía o trabajador en un día, con nota opcional, para premiar a criterio.
16. Como administrador, quiero dar un bono a un guía en un día en que no llevó personas, porque a veces trabaja en otras tareas.
17. Como administrador, quiero que un bono ingresado después cuente en la fecha del día trabajado, para que los reportes reflejen cuándo se ganó.

### Ventas

18. Como administrador, quiero registrar una venta por reserva el día del buceo, con uno o más servicios y cantidad de personas por servicio, para saber qué se vendió.
19. Como administrador, quiero que el precio unitario se proponga desde el precio vigente a la fecha de la actividad, para no buscarlo a mano.
20. Como administrador, quiero que si cambio la fecha antes de guardar se recalculen los precios, para no guardar un precio de otra vigencia.
21. Como administrador, quiero que el precio de una venta guardada no cambie aunque después cambie el catálogo, para que las cifras históricas sean estables.
22. Como administrador, quiero indicar cuántas personas de cada servicio no se presentaron, para que no cuenten como atendidas ni generen comisión.
23. Como administrador, quiero que al ingresar inasistencias se proponga un abono retenido del 50 % de lo que pagaban esas personas, y poder editarlo, para registrar lo que efectivamente se quedó la escuela.
24. Como administrador, quiero registrar una venta en que nadie se presentó como una venta con todas sus personas inasistentes, para conservar el abono retenido como ingreso.
25. Como administrador, quiero registrar una cancelación anticipada solo cuando la escuela se quedó con el abono, en la fecha reservada, y no registrar nada si se devolvió.
26. Como administrador, quiero aplicar un descuento en pesos a una venta, que afecte solo a lo que pagan las personas que bucearon, para reflejar lo cobrado.
27. Como administrador, quiero calcular el descuento desde un porcentaje como ayuda y ajustar el monto, porque en la práctica se redondea.
28. Como administrador, quiero que todo descuento lleve un motivo y poder crear un motivo nuevo desde el formulario, para analizar después por qué se descuenta.
29. Como administrador, quiero que el sistema rechace un descuento mayor que el monto bruto de quienes bucearon, para no guardar ventas negativas.
30. Como administrador, quiero ver en vivo el monto bruto, el descuento, el neto, los abonos retenidos y el ingreso total mientras registro, para revisar antes de guardar.
31. Como administrador, quiero que una venta y sus ítems se guarden juntos o no se guarden, para no dejar ventas a medias.
32. Como administrador, quiero registrar una venta típica en menos de 30 segundos usando el teclado, porque registro muchas al final del día.
33. Como administrador, quiero registrar un curso de varios días una sola vez, en su fecha de inicio, para no contarlo dos veces.
34. Como administrador, quiero listar ventas filtrando por rango de fechas, servicio, con o sin inasistencias y con o sin descuento, para encontrar una venta.
35. Como administrador, quiero editar una venta y que se vuelvan a validar todas las reglas, para corregir errores sin romper los datos.
36. Como administrador, quiero eliminar una venta con confirmación, para no borrar por accidente.

### Día, asignaciones y cuadre

37. Como administrador, quiero ver en una sola pantalla las ventas de una fecha, las personas que bucearon por servicio, las asignaciones, los trabajadores y los bonos del día, para cerrar el día en un solo lugar.
38. Como administrador, quiero registrar cuántas personas de cada servicio llevó cada guía en el día, para calcular comisiones.
39. Como administrador, quiero asignar a un guía personas de varios servicios el mismo día, porque suele pasar.
40. Como administrador, quiero asignar a un guía cualquier cantidad de personas sin un tope fijo, porque depende del servicio y del día.
41. Como administrador, quiero asignar un curso al guía a cargo en su fecha de inicio, para que genere una sola comisión por alumno.
42. Como administrador, quiero ver por servicio si las personas asignadas cuadran con las que bucearon, para detectar errores de registro.
43. Como administrador, quiero poder guardar un día descuadrado con un aviso, porque a veces registro las ventas y las asignaciones en momentos distintos.
44. Como administrador, quiero ver un listado de días descuadrados en un rango, para corregirlos después.
45. Como administrador, quiero que el sistema rechace asignaciones con menos de una persona, a quien no es guía o sin comisión vigente.

### Costos y otros ingresos

46. Como administrador, quiero registrar un costo con fecha de pago, categoría, monto y descripción opcional, para saber cuánto cuesta operar.
47. Como administrador, quiero que existan las categorías fijas luz, agua y bencina, y poder crear otras desde el formulario, para clasificar imprevistos.
48. Como administrador, quiero que un costo cuente completo en su fecha de pago, sin repartirse entre días.
49. Como administrador, quiero listar costos por rango y categoría con su total.
50. Como administrador, quiero registrar otros ingresos con fecha, categoría y monto, creando categorías al momento, para contar lo que no es venta de buceo.
51. Como administrador, quiero desactivar una categoría o motivo con registros asociados en vez de borrarlo, para no perder historia.

### Remuneraciones

52. Como administrador, quiero ver para un rango cuánto ganó cada guía por día: personas por servicio, comisión aplicada, bonos y total, para saber cuánto pagarle.
53. Como administrador, quiero ver para un rango cuánto ganó cada trabajador por día: sueldo del día, bonos y total.
54. Como administrador, quiero ver totales por persona y un total general del período.
55. Como administrador, quiero que el dueño aparezca con comisión 0 aunque tenga asignaciones, pero con sus bonos si los tiene.

### Dashboard

56. Como administrador, quiero ver ventas por servicio en personas atendidas y en monto neto, agrupadas por día o mes, para saber qué se vende.
57. Como administrador, quiero que el descuento de una venta se reparta entre sus servicios proporcionalmente, de modo que la suma por servicio sea exactamente el neto cobrado.
58. Como administrador, quiero comparar el mismo rango de fechas en dos años distintos, en personas y en dinero, total y por servicio, con diferencia absoluta y porcentual, para comparar una temporada con la de otro año.
59. Como administrador, quiero ver el margen por servicio (ventas netas menos comisiones), para saber qué servicios convienen.
60. Como administrador, quiero ver ganancias por guía y trabajador por período.
61. Como administrador, quiero ver costos y otros ingresos por categoría y período.
62. Como administrador, quiero ver la utilidad por período (ventas netas, abonos retenidos y otros ingresos menos remuneraciones y costos), para saber si el negocio gana.
63. Como administrador, quiero ver inasistencias por período (personas, abono retenido y porcentaje sobre las vendidas), para evaluar mi política de abonos.
64. Como administrador, quiero ver descuentos por motivo y período, para saber cuánto me cuestan las promociones.
65. Como administrador, quiero atajos de rango (este mes, mes anterior, este año, año anterior, personalizado) y filtros por servicio, persona y categoría, para analizar rápido.
66. Como administrador, quiero que todas las cifras se imputen por la fecha del hecho y no por la fecha de ingreso, para que registrar tarde no distorsione los reportes.
67. Como administrador, quiero que el dashboard responda en menos de un segundo con diez años de datos.

### Respaldo

68. Como administrador, quiero elegir una carpeta de respaldo sincronizada con la nube, para no perder datos si se daña el computador.
69. Como administrador, quiero que la aplicación se respalde sola al cerrarse, para no tener que acordarme.
70. Como administrador, quiero un botón "Respaldar ahora".
71. Como administrador, quiero que se conserven los 30 respaldos más recientes con fecha y hora en el nombre.
72. Como administrador, quiero ver la fecha del último respaldo exitoso y un aviso si falló o si no hay carpeta configurada.
73. Como administrador, quiero que un fallo del respaldo nunca me impida cerrar la aplicación ni dañe la base principal.
74. Como administrador, quiero instrucciones escritas para restaurar un respaldo a mano.

### General

75. Como administrador, quiero la interfaz en español, fechas dd-mm-aaaa y montos en pesos sin decimales con separador de miles.
76. Como administrador, quiero usar la aplicación sin conexión a internet.
77. Como administrador, quiero poder editar o eliminar cualquier registro con confirmación, y que los cálculos siempre reflejen los datos actuales.
78. Como administrador, quiero que una actualización de la aplicación conserve todos mis datos.

## Implementation Decisions

- **Stack**: Tauri v2 con backend Rust; frontend React + TypeScript + Vite + Tailwind, con TanStack Query, React Router, React Hook Form + Zod y Recharts. SQLite local (rusqlite con `bundled` y `backup`) en la carpeta de datos de la aplicación, modo WAL, migraciones versionadas con rusqlite_migration. Todos los montos son enteros en pesos.
- **Capas del backend**: los comandos Tauri son envoltorios delgados. Toda la lógica vive en funciones de servicio que reciben una conexión SQLite y devuelven resultados o un error de dominio serializable. Esta es la única costura de pruebas.
- **Módulos de servicio**: catálogo (servicios, precios y comisiones con vigencias), personal (guías, dueño, trabajadores, sueldos con vigencias), ventas (guardar, calcular, listar, obtener, eliminar; motivos de descuento), día (obtener el día, guardar asignaciones, días descuadrados, días trabajados, bonos), costos, otros ingresos, reportes (remuneraciones y dashboard) y respaldo.
- **Vigencias**: un mecanismo común para precio, comisión y sueldo. El valor aplicable a una fecha es el de la vigencia más reciente con inicio ≤ fecha. Sin vigencia, la operación se rechaza con un error que pide definir el valor.
- **Venta**: tiene fecha de actividad, descuento en pesos, motivo (obligatorio si y solo si el descuento es > 0) y nota opcional. Cada ítem guarda servicio, cantidad, precio unitario (copia del precio vigente al guardar), inasistencias y abono retenido. Los montos de la venta (bruto, neto, abonos, ingreso) se derivan de los ítems y no se guardan. Venta e ítems se guardan en una transacción. Al editar se revalida todo.
- **Cálculo de una venta**: bucearon = cantidad − inasistencias; bruto = Σ bucearon × precio unitario; neto = bruto − descuento; ingreso = neto + Σ abonos retenidos. Restricciones: 0 ≤ descuento ≤ bruto; 0 ≤ inasistencias ≤ cantidad; un abono retenido > 0 requiere inasistencias. El abono propuesto es el 50 % de inasistencias × precio unitario, editable.
- **Reparto del descuento**: en los reportes por servicio, el descuento se reparte entre los ítems en proporción a su bruto, con el método del mayor resto. Así, la suma por servicio es exactamente el neto. Con bruto 0 no se reparte nada.
- **Asignaciones (ADR-0001)**: no existen salidas. Una asignación es (fecha, guía, servicio, cantidad ≥ 1), única por guía, servicio y día, sin tope de personas. Las asignaciones de un día se guardan como un reemplazo transaccional del conjunto completo. Validaciones: rol guía, comisión vigente a la fecha.
- **Cuadre**: por día y servicio, Σ asignadas vs. Σ bucearon. Un descuadre se informa (sobrante o faltante por servicio) pero no impide guardar. Un servicio con ventas y sin asignaciones, o al revés, también es descuadre.
- **Inasistencia (ADR-0002)**: no existe el estado "cancelada". Una venta sin nadie presente tiene todos sus ítems con inasistencias = cantidad.
- **Remuneraciones**: guía por día = Σ comisión vigente × asignadas + bonos; el dueño tiene comisión 0 y conserva sus bonos. Trabajador por día = monto del día trabajado + bonos. Un guía puede tener bono sin asignaciones. El sistema no registra si ya se pagó.
- **Días trabajados**: uno por trabajador y fecha; el monto se propone desde el sueldo vigente y se guarda editado.
- **Categorías**: categorías de costo (con marca de fija para luz, agua y bencina), categorías de otro ingreso y motivos de descuento. Todas se pueden crear desde el formulario que las usa y se desactivan, nunca se borran, si tienen registros.
- **Otro ingreso**: fecha, categoría y monto > 0, sin descripción. **Costo**: fecha de pago, categoría, monto > 0 y descripción opcional, sin prorrateo.
- **Fórmulas de resultados**: ingresos = ventas netas + abonos retenidos + otros ingresos; utilidad = ingresos − remuneraciones − costos; margen por servicio = ventas netas repartidas del servicio − comisiones de ese servicio. El abono retenido no entra en el margen por servicio. Todo se imputa por la fecha del hecho.
- **Reportes del dashboard**: ventas por servicio (personas y neto, por día o mes, con buckets vacíos), comparación del mismo rango en dos años (el rango se traslada al año de comparación conservando día y mes, puede cruzar el cambio de año y el 29-02 pasa a 28-02 en años no bisiestos), ganancias por persona, costos por categoría, otros ingresos por categoría, utilidad, margen por servicio, inasistencias y descuentos por motivo. La agrupación es solo diaria o mensual. Hay dos vistas SQL de apoyo: una por ítem de venta con sus montos derivados y otra de detalle de comisiones. Se agregan índices si la prueba de rendimiento lo exige.
- **Respaldo**: usa la API de backup en línea de SQLite hacia un archivo temporal en la carpeta configurada, luego `integrity_check`, renombrado final con fecha y hora, y rotación de las 30 versiones más recientes (configurable). Corre al cerrar, con overlay y timeout; la aplicación siempre cierra, sin reentrada y registrando el error. También hay respaldo manual y un indicador de estado en la barra lateral. La restauración es manual y queda documentada.
- **Frontend**: barra lateral con Ventas, Día, Servicios, Personal, Costos, Otros ingresos, Remuneraciones, Dashboard y Respaldo. Componentes reutilizables: editor de vigencias, selector creable, formulario de venta con ítems dinámicos y resumen en vivo, editor de asignaciones guía × servicio, cuadre por servicio y barra de filtros con atajos de rango. Los errores del backend se muestran como toast.
- **Datos iniciales**: una migración de semillas idempotente cargará los servicios, precios, comisiones, personal, categorías y motivos que entregue el administrador (pregunta abierta P-04). Mientras tanto se cargan solo las categorías de costo fijas.

## Testing Decisions

- **Una sola costura**: las pruebas llaman a las funciones de servicio del backend Rust sobre una base SQLite en memoria con todas las migraciones aplicadas. No se prueban detalles internos, como funciones privadas, el SQL generado o la forma de las tablas. Solo cuenta lo observable: lo que devuelven las funciones, los errores que rechazan operaciones inválidas y los totales de los reportes.
- **Una buena prueba** arma un escenario con el vocabulario del dominio (crear servicio, vigencias, venta, asignaciones), ejecuta la operación y verifica un resultado de negocio. Cada regla de validación tiene un caso aceptado y uno rechazado.
- **Cálculo de venta y reparto del descuento**: se prueban a través de guardar y obtener una venta y de los reportes por servicio, no por separado. Casos: todas las personas inasistentes; descuento igual al bruto; descuento mayor al bruto (rechazado); abono sin inasistencias (rechazado); bruto 0; un solo ítem; empates de resto; la suma por servicio siempre igual al neto.
- **Vigencias**: valor a una fecha exacta de inicio, entre dos vigencias, antes de la primera (rechazo). Cambiar el precio del catálogo no altera una venta guardada.
- **Asignaciones y cuadre**: cada validación de asignación; reemplazo del conjunto del día; sobrantes y faltantes por servicio; servicio vendido sin asignaciones y al revés.
- **Dataset de referencia**: unas dos semanas de datos con cambios de vigencia, inasistencias parciales y totales, descuentos sobre varios servicios, dueño con asignaciones, días descuadrados, bonos (incluido uno sin asignaciones), costos y otros ingresos. Los totales esperados se calculan a mano y quedan junto a la prueba. Valida remuneraciones y todos los reportes del dashboard.
- **Respaldo**: con un directorio temporal: se crea un archivo nuevo con nombre fechado, la rotación conserva N, una carpeta inexistente devuelve error sin dañar la base y no queda archivo temporal colgando.
- **Rendimiento**: una prueba con diez años de datos sintéticos verifica que cada reporte responde en menos de un segundo.
- **Frontend**: sin pruebas automáticas. Se valida a mano registrando una semana real y midiendo el tiempo por venta.
- **Prior art**: no hay código aún. Esta suite fija el patrón para el resto del proyecto.

## Out of Scope

- Salidas, botes y capacidad del bote (ADR-0001).
- Reservas o abonos registrados antes de la fecha de la actividad; estado de venta "cancelada" (ADR-0002).
- Datos de clientes.
- Registro de pagos efectivos al personal (solo se calcula cuánto ganó).
- Comisiones distintas por guía; reglas automáticas de bonos.
- Prorrateo de costos entre días o meses.
- Temporadas guardadas; la comparación usa un rango elegido a mano trasladado a otro año.
- Comparación entre dos rangos arbitrarios de distinta duración.
- Agrupación semanal o anual en el dashboard.
- Restauración de respaldos desde la aplicación.
- Multiusuario, sincronización en red, versión móvil o web, plataformas distintas de Windows.
- Facturación, impuestos o contabilidad formal.

## Further Notes

- Preguntas abiertas: P-02 (proveedor de nube para la carpeta de respaldo; no afecta el código) y P-04 (datos iniciales; bloquea solo la migración de semillas).
- Usar estrictamente los términos de `GLOSSARY.md`: guía (no buzo), inasistencia (no cancelación ni no-show), asignación (no salida), costo (no gasto), otro ingreso (no utilidad).
- La doble inmersión cuenta como un solo servicio.
- Primer hito útil: catálogos, ventas, día con asignaciones y cuadre, y respaldo, para empezar a ingresar datos reales mientras se construyen los reportes.
