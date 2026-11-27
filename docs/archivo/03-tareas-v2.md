> **Archivado (2026-11-20).** Esta lista por capas fue reemplazada por los tickets de `.scratch/mundoazul-v1/issues/`, indexados en `docs/03-tareas.md`. Se conserva solo como historia; no se actualiza. Sus pruebas de dominio puro (2.1, 2.2) y Vitest (1.7) quedaron descartadas a favor de la costura única del spec.

# Mundo Azul — Lista de tareas

| Campo | Valor |
|---|---|
| Versión | 2.0 |
| Fecha | 2026-11-20 |
| Requerimientos | `docs/01-requerimientos.md` |
| Diseño | `docs/02-diseno.md` |

Convenciones: cada tarea indica los requerimientos que cubre y su criterio de terminado ("Listo cuando"). Las tareas de backend se desarrollan con pruebas primero. `→` indica dependencias.

---

## Fase 0 — Definiciones pendientes

- [x] ~~**0.1** Resolver P-01, P-03, P-05 y P-06~~ → decisiones D-01 a D-11 en `01-requerimientos.md` §9.1, ADR-0001 y ADR-0002.
- [ ] **0.2** Recibir los datos iniciales del administrador (P-04).
  - Listo cuando: existe la lista de servicios, precios, comisiones, personal, categorías de costo, categorías de otros ingresos y motivos de descuento iniciales.

## Fase 1 — Base del proyecto

- [ ] **1.1** Crear proyecto Tauri v2 + React + TypeScript + Vite + Tailwind; `.gitignore`, ESLint, Prettier.
  - Listo cuando: `npm run tauri dev` abre la ventana con una página de prueba estilizada con Tailwind.
- [ ] **1.2** Dependencias Rust: `rusqlite` (`bundled`, `backup`), `rusqlite_migration`, `serde`, `thiserror`, `chrono`, `tauri-plugin-dialog`. → 1.1
- [ ] **1.3** Módulo `db`: abrir/crear la base en `app_data_dir`, PRAGMAs, `Mutex<Connection>` en el estado. → 1.2 · RNF-08
  - Listo cuando: al iniciar la app se crea `mundoazul.sqlite` en AppData.
- [ ] **1.4** Migración `001_inicial.sql` con el esquema completo (diseño §3.2) y prueba que aplica migraciones sobre una base en memoria. → 1.3 · RNF-10
- [ ] **1.5** `AppError` serializable y wrapper `lib/api.ts` de `invoke` que muestra errores en un toast. → 1.1
- [ ] **1.6** Layout base: barra lateral, rutas vacías, componentes UI base (incluido `CreatableSelect`). → 1.1 · RNF-04
- [ ] **1.7** Utilidades `format.ts` y `dates.ts` con pruebas Vitest. → 1.1 · RNF-04

## Fase 2 — Dominio puro

- [ ] **2.1** `domain::venta::calcular` (bucearon, bruto, neto, abonos, ingreso, abono propuesto al 50 %) con pruebas de bordes (todas inasistentes, descuento = bruto, descuento > bruto → error, abono sin inasistencias → error). · RN-06, RN-07, RN-12, RN-13
- [ ] **2.2** `domain::venta::repartir_descuento` por mayor resto, con pruebas: la suma siempre es el descuento; bruto 0; un solo ítem; empates de resto. · RN-16
- [ ] **2.3** Repositorio genérico de **vigencias** (valor vigente a una fecha, listar, guardar, eliminar) para precios, comisiones y sueldos, con pruebas. → 1.4 · RN-22–RN-25
- [ ] **2.4** `domain::periodo` (claves diario/mensual y buckets vacíos para gráficos) con pruebas. · RF-27

## Fase 3 — Catálogos

- [ ] **3.1** Backend servicios: CRUD, activar/desactivar, precios y comisiones con vigencias, `precio_vigente`. → 2.3 · RF-01–RF-04
- [ ] **3.2** Pantalla Servicios con `VigenciasEditor` reutilizable. → 1.6, 3.1
  - Listo cuando: se crea un servicio, se le agregan dos vigencias de precio y la tabla muestra el precio vigente hoy.
