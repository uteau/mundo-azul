# Mundo Azul — Documento de requerimientos funcionales y de negocio

| Campo | Valor |
|---|---|
| Versión | 2.1 |
| Fecha | 2026-11-27 |
| Fuente | Sesiones de definición del 2026-11-20 y del 2026-11-27 |
| Glosario | `GLOSSARY.md` (términos canónicos) |
| Decisiones de arquitectura | `docs/adr/` |
| Siguiente documento | `docs/02-diseno.md` |

---

## 1. Propósito y contexto

Mundo Azul es un centro de buceo que vende cursos y salidas. Un cliente reserva cupo para varias personas, cada una contrata un servicio, y paga un abono (normalmente 50 %). Cada día hay una o más salidas en bote; en ellas cada guía lleva una o varias personas bajo su cargo y gana una comisión por persona según el servicio.

El administrador necesita registrar, **a posteriori** (al final del día o de la semana), las ventas, las asignaciones de los guías, las remuneraciones, los costos y otros ingresos, y analizar la operación en un dashboard para tomar decisiones estratégicas: ventas por servicio, margen, comparación entre temporadas, ganancias del personal y utilidad.

El sistema **no** se usa durante la operación en terreno y **no** registra nada antes del día del buceo: es una herramienta de registro y análisis.

## 2. Alcance

### 2.1 Dentro del alcance

- Registro de ventas (una por reserva) con inasistencias y descuentos.
- Registro diario de asignaciones de guías por servicio.
- Catálogo de servicios con precios y comisiones históricos.
- Registro de personal (guías y trabajadores), con sueldos históricos, días trabajados y bonos.
- Registro de costos y otros ingresos por categoría.
- Dashboard con indicadores, comparaciones y filtros.
- Respaldo automático y manual de la base de datos.

### 2.2 Fuera del alcance (v1)

- Registro de reservas o abonos antes del día del buceo.
- Registro de salidas del bote, su capacidad u ocupación (ADR-0001).
- Datos personales de clientes (nombre, contacto, certificaciones).
- Control de pagos efectivos al personal (si ya se le pagó o no).
- Múltiples usuarios, roles o autenticación.
- Uso en línea, sincronización entre equipos o integraciones externas.
- Reportes exportables (PDF, Excel).
- Restauración de respaldos desde la aplicación (procedimiento manual).
- Importación de datos desde la aplicación: los datos históricos se cargan con una conversión única fuera de ella (D-21).
- Agrupación quincenal en el dashboard.
- Registro de temporadas con fechas fijas.
- Gestión de inventario o equipos.

## 3. Usuarios

| Usuario | Descripción |
|---|---|
| Administrador | Único usuario. Normalmente es el dueño. Ingresa todos los datos, corrige errores y consulta el dashboard desde su equipo personal Windows. |

## 4. Glosario

Los términos canónicos y los que se deben evitar están en `GLOSSARY.md`. Resumen: **Servicio**, **Curso**, **Venta**, **Descuento**, **Inasistencia**, **Abono retenido**, **Asignación**, **Vigencia**, **Guía**, **Dueño**, **Comisión**, **Trabajador**, **Bono**, **Remuneraciones**, **Otro ingreso**, **Costo**, **Margen por servicio**, **Utilidad**.

Términos derivados usados en este documento:

| Término | Definición |
|---|---|
| Personas que bucearon (de un ítem) | Cantidad del ítem − inasistencias. |
| Monto bruto (de una venta) | Σ personas que bucearon × precio unitario de cada ítem. |
| Monto neto (de una venta) | Monto bruto − descuento. |
| Ingreso (de una venta) | Monto neto + Σ abonos retenidos de sus ítems. |

## 5. Reglas de negocio

### 5.1 Ventas

