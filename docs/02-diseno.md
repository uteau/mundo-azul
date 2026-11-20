# Mundo Azul — Documento de diseño

| Campo | Valor |
|---|---|
| Versión | 2.0 |
| Fecha | 2026-11-20 |
| Requerimientos | `docs/01-requerimientos.md` |
| Glosario | `GLOSSARY.md` |
| ADRs | `docs/adr/` |
| Siguiente documento | `docs/03-tareas.md` |

---

## 1. Decisiones principales

| # | Decisión | Motivo |
|---|---|---|
| D-01 | **Tauri v2** con backend en **Rust** y frontend **React + TypeScript + Tailwind CSS** (Vite). | Stack definido; binario liviano para Windows. |
| D-02 | Acceso a SQLite desde Rust con **`rusqlite`** (features `bundled` y `backup`), no desde el frontend. | La API de backup de SQLite solo está disponible en Rust; las reglas de negocio y transacciones quedan en un único lugar testeable. |
| D-03 | El frontend se comunica solo mediante **comandos Tauri** (`invoke`) tipados. | Frontera clara; el frontend no conoce SQL. |
| D-04 | Montos como `INTEGER` (pesos). Fechas como `TEXT` ISO `YYYY-MM-DD`. | RNF-05; las fechas ISO se ordenan y comparan como texto. |
| D-05 | Histórico de valores mediante tablas de **vigencias** (`vigente_desde`). | S-02: soporta temporadas que cruzan el año. |
| D-06 | El precio unitario se **copia** al ítem de venta (snapshot). Comisiones y sueldos se **resuelven por fecha** al calcular. | RN-03 exige fijar el precio; las comisiones son deterministas desde su histórico. El monto del día trabajado sí se guarda (RN-29). |
| D-07 | Los reportes se calculan **al vuelo** (SQL + combinación en Rust), sin tablas agregadas. | Volumen bajo (RNF-07); evita inconsistencias al editar (RN-43). |
| D-08 | Migraciones con **`rusqlite_migration`**, embebidas en el binario. | RNF-10. |
| D-09 | Frontend: **TanStack Query**, **React Router**, **React Hook Form + Zod**, **Recharts**. | Herramientas estándar; invalidación de caché simple tras cada mutación. |
| D-10 | Base de datos en `app_data_dir()` (`%APPDATA%\cl.mundoazul.app\mundoazul.sqlite`), modo `WAL`, `foreign_keys = ON`. | RNF-08. |
| D-11 | **Asignaciones sin vínculo a ventas ni salidas**: tabla `asignacion(fecha, guia, servicio, cantidad)` y cuadre por día y servicio. | ADR-0001. |
| D-12 | **Inasistencias en el ítem de venta** (`inasistencias`, `abono_retenido`); no hay estado "cancelada". | ADR-0002. |
| D-13 | Montos de venta (bruto, neto, ingreso) **no se almacenan**: se derivan de ítems y descuento. | Una sola fuente de verdad; las ediciones de inasistencias no dejan totales desfasados. |

## 2. Arquitectura

```
┌──────────────────────────── Ventana Tauri (WebView2) ────────────────────────────┐
│ React + Tailwind                                                                 │
│  pages/ ─► features/*/components ─► features/*/api.ts (TanStack Query)           │
│                                         │  invoke("comando", args)               │
└─────────────────────────────────────────┼────────────────────────────────────────┘
                                          ▼
┌──────────────────────────────── Backend Rust ────────────────────────────────────┐
│ commands/   (capa delgada: deserializa, llama al servicio, mapea errores)        │
│ services/   (reglas de negocio, validaciones, transacciones)                     │
│ repo/       (SQL con rusqlite)                                                   │
│ reports/    (consultas agregadas del dashboard y remuneraciones)                 │
│ backup/     (API de backup SQLite, rotación de versiones)                        │
│ db/         (conexión, PRAGMAs, migraciones)                                     │
└─────────────────────────────────────────┬────────────────────────────────────────┘
                                          ▼
                            SQLite (archivo local en AppData)
                                          │  backup API
                                          ▼
                        Carpeta sincronizada (OneDrive / Google Drive)
```