- [ ] **3.3** Backend personal: CRUD de guías y trabajadores, marca de dueño, sueldos con vigencias. → 2.3 · RF-05–RF-07
- [ ] **3.4** Pantalla Personal (pestañas Guías / Trabajadores, editor de sueldos). → 1.6, 3.3
- [ ] **3.5** Backend costos: categorías (fijas y nuevas, desactivar) y CRUD de costos con filtros y total. → 1.4 · RF-20–RF-22, RN-33–RN-35, RN-37
- [ ] **3.6** Pantalla Costos con creación de categoría desde el formulario. → 1.6, 3.5
- [ ] **3.7** Backend y pantalla de **otros ingresos** con categorías creables al momento. → 1.4, 1.6 · RF-23, RN-36, RN-37
- [ ] **3.8** Migración `003_semillas.sql` con los datos iniciales de 0.2 (idempotente). → 0.2, 1.4

## Fase 4 — Ventas

- [ ] **4.1** Backend `venta_guardar` (venta + ítems en una transacción, snapshot de precio vigente, validación de vigencia, descuento ≤ bruto, motivo obligatorio con descuento) y `venta_calcular`. → 2.1, 3.1 · RF-10, RF-11, RNF-06
  - Listo cuando: pruebas verifican que cambiar el precio del catálogo no altera ventas guardadas y que editar inasistencias revalida el descuento.
- [ ] **4.2** Backend motivos de descuento (listar, crear, desactivar). → 1.4 · RF-13, RN-14, RN-37
- [ ] **4.3** Backend `ventas_listar` (rango, servicio, con inasistencias, con descuento), `venta_obtener`, `venta_eliminar`. → 4.1 · RF-14, RF-15
- [ ] **4.4** `VentaForm`: ítems dinámicos con inasistencias y abono retenido propuesto, descuento con ayuda de % y motivo creable, resumen en vivo (bruto, descuento, neto, abonos, ingreso), navegación por teclado. → 1.6, 4.1, 4.2 · RF-10–RF-13, RNF-11
- [ ] **4.5** Pantalla Ventas: listado con filtros y acciones editar/eliminar. → 4.3, 4.4 · RF-14, RF-15

## Fase 5 — Día, asignaciones y cuadre

- [ ] **5.1** Backend `dia_obtener(fecha)`: ventas, personas que bucearon por servicio, asignaciones, descuadres, días trabajados y bonos. → 4.3 · RF-16
- [ ] **5.2** Backend `asignaciones_guardar(fecha, filas)` (reemplazo transaccional; rol guía, una fila por guía/servicio, cantidad ≥ 1, comisión vigente) y `dias_descuadrados(rango)`. → 5.1 · RF-17–RF-19, RN-17–RN-21
  - Listo cuando: pruebas cubren cada validación con un caso válido y uno rechazado, y el cuadre detecta sobrantes y faltantes por servicio.
- [ ] **5.3** Pantalla Día: selector de fecha, ventas del día, `AsignacionesEditor` (guía × servicio), `CuadreServicios` con descuadres resaltados. → 4.5, 5.2 · RF-16–RF-19
- [ ] **5.4** Sección de trabajadores en Día: marcar quiénes trabajaron (monto precargado y editable), sin duplicados. → 3.3, 5.3 · RF-08, RN-29
- [ ] **5.5** Bonos: backend CRUD, sección de bonos del día en Día y listado general. → 3.3, 5.3 · RF-09, RN-30

## Fase 6 — Remuneraciones y reportes

