# 01: Esqueleto de la app

**What to build:** Al abrir la aplicación de escritorio aparece la barra lateral con todas las secciones (Ventas, Día, Servicios, Personal, Costos, Otros ingresos, Remuneraciones, Dashboard, Respaldo), aún vacías. La primera vez que se abre, crea la base de datos en la carpeta de datos de la aplicación y le aplica las migraciones versionadas. Los errores del backend se muestran al administrador como un toast en español. Queda listo el arnés de pruebas de la costura única: funciones de servicio en Rust probadas sobre una base SQLite en memoria con todas las migraciones aplicadas. La primera migración crea el esquema completo del diseño (§3.2), con sus vistas y las categorías de costo fijas, para que los tickets siguientes trabajen en paralelo sin chocar en migraciones.

**Blocked by:** None (can start immediately)

**Status:** ready-for-agent

- [ ] La aplicación abre una ventana con la barra lateral y una ruta por sección.
- [ ] Al iniciar se crea la base en la carpeta de datos de la aplicación (nunca en la carpeta de respaldo), en modo WAL y con claves foráneas activas.
- [ ] Las migraciones son versionadas y existe una prueba que las aplica sobre una base en memoria.
- [ ] Un error de dominio devuelto por un comando llega al frontend como mensaje legible en un toast.
- [ ] Existen utilidades de formato: fechas dd-mm-aaaa y montos en pesos sin decimales con separador de miles.
- [ ] La migración inicial crea todas las tablas y vistas del diseño, incluidas la comisión de la asignación (ADR-0003), un único dueño y un ítem por servicio en cada venta.
- [ ] Hay al menos una prueba de ejemplo en la costura de servicio que sirve de patrón para el resto.
- [ ] Las funciones de servicio reciben la fecha de hoy como parámetro, para poder probar el rechazo de fechas futuras.
- [ ] Hay un `.gitignore` que deja fuera dependencias, compilados, el archivo real de datos iniciales y los datos históricos.