- Una sola conexión `rusqlite::Connection` protegida por `Mutex` en el `State` de Tauri.
- Errores de dominio como `enum AppError` serializable `{ code, message, field? }`, mostrados en español. Los **avisos** (día descuadrado) no son errores: se devuelven como datos.

## 3. Modelo de datos

### 3.1 Diagrama entidad-relación

```
servicio 1──* precio_servicio
servicio 1──* comision
servicio 1──* venta_item *──1 venta *──0..1 motivo_descuento
servicio 1──* asignacion *──1 personal(rol=guia)
personal(rol=trabajador) 1──* sueldo_trabajador
personal(rol=trabajador) 1──* dia_trabajado
personal 1──* bono
categoria_costo 1──* costo
categoria_otro_ingreso 1──* otro_ingreso
configuracion (clave/valor)
```

### 3.2 Esquema (migración inicial)

```sql
PRAGMA foreign_keys = ON;

CREATE TABLE servicio (
  id        INTEGER PRIMARY KEY,
  nombre    TEXT NOT NULL UNIQUE,
  activo    INTEGER NOT NULL DEFAULT 1 CHECK (activo IN (0,1))
);

CREATE TABLE precio_servicio (
  id             INTEGER PRIMARY KEY,
  servicio_id    INTEGER NOT NULL REFERENCES servicio(id) ON DELETE RESTRICT,
  vigente_desde  TEXT NOT NULL,                -- YYYY-MM-DD
  precio         INTEGER NOT NULL CHECK (precio >= 0),
  UNIQUE (servicio_id, vigente_desde)
);

CREATE TABLE comision (
  id             INTEGER PRIMARY KEY,
  servicio_id    INTEGER NOT NULL REFERENCES servicio(id) ON DELETE RESTRICT,
  vigente_desde  TEXT NOT NULL,
  monto          INTEGER NOT NULL CHECK (monto >= 0),
  UNIQUE (servicio_id, vigente_desde)
);

CREATE TABLE personal (
  id        INTEGER PRIMARY KEY,
  nombre    TEXT NOT NULL,
  rol       TEXT NOT NULL CHECK (rol IN ('guia','trabajador')),
  es_dueno  INTEGER NOT NULL DEFAULT 0 CHECK (es_dueno IN (0,1)),
  activo    INTEGER NOT NULL DEFAULT 1 CHECK (activo IN (0,1)),
  CHECK (es_dueno = 0 OR rol = 'guia')
);

CREATE TABLE sueldo_trabajador (
  id             INTEGER PRIMARY KEY,
  personal_id    INTEGER NOT NULL REFERENCES personal(id) ON DELETE CASCADE,
  vigente_desde  TEXT NOT NULL,
  monto_diario   INTEGER NOT NULL CHECK (monto_diario >= 0),
  UNIQUE (personal_id, vigente_desde)
);

CREATE TABLE dia_trabajado (
  id           INTEGER PRIMARY KEY,
  personal_id  INTEGER NOT NULL REFERENCES personal(id) ON DELETE RESTRICT,
  fecha        TEXT NOT NULL,
  monto        INTEGER NOT NULL CHECK (monto >= 0),
  UNIQUE (personal_id, fecha)                    -- RN-29
);

CREATE TABLE bono (
  id           INTEGER PRIMARY KEY,
  personal_id  INTEGER NOT NULL REFERENCES personal(id) ON DELETE RESTRICT,
  fecha        TEXT NOT NULL,
  monto        INTEGER NOT NULL CHECK (monto > 0),
  nota         TEXT
);

CREATE TABLE motivo_descuento (
  id      INTEGER PRIMARY KEY,
  nombre  TEXT NOT NULL UNIQUE,
  activo  INTEGER NOT NULL DEFAULT 1 CHECK (activo IN (0,1))
);

CREATE TABLE venta (
  id                   INTEGER PRIMARY KEY,
  fecha                TEXT NOT NULL,          -- fecha de la actividad (RN-01)
  descuento            INTEGER NOT NULL DEFAULT 0 CHECK (descuento >= 0),
  motivo_descuento_id  INTEGER REFERENCES motivo_descuento(id) ON DELETE RESTRICT,
  nota                 TEXT,
  creada_en            TEXT NOT NULL DEFAULT (datetime('now')),
  actualizada_en       TEXT NOT NULL DEFAULT (datetime('now')),
  CHECK ((descuento = 0) = (motivo_descuento_id IS NULL))   -- RN-14
);
CREATE INDEX idx_venta_fecha ON venta(fecha);

CREATE TABLE venta_item (
  id               INTEGER PRIMARY KEY,
  venta_id         INTEGER NOT NULL REFERENCES venta(id) ON DELETE CASCADE,
  servicio_id      INTEGER NOT NULL REFERENCES servicio(id) ON DELETE RESTRICT,
  cantidad         INTEGER NOT NULL CHECK (cantidad >= 1),
  precio_unitario  INTEGER NOT NULL CHECK (precio_unitario >= 0),  -- snapshot (RN-03)
  inasistencias    INTEGER NOT NULL DEFAULT 0 CHECK (inasistencias >= 0),
  abono_retenido   INTEGER NOT NULL DEFAULT 0 CHECK (abono_retenido >= 0),
  CHECK (inasistencias <= cantidad),
  CHECK (inasistencias > 0 OR abono_retenido = 0)                  -- RN-07
);
CREATE INDEX idx_venta_item_venta ON venta_item(venta_id);

CREATE TABLE asignacion (
  id           INTEGER PRIMARY KEY,
  fecha        TEXT NOT NULL,
  guia_id      INTEGER NOT NULL REFERENCES personal(id) ON DELETE RESTRICT,
  servicio_id  INTEGER NOT NULL REFERENCES servicio(id) ON DELETE RESTRICT,
  cantidad     INTEGER NOT NULL CHECK (cantidad >= 1),
  UNIQUE (fecha, guia_id, servicio_id)          -- RN-17
);
CREATE INDEX idx_asignacion_fecha ON asignacion(fecha);

CREATE TABLE categoria_costo (
  id      INTEGER PRIMARY KEY,
  nombre  TEXT NOT NULL UNIQUE,
  fija    INTEGER NOT NULL DEFAULT 0 CHECK (fija IN (0,1)),
  activa  INTEGER NOT NULL DEFAULT 1 CHECK (activa IN (0,1))
);

CREATE TABLE costo (
  id            INTEGER PRIMARY KEY,
  fecha         TEXT NOT NULL,                  -- fecha de pago (RN-35)
  categoria_id  INTEGER NOT NULL REFERENCES categoria_costo(id) ON DELETE RESTRICT,
  monto         INTEGER NOT NULL CHECK (monto > 0),
  descripcion   TEXT
);
CREATE INDEX idx_costo_fecha ON costo(fecha);

CREATE TABLE categoria_otro_ingreso (
  id      INTEGER PRIMARY KEY,
  nombre  TEXT NOT NULL UNIQUE,
  activa  INTEGER NOT NULL DEFAULT 1 CHECK (activa IN (0,1))
);

CREATE TABLE otro_ingreso (
  id            INTEGER PRIMARY KEY,
  fecha         TEXT NOT NULL,
  categoria_id  INTEGER NOT NULL REFERENCES categoria_otro_ingreso(id) ON DELETE RESTRICT,
  monto         INTEGER NOT NULL CHECK (monto > 0)
);
CREATE INDEX idx_otro_ingreso_fecha ON otro_ingreso(fecha);

CREATE TABLE configuracion (
  clave  TEXT PRIMARY KEY,
  valor  TEXT NOT NULL
);
-- claves: backup.carpeta, backup.max_versiones, backup.ultimo_ok, backup.ultimo_error
```

