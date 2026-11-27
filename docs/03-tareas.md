# Mundo Azul — Tareas

| Campo | Valor |
|---|---|
| Versión | 3.1 |
| Fecha | 2026-11-27 |
| Requerimientos | `docs/01-requerimientos.md` |
| Diseño | `docs/02-diseno.md` |
| Spec | `.scratch/mundoazul-v1/spec.md` |
| Tickets | `.scratch/mundoazul-v1/issues/` |
| Versión anterior | `docs/archivo/03-tareas-v2.md` (por capas, archivada) |

Este documento es el índice de los tickets. Cada ticket es una tajada vertical que atraviesa esquema, backend, pantalla y pruebas, así que se puede probar por sí solo. El detalle (qué construir y criterios de aceptación) y el **estado** viven en el archivo de cada ticket; si este índice y un ticket no coinciden, manda el ticket.

Las pruebas usan una sola costura: las funciones de servicio en Rust sobre una base SQLite en memoria con las migraciones aplicadas. El frontend se valida a mano.

---

## Tickets

| # | Ticket | Bloqueado por | Cubre |
|---|---|---|---|
| 01 | [Esqueleto de la app](../.scratch/mundoazul-v1/issues/01-esqueleto-de-la-app.md) | — | RNF-01–05, RNF-08, RNF-10 |
| 02 | [Servicios con precios y comisiones](../.scratch/mundoazul-v1/issues/02-servicios-con-precios-y-comisiones.md) | 01 | RF-01–04, RN-22, RN-23, RN-25 |
| 03 | [Personal](../.scratch/mundoazul-v1/issues/03-personal.md) | 02 | RF-05–07, RN-24, RN-32 |
| 04 | [Venta simple](../.scratch/mundoazul-v1/issues/04-venta-simple.md) | 02 | RF-10, RF-11, RF-14, RF-15, RN-01–05, RNF-06, RNF-11 |
| 05 | [Inasistencias y abono retenido](../.scratch/mundoazul-v1/issues/05-inasistencias-y-abono-retenido.md) | 04 | RN-06–11 (ADR-0002) |
| 06 | [Descuento con motivo](../.scratch/mundoazul-v1/issues/06-descuento-con-motivo.md) | 05, 09 | RF-12, RF-13, RN-12–15 |
| 07 | [Día: asignaciones y cuadre](../.scratch/mundoazul-v1/issues/07-dia-asignaciones-y-cuadre.md) | 03, 05 | RF-16–19, RN-17–21, RN-23 (ADR-0001, ADR-0003) |
| 08 | [Días trabajados y bonos](../.scratch/mundoazul-v1/issues/08-dias-trabajados-y-bonos.md) | 07 | RF-08, RF-09, RN-27, RN-29, RN-30 |
| 09 | [Costos](../.scratch/mundoazul-v1/issues/09-costos.md) | 01 | RF-20–22, RN-33–35, RN-37 |
| 10 | [Otros ingresos](../.scratch/mundoazul-v1/issues/10-otros-ingresos.md) | 09 | RF-23, RN-36, RN-37 |
| 11 | [Remuneraciones](../.scratch/mundoazul-v1/issues/11-remuneraciones.md) | 06, 08 | RF-24–26, RN-26–28, RN-31 |
| 12 | [Dataset de referencia](../.scratch/mundoazul-v1/issues/12-dataset-de-referencia.md) | 05, 06, 10, 11 | Valida RN-26–41 |
| 13 | [Dashboard: filtros y utilidad](../.scratch/mundoazul-v1/issues/13-dashboard-filtros-y-utilidad.md) | 12 | RF-31, RF-36, RN-38, RN-39, RN-41 |
| 14 | [Ventas y margen por servicio](../.scratch/mundoazul-v1/issues/14-ventas-y-margen-por-servicio.md) | 13 | RF-27, RF-32, RN-16, RN-40 |
| 15 | [Comparador entre años](../.scratch/mundoazul-v1/issues/15-comparador-entre-anios.md) | 14 | RF-28 |
| 16 | [Ganancias, costos y otros ingresos](../.scratch/mundoazul-v1/issues/16-ganancias-costos-y-otros-ingresos.md) | 13 | RF-29, RF-30, RF-33 |
| 17 | [Inasistencias y descuentos](../.scratch/mundoazul-v1/issues/17-inasistencias-y-descuentos.md) | 13 | RF-34, RF-35 |
| 18 | [Rendimiento con 10 años de datos](../.scratch/mundoazul-v1/issues/18-rendimiento-con-10-anios.md) | 14, 15, 16, 17 | RNF-07 |
| 19 | [Respaldo manual](../.scratch/mundoazul-v1/issues/19-respaldo-manual.md) | 01 | RF-37, RF-39–42, D-02 |
| 20 | [Respaldo al cerrar](../.scratch/mundoazul-v1/issues/20-respaldo-al-cerrar.md) | 19 | RF-38, RNF-09 |
| 21 | [Entrega final y manual de uso](../.scratch/mundoazul-v1/issues/21-entrega-final-y-manual.md) | 18, 22 | RNF-01, RNF-02, criterios generales (`ready-for-human`) |
| 22 | [Datos iniciales e instalador del primer hito](../.scratch/mundoazul-v1/issues/22-datos-iniciales-e-instalador-del-primer-hito.md) | 06, 08, 10, 20, datos de P-04 | D-19, D-20, P-04 |
| 23 | [Conversión de datos históricos](../.scratch/mundoazul-v1/issues/23-conversion-de-datos-historicos.md) | 22, datos de P-07 | D-21, P-07 (`needs-info`) |

## Cómo avanzar

Se puede tomar cualquier ticket cuyos bloqueadores estén todos terminados. Al terminar uno, se marca su `Status:` en el archivo del ticket.

Un ticket terminado queda con `Status: done`.

- **Se puede empezar ya:** 01.
- **Después de 01, en paralelo:** 02, 09 y 19.
- **Ruta crítica:** 01 → 02 → 04 → 05 → 07 → 08 → 11 → 12 → 13 → 14 → 15 → 18 → 21.
- **Primer hito útil:** 01–10, 19, 20 y 22: el administrador instala la aplicación con sus datos iniciales, registra días reales y los respalda mientras se construyen los reportes.

## Pendiente fuera de los tickets

- **P-04:** el administrador debe entregar los datos iniciales (servicios, precios, comisiones, personal, sueldos, categorías y motivos de descuento). Bloquea el instalador real del ticket 22.
- **P-07:** volumen y formato de los datos históricos. Bloquea el ticket 23.
- **P-02:** elegir el proveedor de nube para la carpeta de respaldo. No afecta el código.