| ID | Regla |
|---|---|
| RN-01 | Una venta corresponde a una reserva y pertenece a un solo día: la **fecha de la actividad**. Se registra ese día o después, nunca antes: una fecha posterior a hoy se rechaza. |
| RN-02 | Una venta contiene uno o más ítems; cada ítem indica un servicio y una cantidad de personas (≥ 1). Una venta tiene como máximo un ítem por servicio. |
| RN-03 | El precio unitario de cada ítem es el **precio vigente del servicio a la fecha de la actividad**; el administrador no lo edita (una rebaja se registra como descuento). Una vez guardado, queda fijo aunque luego cambie el catálogo. Al editar una venta guardada: los ítems que conservan su servicio conservan su precio; un ítem nuevo o con otro servicio toma el precio vigente; si cambia la fecha, todos los ítems toman el precio vigente a la nueva fecha, con aviso antes de guardar. |
| RN-04 | No se registran datos de clientes, solo cantidades de personas por servicio. |
| RN-05 | Un **curso** que dura varios días se registra una sola vez, en su fecha de inicio. |

### 5.2 Inasistencias y abonos retenidos (ADR-0002)

| ID | Regla |
|---|---|
| RN-06 | Cada ítem registra cuántas de sus personas no se presentaron (0 ≤ inasistencias ≤ cantidad). |
| RN-07 | Si un ítem tiene inasistencias, registra el **abono retenido** de esas personas. El sistema propone el 50 % de (inasistencias × precio unitario) y el administrador puede editarlo. Si no hay inasistencias, el abono retenido es 0. |
| RN-08 | Las inasistencias no generan comisión ni cuentan como personas atendidas. |
| RN-09 | Una venta en que no se presentó nadie es una venta con todas sus personas como inasistencias. No existe el estado "cancelada". |
| RN-10 | Una cancelación anticipada solo se registra si la escuela se queda con el abono: como venta con todas sus personas inasistentes, en la fecha que estaba reservada. Si el abono se devolvió, no se registra nada. |
| RN-11 | Fuera de las inasistencias no se registra abono: lo que pagan las personas que bucearon es parte del monto neto. |

### 5.3 Descuentos

| ID | Regla |
|---|---|
| RN-12 | Una venta tiene como máximo un descuento, guardado como **monto en pesos** (0 ≤ descuento ≤ monto bruto). |
| RN-13 | El descuento se aplica solo sobre lo que pagan las personas que bucearon; nunca sobre el abono retenido. El formulario puede calcularlo desde un porcentaje como ayuda, pero lo que se guarda es el monto. |
| RN-14 | Todo descuento > 0 tiene un **motivo** de una lista editable (fecha especial, promoción, etc.). Se pueden crear motivos nuevos al momento de registrar la venta. |
| RN-15 | El descuento no afecta las comisiones de los guías. |
| RN-16 | En los reportes por servicio, el descuento de una venta se reparte entre sus ítems en proporción al monto bruto de cada uno, de modo que la suma por servicio sea exactamente el monto neto. |

### 5.4 Asignaciones (ADR-0001)

| ID | Regla |
|---|---|
| RN-17 | Una asignación indica que un guía llevó N personas (≥ 1) de un servicio en un día. Hay como máximo una asignación por guía, servicio y día. |
| RN-18 | No hay tope de personas por guía. |
| RN-19 | Un guía puede tener asignaciones de varios servicios el mismo día. |
| RN-20 | **Cuadre del día**: para cada servicio, Σ personas asignadas debe ser igual a Σ personas que bucearon en las ventas de ese día. Si no coincide, el sistema avisa (día descuadrado) pero permite guardar. |
| RN-21 | Un curso se asigna al guía a cargo en su fecha de inicio; genera una sola comisión por alumno. |

### 5.5 Precios, comisiones y sueldos con histórico

| ID | Regla |
|---|---|
| RN-22 | Cada servicio tiene un precio por período de vigencia. Al cambiar el precio se crea una nueva vigencia; las anteriores se conservan. |
| RN-23 | Cada servicio tiene una comisión por persona, con la misma lógica de vigencias. La comisión es igual para todos los guías. Cada asignación conserva la comisión vigente a su fecha al registrarla; un cambio de comisión no altera asignaciones ya registradas (ADR-0003). Al volver a guardar un día, una asignación de un guía y servicio que ya existía conserva su comisión. |
| RN-24 | Cada trabajador tiene un sueldo diario propio, con la misma lógica de vigencias. |
| RN-25 | El valor aplicable a una fecha es el de la vigencia más reciente cuya fecha de inicio sea ≤ a esa fecha. Si no existe ninguna, el sistema impide registrar la operación y pide definir el valor. Una vigencia puede empezar en el pasado o en el futuro, y se puede corregir o eliminar: solo afecta los registros que se hagan después. |