### 3.3 Invariantes validadas en la capa de servicios

| Invariante | Regla | Dónde |
|---|---|---|
| `venta.descuento` ≤ monto bruto (Σ (cantidad − inasistencias) × precio_unitario) | RN-12 | `services::ventas` (también al editar inasistencias) |
| `personal.rol = 'guia'` en `asignacion.guia_id` | RN-17 | `services::asignaciones` |
| Existe vigencia para la fecha (precio al crear ítem; comisión al asignar; sueldo al registrar día) | RN-25 | servicios correspondientes |
| Cuadre por (fecha, servicio): Σ `asignacion.cantidad` = Σ personas que bucearon | RN-20 | `services::dia` — **aviso**, no bloquea |

Como las asignaciones no referencian ventas, cambiar la fecha o las cantidades de una venta nunca se bloquea: solo puede descuadrar los días afectados.

### 3.4 Cálculos

**Venta** (`domain::venta::calcular`), función pura:

```
bucearon(item)  = cantidad − inasistencias
bruto           = Σ bucearon × precio_unitario
neto            = bruto − descuento
abonos          = Σ abono_retenido
ingreso         = neto + abonos
abono_propuesto(item) = round_half_up(inasistencias × precio_unitario × 0,5)
```

**Reparto del descuento por servicio** (`domain::venta::repartir_descuento`, RN-16): proporcional al bruto de cada ítem, redondeado por **mayor resto** para que la suma sea exactamente `descuento`. Si el bruto es 0, el descuento es 0 (RN-12).

