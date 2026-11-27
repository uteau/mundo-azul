# 22: Datos iniciales e instalador del primer hito

**What to build:** El administrador instala la aplicación en su equipo Windows y la encuentra con sus servicios, precios, comisiones, personal, sueldos, categorías y motivos ya cargados, para registrar días reales (ventas, asignaciones, días trabajados, bonos, costos y otros ingresos) y respaldarlos mientras se construyen los reportes. Los datos iniciales vienen en un archivo embebido en el instalador y se cargan una sola vez, cuando la base se crea; nunca al actualizar.

**Blocked by:** 06, 08, 10, 20, y los datos iniciales del administrador (pregunta abierta P-04) solo para generar el instalador real

**Status:** ready-for-agent

- [ ] Los datos iniciales se cargan con las funciones de servicio, en una transacción, solo cuando la base no existía; una base existente nunca los vuelve a cargar (prueba con el ejemplo ficticio).
- [ ] Un archivo de datos iniciales inválido (por ejemplo, un servicio sin precio o dos dueños) impide crear la base con un error legible, sin dejar una base a medias.
- [ ] El archivo real de datos iniciales no se versiona; el repositorio tiene un ejemplo ficticio con el mismo formato, documentado.
- [ ] Se genera el instalador NSIS para Windows con el archivo real embebido.
- [ ] Verificación manual del administrador: instalar sin internet, ver los datos iniciales, registrar un día completo, cerrar y encontrar el respaldo en la carpeta sincronizada; instalar una versión nueva encima conserva lo registrado.