### 5.6 Remuneraciones

| ID | Regla |
|---|---|
| RN-26 | Pago de un guía en un día = Σ (comisión vigente del servicio × personas asignadas ese día) + bonos de ese día. |
| RN-27 | Al dueño no se le calcula pago (siempre 0), aunque tenga asignaciones, y no recibe bonos. Hay como máximo un dueño, y solo se puede marcar o desmarcar como dueño a un guía sin asignaciones ni bonos. |
| RN-28 | Pago de un trabajador en un día = monto del día trabajado + bonos de ese día. |
| RN-29 | Un trabajador tiene como máximo un registro de día trabajado por fecha. El registro guarda el monto pagado ese día, propuesto desde el sueldo vigente y editable. |
| RN-30 | Los bonos son manuales, sin reglas automáticas. Cada bono se asocia a una persona y a un día, y se imputa en esa fecha aunque se ingrese después. Un guía puede recibir bono en un día sin asignaciones. El dueño no recibe bonos (RN-27). |
| RN-31 | El sistema calcula cuánto ganó cada guía y trabajador; no registra si ya se le pagó. |
| RN-32 | Un trabajador no actúa como guía y viceversa; si ocurre, se registra como dos personas distintas. El rol se fija al crear la persona y no cambia. |

### 5.7 Costos y otros ingresos

| ID | Regla |
|---|---|
| RN-33 | Los costos son gastos generales del negocio, no asociados a ventas. |
| RN-34 | Existen categorías de costo fijas (luz, agua, bencina). El administrador puede crear categorías nuevas en cualquier momento, incluso desde el formulario de registro. |
| RN-35 | Cada costo se ingresa con fecha de pago, categoría, monto (> 0) y descripción opcional. No se reparte entre días. |
| RN-36 | Un otro ingreso se ingresa con fecha, categoría (arriendo de equipos, carga de botellas, etc.) y monto (> 0). Las categorías se pueden crear al momento. |
| RN-37 | Una categoría (de costo, de otro ingreso o motivo de descuento) con registros asociados no se elimina; se puede desactivar. |

### 5.8 Utilidad

| ID | Regla |
|---|---|
| RN-38 | Ingresos de un período = Σ montos netos de ventas + Σ abonos retenidos + Σ otros ingresos. |
| RN-39 | Utilidad de un período = ingresos − remuneraciones − costos. |
| RN-40 | Margen por servicio = ventas netas del servicio (con el descuento repartido, RN-16) − comisiones pagadas por ese servicio. |
| RN-41 | Todas las cifras se imputan por fecha de actividad, de asignación, de día trabajado, de bono, de costo o de otro ingreso; nunca por fecha de ingreso al sistema. |

### 5.9 Correcciones

| ID | Regla |
|---|---|
| RN-42 | El administrador puede editar o eliminar cualquier registro. Las eliminaciones piden confirmación. Un servicio, persona, categoría o motivo sin registros asociados se elimina (con sus vigencias); con registros, solo se desactiva. Las categorías de costo fijas no se eliminan ni se renombran, pero se pueden desactivar. |
| RN-43 | Toda edición vuelve a validar las reglas de negocio. Los cálculos siempre reflejan los datos actuales. |
| RN-44 | Un elemento desactivado (servicio, persona, categoría, motivo) no se ofrece en registros nuevos, pero se conserva en los registros existentes, que se pueden editar y guardar sin cambiarlo. |
| RN-45 | Ninguna asignación, día trabajado, bono, costo u otro ingreso se registra con fecha posterior a hoy. Las vigencias sí pueden tener fecha futura. |

## 6. Requerimientos funcionales

### 6.1 Catálogo de servicios