**Valor vigente a una fecha** (precios, comisiones y sueldos):

```sql
SELECT monto FROM comision
WHERE servicio_id = ?1 AND vigente_desde <= ?2
ORDER BY vigente_desde DESC LIMIT 1;
```

**Ventas por ítem** (base de los reportes de ventas): vista SQL.

```sql
CREATE VIEW v_venta_item AS
SELECT v.id AS venta_id, v.fecha, v.descuento, v.motivo_descuento_id,
       vi.id AS item_id, vi.servicio_id, vi.cantidad, vi.inasistencias,
       vi.cantidad - vi.inasistencias AS bucearon,
       (vi.cantidad - vi.inasistencias) * vi.precio_unitario AS bruto,
       vi.abono_retenido
FROM venta v JOIN venta_item vi ON vi.venta_id = v.id;
```

Los reportes por servicio leen `v_venta_item` por rango y aplican `repartir_descuento` en Rust agrupando por `venta_id`.

**Ingresos** (RN-38) = Σ bruto − Σ descuento + Σ abono_retenido (de `v_venta_item` / `venta`) + Σ `otro_ingreso.monto`.

**Comisiones por día** (RN-26, RN-27): vista SQL.

```sql
CREATE VIEW v_comision_detalle AS
SELECT a.fecha, a.guia_id, a.servicio_id, a.cantidad AS personas,
       CASE WHEN p.es_dueno = 1 THEN 0 ELSE
         (SELECT c.monto FROM comision c
          WHERE c.servicio_id = a.servicio_id AND c.vigente_desde <= a.fecha
          ORDER BY c.vigente_desde DESC LIMIT 1)
       END AS comision
FROM asignacion a JOIN personal p ON p.id = a.guia_id;
```

Pago del guía en el día = Σ `personas × comision` + Σ bonos de ese guía y fecha (los bonos del dueño también se ignoran).

**Pago a trabajadores por día** (RN-28) = `dia_trabajado.monto` + Σ bonos del día.

**Utilidad** (RN-39) = ingresos − (comisiones + días trabajados + bonos) − Σ `costo.monto`, todo en la misma ventana.

