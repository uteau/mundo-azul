# 19: Respaldo manual

**What to build:** En la pantalla Respaldo, el administrador elige la carpeta (típicamente sincronizada con la nube) y la cantidad de versiones a conservar (30 por defecto), y puede presionar "Respaldar ahora". La copia usa la API de backup en línea de SQLite hacia un archivo temporal, verifica integridad, lo renombra con fecha y hora y borra las versiones más antiguas. La barra lateral muestra la fecha del último respaldo exitoso y avisa si el último falló o si no hay carpeta configurada. Se documenta la restauración manual.

**Blocked by:** 01

**Status:** ready-for-agent

- [ ] Pruebas con directorio temporal: se crea un archivo con fecha y hora en el nombre, y la rotación conserva exactamente N.
- [ ] Una carpeta inexistente devuelve error, no daña la base principal y no deja un archivo temporal.
- [ ] El estado (último éxito, último error, sin carpeta) se persiste y se muestra en la barra lateral.
- [ ] Hay una guía escrita de restauración manual.