- [ ] **6.1** Migración `002_vistas.sql` (`v_venta_item`, `v_comision_detalle`) y reportes `remuneraciones_guias` / `remuneraciones_trabajadores` con desglose. → 5.2, 5.4, 5.5 · RF-24–RF-26, RN-26–RN-28, RN-31
- [ ] **6.2** **Dataset de referencia**: ~2 semanas con cambios de vigencia, inasistencias parciales y totales, descuentos con varios servicios, dueño con asignaciones, días descuadrados, bonos, costos y otros ingresos; totales esperados calculados a mano junto al test. → 6.1
- [ ] **6.3** Pantalla Remuneraciones: rango, tabla por persona y día con desglose, totales. → 6.1
- [ ] **6.4** Reportes `dash_ventas_por_servicio` (con reparto de descuento), `dash_ganancias_personal`, `dash_costos`, `dash_otros_ingresos`, `dash_utilidad`, `dash_margen_servicio`, validados contra el dataset. → 2.2, 2.4, 6.1, 6.2 · RF-27, RF-29–RF-33, RN-38–RN-41
- [ ] **6.5** Reportes `dash_inasistencias` y `dash_descuentos`. → 6.4 · RF-34, RF-35
- [ ] **6.6** Reporte `dash_comparacion(rango, anio_comparacion)`: el mismo rango trasladado a otro año (cruce de año y 29-02 incluidos), por servicio en personas y dinero, con diferencia y variación %. → 6.4 · RF-28

## Fase 7 — Dashboard

- [ ] **7.1** `FiltrosBar` con atajos de rango, período y filtros contextuales. → 1.7 · RF-36
- [ ] **7.2** Tarjetas KPI y gráfico de utilidad. → 6.4, 7.1 · RF-31
- [ ] **7.3** Ventas por servicio (personas / dinero) y margen por servicio. → 6.4, 7.1 · RF-27, RF-32
- [ ] **7.4** Comparador entre años. → 6.6, 7.1 · RF-28
- [ ] **7.5** Ganancias por persona, costos por categoría y otros ingresos por categoría. → 6.4, 7.1 · RF-29, RF-30, RF-33
- [ ] **7.6** Inasistencias y descuentos por motivo. → 6.5, 7.1 · RF-34, RF-35
- [ ] **7.7** Verificación de rendimiento con 10 años de datos sintéticos (< 1 s por reporte); índices si hace falta. → 6.4 · RNF-07

## Fase 8 — Respaldo

- [ ] **8.1** Módulo `backup`: copia a `.tmp`, `integrity_check`, renombrar, rotación; pruebas con directorio temporal. → 1.3 · RF-40, RF-41
- [ ] **8.2** Configuración y ejecución manual; pantalla Respaldo; estado en la barra lateral y "Respaldar ahora". → 8.1, 1.6 · RF-37, RF-39, RF-42
- [ ] **8.3** Respaldo al cerrar con overlay, timeout y salida siempre, sin reentrada. → 8.1 · RF-38, RNF-09
  - Listo cuando: cerrar con carpeta válida crea un archivo nuevo; con carpeta inexistente cierra igual y registra el error.
- [ ] **8.4** Documentar la restauración manual en `docs/restauracion.md`. → 8.1

## Fase 9 — Cierre y entrega

- [ ] **9.1** Revisión de UX de punta a punta: registrar una semana real a posteriori, medir tiempo por venta, ajustar atajos y foco. · RNF-11
- [ ] **9.2** Configurar `tauri.conf.json` y generar el instalador en Windows. · RNF-01
- [ ] **9.3** Prueba de aceptación en el equipo del administrador: instalación, uso sin internet, respaldo real, los 7 criterios de aceptación generales. · RNF-02
- [ ] **9.4** Guía breve de uso (`docs/manual.md`): flujo diario, inasistencias, cuadre, correcciones, respaldo y restauración.

---

## Ruta crítica sugerida

1.1 → 1.3 → 1.4 → 2.3 → 3.1 → 4.1 → 5.1 → 5.2 → 6.1 → 6.2 → 6.4 → 7.2 → 8.3 → 9.2 → 9.3

Primer hito útil (MVP de registro): fases 1 a 5 más 8.1/8.2, para ingresar datos reales y respaldarlos mientras se construyen los reportes.