**Margen por servicio** (RN-40) = neto repartido del servicio − Σ `personas × comision` del servicio en el rango.

**Cuadre del día** (RN-20):

```sql
SELECT servicio_id, SUM(vendidas) AS bucearon, SUM(asignadas) AS asignadas FROM (
  SELECT servicio_id, bucearon AS vendidas, 0 AS asignadas FROM v_venta_item WHERE fecha = ?1
  UNION ALL
  SELECT servicio_id, 0, cantidad FROM asignacion WHERE fecha = ?1
) GROUP BY servicio_id HAVING SUM(vendidas) <> SUM(asignadas);
```

**Agrupación por período** (RF-27): diario `fecha`, mensual `substr(fecha,1,7)`.

**Comparación entre años** (RF-28): el rango A es el elegido; el rango B es el mismo rango trasladado al año de comparación (mismo día y mes; si el rango cruza el cambio de año, ambos extremos se trasladan la misma cantidad de años; 29-02 → 28-02 en años no bisiestos). Se ejecuta la misma consulta por servicio para A y B; Rust combina por `servicio_id` y calcula `diferencia = B − A` y `variacion_pct = (B − A) / A` (nulo si A = 0).

## 4. API de comandos Tauri

Todos devuelven `Result<T, AppError>`. Los tipos se espejan en `src/lib/types.ts`.

| Módulo | Comandos |
|---|---|
| servicios | `servicios_listar`, `servicio_guardar`, `servicio_set_activo`, `precios_listar(servicio_id)`, `precio_guardar`, `precio_eliminar`, `comisiones_listar(servicio_id)`, `comision_guardar`, `comision_eliminar`, `precio_vigente(servicio_id, fecha)` |
| personal | `personal_listar(rol?)`, `personal_guardar`, `personal_set_activo`, `sueldos_listar`, `sueldo_guardar`, `sueldo_eliminar`, `dias_trabajados_listar(rango, personal_id?)`, `dias_trabajados_registrar(fecha, [{personal_id, monto}])`, `dia_trabajado_actualizar`, `dia_trabajado_eliminar`, `bonos_listar(rango, personal_id?)`, `bono_guardar`, `bono_eliminar` |
| ventas | `ventas_listar(filtros)`, `venta_obtener(id)`, `venta_guardar(VentaInput)` (venta + ítems en una transacción), `venta_eliminar(id)`, `venta_calcular(VentaInput)` (previsualización), `motivos_listar`, `motivo_guardar`, `motivo_set_activo` |
| dia | `dia_obtener(fecha)` → `{ ventas, bucearon_por_servicio, asignaciones, descuadres, dias_trabajados, bonos }`, `asignaciones_guardar(fecha, [{guia_id, servicio_id, cantidad}])` (reemplaza las del día en una transacción), `dias_descuadrados(rango)` |
| costos | `categorias_costo_listar`, `categoria_costo_guardar`, `categoria_costo_set_activa`, `costos_listar(filtros)`, `costo_guardar`, `costo_eliminar` |
| otros_ingresos | `categorias_otro_ingreso_listar`, `categoria_otro_ingreso_guardar`, `categoria_otro_ingreso_set_activa`, `otros_ingresos_listar(filtros)`, `otro_ingreso_guardar`, `otro_ingreso_eliminar` |
| reportes | `remuneraciones_guias(rango)`, `remuneraciones_trabajadores(rango)`, `dash_ventas_por_servicio(rango, periodo, servicio_ids?)`, `dash_comparacion(rango, anio_comparacion)`, `dash_ganancias_personal(rango, periodo)`, `dash_costos(rango, periodo, categoria_ids?)`, `dash_otros_ingresos(rango, periodo)`, `dash_utilidad(rango, periodo)`, `dash_margen_servicio(rango, periodo)`, `dash_inasistencias(rango, periodo)`, `dash_descuentos(rango, periodo)` |
| respaldo | `respaldo_config_obtener`, `respaldo_config_guardar(carpeta, max_versiones)`, `respaldo_ejecutar` → `{ archivo, fecha }`, `respaldo_estado` |