| ID | Requerimiento | Prioridad |
|---|---|---|
| RF-01 | Crear, editar, desactivar y eliminar servicios (nombre, estado activo). | Alta |
| RF-02 | Gestionar el histórico de precios de cada servicio: agregar vigencia (fecha desde, precio), ver y corregir. | Alta |
| RF-03 | Gestionar el histórico de comisiones por servicio, con la misma interfaz. | Alta |
| RF-04 | Mostrar el precio y la comisión vigentes hoy en el listado de servicios. | Media |

### 6.2 Personal

| ID | Requerimiento | Prioridad |
|---|---|---|
| RF-05 | Crear, editar, desactivar y eliminar guías. Marcar a un guía como dueño (RN-27). | Alta |
| RF-06 | Crear, editar, desactivar y eliminar trabajadores. | Alta |
| RF-07 | Gestionar el histórico de sueldo diario de cada trabajador. | Alta |
| RF-08 | Registrar días trabajados, individualmente o marcando varios trabajadores para un mismo día. Monto propuesto desde el sueldo vigente y editable. Impedir duplicados por trabajador y fecha. | Alta |
| RF-09 | Registrar bonos para guías (salvo el dueño) o trabajadores: persona, fecha, monto y nota opcional. | Alta |

### 6.3 Ventas

| ID | Requerimiento | Prioridad |
|---|---|---|
| RF-10 | Registrar una venta: fecha de actividad, ítems (servicio, cantidad, inasistencias, abono retenido), descuento opcional con motivo, nota opcional. Mostrar en tiempo real monto bruto, descuento, monto neto, abonos retenidos e ingreso. | Alta |
| RF-11 | Proponer el precio vigente de cada servicio a la fecha de la actividad (recalcular si cambia la fecha antes de guardar) y el abono retenido al 50 % al ingresar inasistencias. | Alta |
| RF-12 | Ayuda de descuento por porcentaje: al ingresar un %, proponer el monto sobre el monto bruto; el administrador lo ajusta. | Media |
| RF-13 | Crear un motivo de descuento nuevo desde el formulario de venta. | Alta |
| RF-14 | Listar ventas con filtros por rango de fechas, servicio, con/sin inasistencias y con/sin descuento. | Alta |
| RF-15 | Editar o eliminar (con confirmación) cualquier venta. | Alta |

### 6.4 Asignaciones y cuadre del día

| ID | Requerimiento | Prioridad |
|---|---|---|
| RF-16 | Vista diaria: para una fecha, mostrar las ventas, las personas que bucearon por servicio, las asignaciones por guía y el cuadre por servicio. | Alta |
| RF-17 | Registrar, editar y eliminar asignaciones del día (guía, servicio, personas). | Alta |
| RF-18 | Validar: personas ≥ 1, rol guía, una asignación por guía/servicio/día, comisión vigente. | Alta |
| RF-19 | Indicar visualmente los días descuadrados (en la vista diaria y en un listado de días). | Media |

### 6.5 Costos y otros ingresos

| ID | Requerimiento | Prioridad |
|---|---|---|
| RF-20 | Gestionar categorías de costos: precargar las fijas, crear, renombrar y desactivar. | Alta |
| RF-21 | Registrar, editar y eliminar costos (fecha, categoría, monto, descripción). Crear categoría desde el formulario. | Alta |
| RF-22 | Listar costos con filtros por rango de fechas y categoría, con total. | Alta |
| RF-23 | Gestionar categorías de otros ingresos y registrar, editar, eliminar y listar otros ingresos (fecha, categoría, monto), con creación de categoría desde el formulario. | Alta |

### 6.6 Remuneraciones

| ID | Requerimiento | Prioridad |
|---|---|---|
| RF-24 | Ver, para un rango de fechas, cuánto ganó cada guía por día: personas por servicio, comisión aplicada, bonos y total. | Alta |
| RF-25 | Ver, para un rango de fechas, cuánto ganó cada trabajador por día: sueldo del día, bonos y total. | Alta |
| RF-26 | Mostrar totales por persona y total general del período. | Alta |

### 6.7 Dashboard

