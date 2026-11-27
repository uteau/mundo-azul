# 20: Respaldo al cerrar

**What to build:** Al cerrar la aplicación se ejecuta un respaldo automático con un overlay que avisa que se está respaldando. Tiene un tiempo límite y la aplicación siempre se cierra, aunque el respaldo falle; el error se registra para mostrarlo en la próxima apertura. Cerrar dos veces seguidas no inicia dos respaldos.

**Blocked by:** 19

**Status:** ready-for-agent

- [ ] Con una carpeta válida, cerrar crea un archivo de respaldo nuevo.
- [ ] Con una carpeta inexistente, la aplicación cierra igual y el error queda registrado.
- [ ] Al vencer el tiempo límite la aplicación cierra sin corromper la base.
- [ ] No hay reentrada: un segundo intento de cierre durante el respaldo no inicia otro.
- [ ] Verificación manual en Windows: cerrar con una carpeta válida y con una inexistente.