## 5. Respaldo

1. **Destino**: carpeta elegida con el diálogo nativo (`tauri-plugin-dialog`), guardada en `configuracion`.
2. **Copia**: `rusqlite::backup::Backup` con `run_to_completion(100 páginas, 10 ms, None)` hacia `mundoazul-YYYYMMDD-HHMMSS.sqlite.tmp` en la carpeta destino; luego `PRAGMA integrity_check` sobre la copia y `rename` al nombre final.
3. **Rotación**: listar `mundoazul-*.sqlite`, ordenar por nombre y borrar los que excedan `max_versiones` (por defecto 30).
4. **Al cerrar**: `WindowEvent::CloseRequested` → `api.prevent_close()` → overlay "Respaldando…" → respaldo en un hilo → `app.exit(0)` con éxito o fallo (RNF-09). Timeout de 30 s, sin reentrada.
5. **Estado**: `backup.ultimo_ok` / `backup.ultimo_error`; la barra lateral muestra "Último respaldo: hace 2 h" o un aviso en rojo.
6. **Restauración**: manual (cerrar la app y copiar el respaldo sobre `mundoazul.sqlite` en AppData).

## 6. Frontend

### 6.1 Estructura

```
src/
  main.tsx, App.tsx, router.tsx
  lib/        api.ts, types.ts, format.ts (CLP, fechas), dates.ts (rangos y atajos)
  components/ Button, Input, MoneyInput, DateInput, Select, CreatableSelect, Table, Dialog, ConfirmDialog, Toast, EmptyState, PageHeader
  features/
    servicios/      api.ts, ServiciosPage.tsx, VigenciasEditor.tsx (precios, comisiones y sueldos)
    personal/       api.ts, PersonalPage.tsx, DiasTrabajadosPage.tsx, BonosPage.tsx
    ventas/         api.ts, VentasPage.tsx, VentaForm.tsx, VentaResumen.tsx
    dia/            api.ts, DiaPage.tsx, AsignacionesEditor.tsx, CuadreServicios.tsx
    costos/         api.ts, CostosPage.tsx
    otros-ingresos/ api.ts, OtrosIngresosPage.tsx
    remuneraciones/ api.ts, RemuneracionesPage.tsx
    dashboard/      api.ts, DashboardPage.tsx, FiltrosBar.tsx, widgets/*
    respaldo/       api.ts, RespaldoPage.tsx, BackupStatus.tsx, ClosingOverlay.tsx
```

`CreatableSelect` se usa para categorías de costo, categorías de otro ingreso y motivos de descuento (crear al momento).

### 6.2 Navegación

Barra lateral: **Dashboard** · **Día** · Ventas · Costos · Otros ingresos · Remuneraciones · Personal · Servicios · Respaldo. Al pie, el estado del último respaldo y "Respaldar ahora".

### 6.3 Flujo principal: registrar un día

1. Elegir fecha (por defecto hoy; flechas para día anterior/siguiente).
2. **Ventas del día**: lista con "Nueva venta" (`Ctrl+N`, fecha precargada). En el formulario, cada ítem tiene servicio, cantidad e inasistencias; si inasistencias > 0 aparece el abono retenido propuesto. Descuento con ayuda de % y motivo.
3. **Asignaciones**: tabla guía × servicio con la cantidad de personas. Junto a ella, `CuadreServicios` muestra por servicio "bucearon 8 / asignadas 7" y resalta los descuadres.
4. **Trabajadores y bonos**: casillas para marcar quién trabajó (monto precargado) y bonos del día.
5. **Otros ingresos y costos del día**: acceso rápido a registrar.

### 6.4 Convenciones de UI