| ID | Requerimiento | Prioridad |
|---|---|---|
| RF-27 | Ventas por servicio (personas atendidas y monto neto) agrupadas por día o mes. | Alta |
| RF-28 | Comparación del mismo rango de fechas en dos años distintos (p. ej. 15-12 al 28-02 de una temporada contra el mismo tramo de otra), en personas atendidas y en dinero, total y por servicio, con diferencia absoluta y porcentual. El año de comparación corresponde al inicio del rango, y ambos extremos se trasladan la misma cantidad de años; el rango puede cruzar el cambio de año; el 29-02 se toma como 28-02 en años no bisiestos. La variación es (rango elegido − rango de comparación) / rango de comparación; con base 0 se muestra "sin base". | Alta |
| RF-29 | Ganancias de cada guía y trabajador por período. | Alta |
| RF-30 | Costos por período y por categoría. | Alta |
| RF-31 | Utilidad por período: ingresos (ventas netas, abonos retenidos, otros ingresos), remuneraciones, costos y utilidad. | Alta |
| RF-32 | Margen por servicio por período. | Alta |
| RF-33 | Otros ingresos por categoría y período. | Alta |
| RF-34 | Inasistencias por período: personas, abono retenido y % sobre personas vendidas. | Media |
| RF-35 | Descuentos por motivo y período. | Media |
| RF-36 | Filtros comunes: rango de fechas (atajos: este mes, mes anterior, este año, año anterior, rango personalizado), servicio, persona y categoría, según el indicador. | Alta |

### 6.8 Respaldo

| ID | Requerimiento | Prioridad |
|---|---|---|
| RF-37 | Configurar la carpeta de respaldo (típicamente sincronizada con OneDrive o Google Drive). | Alta |
| RF-38 | Respaldo automático al cerrar la aplicación. | Alta |
| RF-39 | Botón de respaldo manual. | Alta |
| RF-40 | Cada respaldo genera un archivo con fecha y hora en el nombre; se conservan las N versiones más recientes (por defecto 30). | Alta |
| RF-41 | La copia usa la API de backup en línea de SQLite. | Alta |
| RF-42 | Mostrar la fecha del último respaldo exitoso y avisar si el último intento falló o si no hay carpeta configurada. | Media |

## 7. Requerimientos no funcionales

| ID | Requerimiento |
|---|---|
| RNF-01 | Aplicación de escritorio para Windows 10/11, instalable, de un solo usuario. |
| RNF-02 | Funciona 100 % sin conexión a internet. |
| RNF-03 | Stack: Tauri + React + Tailwind CSS; base de datos SQLite local. |
| RNF-04 | Interfaz en español. Fechas `dd-mm-aaaa`; montos en pesos sin decimales con separador de miles. |
| RNF-05 | Los montos se almacenan como enteros. |
| RNF-06 | Integridad: una venta con sus ítems se guarda de forma atómica. |
| RNF-07 | El dashboard responde en menos de 1 segundo con 10 años de datos (< 50.000 ventas). |
| RNF-08 | La base de datos vive en la carpeta de datos de la aplicación, nunca en la carpeta sincronizada. |
| RNF-09 | Un fallo del respaldo nunca impide cerrar la aplicación ni corrompe la base principal. |
| RNF-10 | Las actualizaciones de esquema se aplican con migraciones versionadas sin pérdida de datos. |
| RNF-11 | Registro ágil: una venta típica (1–3 ítems) en menos de 30 segundos, con navegación por teclado. |

## 8. Supuestos

| ID | Supuesto |
|---|---|
| S-01 | La moneda es el peso chileno (CLP), sin decimales. |
| S-02 | "Precios año a año" se modela con fecha de inicio de vigencia, para soportar temporadas que cruzan el cambio de año. |
| S-03 | Servicios de varias inmersiones el mismo día (doble inmersión) cuentan como un solo servicio con un guía a cargo. |

## 9. Decisiones y preguntas abiertas

### 9.1 Decisiones tomadas