- `MoneyInput`: solo dígitos, muestra `$ 45.000`, entrega `number` entero.
- Atajos: `Ctrl+N` nueva venta en Día, `Enter` guardar en diálogos, `Esc` cerrar.
- Toda eliminación usa `ConfirmDialog`.
- Tras cada mutación se invalidan `['ventas']`, `['dia', fecha]`, `['reportes']`.

### 6.5 Dashboard

- `FiltrosBar` global: rango con atajos, período (diario / mensual) y filtros contextuales.
- Widgets:
  - Tarjetas KPI: ingresos, remuneraciones, costos, utilidad, personas atendidas.
  - Utilidad: ingresos (ventas netas, abonos retenidos, otros ingresos) vs remuneraciones vs costos, con línea de utilidad.
  - Ventas por servicio y período (alternar personas / dinero).
  - Margen por servicio.
  - Comparador entre años: mismo rango en el año base y en el de comparación, por servicio (A, B, diferencia, variación %).
  - Ganancias por persona y período.
  - Costos por categoría y período; otros ingresos por categoría y período.
  - Inasistencias (personas, abono retenido, %) y descuentos por motivo.

## 7. Estructura del repositorio

```
mundoazul/
  GLOSSARY.md
  docs/                 requerimientos, diseño, tareas, adr/
  src/                  frontend React
  src-tauri/
    src/
      main.rs, lib.rs, error.rs, state.rs
      db/        mod.rs, migrations/ (001_inicial.sql, 002_vistas.sql, 003_semillas.sql)
      domain/    venta (cálculo y reparto de descuento), vigencia, periodo
      repo/      un módulo por agregado
      services/  ventas.rs, dia.rs, personal.rs, servicios.rs, costos.rs, otros_ingresos.rs
      reports/   ingresos.rs, remuneraciones.rs, dashboard.rs
      backup/    mod.rs
      commands/  un módulo por área
    tauri.conf.json, Cargo.toml
  package.json, vite.config.ts, tailwind config
```

## 8. Estrategia de pruebas

| Nivel | Herramienta | Qué cubre |
|---|---|---|
| Unitarias Rust | `cargo test` | Cálculo de venta con inasistencias, abono propuesto, reparto de descuento por mayor resto, resolución de vigencias, claves de período, rotación de respaldos. |
| Integración Rust | `cargo test` con SQLite en memoria + migraciones | Servicios y reportes contra un **dataset de referencia** con totales calculados a mano. Cuadre del día, vigencias, duplicados de día trabajado, dueño sin comisión. |
| Respaldo | `cargo test` con directorio temporal | `integrity_check`, nombre con fecha, rotación, fallo controlado si la carpeta no existe. |
| Unitarias frontend | Vitest | `format.ts`, `dates.ts`, esquemas Zod. |
| Manual | Lista de verificación | Instalador en Windows, cierre con respaldo, funcionamiento sin red. |

## 9. Empaquetado

- `tauri build` genera instalador **NSIS** para Windows x64; WebView2 con instalador offline si el equipo no lo tiene.
- Identificador: `cl.mundoazul.app`. Versión semántica en `tauri.conf.json`.
- Desarrollo desde WSL: frontend y pruebas de Rust en Linux; el build final de Windows en Windows (o con `cargo-xwin`).

## 10. Riesgos

| Riesgo | Mitigación |
|---|---|
| El sincronizador de la nube toma el archivo mientras se escribe. | Escribir a `.tmp` y renombrar; la base principal nunca está en la carpeta sincronizada. |
| Errores de ingreso sin vigencia definida. | Validación RN-25 con mensaje que enlaza a Servicios o Personal. |
| Días descuadrados que pasan inadvertidos y distorsionan comisiones. | Aviso en la vista Día, listado de días descuadrados e indicador en el dashboard. |
| Redondeos del reparto de descuento que no cuadran con el total. | Mayor resto + criterio de aceptación 5 en el dataset de referencia. |