| ID | Fecha | Decisión |
|---|---|---|
| D-01 | 2026-11-20 | El dashboard agrupa solo por día y por mes. |
| D-02 | 2026-11-20 | La restauración de un respaldo es manual y se documenta. |
| D-03 | 2026-11-20 | Los datos iniciales (servicios, precios, comisiones, personal, categorías) los entrega el administrador más adelante. |
| D-04 | 2026-11-20 | No se guardan fechas de temporadas; la comparación usa un rango elegido a mano y lo traslada a otro año (RF-28). |
| D-05 | 2026-11-20 | La doble inmersión cuenta como un solo servicio (S-03). |
| D-06 | 2026-11-20 | No se registran salidas; la asignación es diaria por guía y servicio, sin tope (ADR-0001). |
| D-07 | 2026-11-20 | La inasistencia por ítem reemplaza la cancelación de ventas; solo se registra el abono retenido (ADR-0002). |
| D-08 | 2026-11-20 | El descuento se guarda como monto con motivo; el porcentaje es solo una ayuda. |
| D-09 | 2026-11-20 | Los otros ingresos son un registro aparte, con categoría y sin descripción. |
| D-10 | 2026-11-20 | Un curso se registra y comisiona una sola vez, en su fecha de inicio. |
| D-11 | 2026-11-20 | Un guía puede recibir bono en un día sin asignaciones (cierra P-06). |
| D-12 | 2026-11-27 | El dueño no gana comisión ni bonos; hay como máximo uno y la marca solo cambia en un guía sin asignaciones ni bonos (RN-27). |
| D-13 | 2026-11-27 | El precio unitario no se edita; al editar una venta, los ítems conservan su precio salvo que cambie su servicio o la fecha de la venta (RN-03). Un ítem por servicio (RN-02). |
| D-14 | 2026-11-27 | La comisión queda fija en cada asignación al registrarla; las vigencias se corrigen o eliminan libremente y solo afectan registros nuevos (ADR-0003, RN-23, RN-25). |
| D-15 | 2026-11-27 | Comparación entre años: el año de comparación es el del inicio del rango y es la base de la variación (RF-28). |
| D-16 | 2026-11-27 | No se registran hechos con fecha futura; las vigencias sí (RN-01, RN-45). |
| D-17 | 2026-11-27 | El rol de una persona no cambia (RN-32). |
| D-18 | 2026-11-27 | Sin registros asociados se elimina; con registros se desactiva; los elementos inactivos se conservan en registros existentes (RN-42, RN-44). |
| D-19 | 2026-11-27 | Hay un instalador del primer hito para usar la aplicación con datos reales mientras se construyen los reportes. |
| D-20 | 2026-11-27 | Los datos iniciales vienen dentro del instalador y se cargan una sola vez, al crear la base por primera vez; nunca al actualizar. El archivo real no se versiona; el repositorio solo tiene un ejemplo ficticio. |
| D-21 | 2026-11-27 | Los datos históricos se cargan con una conversión única fuera de la aplicación: hacia una base nueva si llegan antes del primer uso, o hacia una copia respaldada de la base en uso si llegan después. Se importan los valores realmente cobrados y pagados, no se inventa información faltante y los datos no se versionan. |

### 9.2 Preguntas abiertas

| ID | Pregunta | Impacto |
|---|---|---|
| P-02 | Proveedor de nube para el respaldo (OneDrive o Google Drive). | Ninguno en el código. |
| P-04 | Datos iniciales a precargar (D-03, D-20). | Bloquea el instalador del primer hito. |
| P-07 | Volumen y formato de los datos históricos (D-21). | Bloquea la conversión de datos históricos. |

## 10. Criterios de aceptación generales

1. Con un conjunto de datos de prueba conocido, los totales de ingresos, remuneraciones, costos, utilidad y margen por servicio del dashboard coinciden con el cálculo manual.
2. Cambiar un precio o comisión con una nueva vigencia no altera los montos de ventas ni pagos anteriores a esa vigencia.
3. Una venta con inasistencias aporta como ingreso el monto neto de quienes bucearon más el abono retenido, y no genera comisión por las inasistencias.
4. Un día donde las asignaciones no suman las personas que bucearon por servicio se señala como descuadrado.
5. La suma de ventas netas por servicio de un período es igual a la suma de montos netos de las ventas del período.
6. Al cerrar la aplicación con una carpeta de respaldo configurada, aparece un archivo nuevo con fecha y hora, y se conservan como máximo N versiones.
7. La aplicación funciona con el equipo desconectado de internet.
