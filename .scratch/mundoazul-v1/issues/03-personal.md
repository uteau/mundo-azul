# 03: Personal

**What to build:** En la pantalla Personal, con pestañas Guías y Trabajadores, el administrador registra guías y marca a uno como dueño. También registra trabajadores, les gestiona el sueldo diario por vigencias con el mismo editor de los servicios y desactiva a quien ya no trabaja.

**Blocked by:** 02

**Status:** ready-for-agent

- [ ] Se puede crear, editar y desactivar guías y trabajadores; cada persona tiene un solo rol, que no cambia después de crearla.
- [ ] Una persona sin registros se puede eliminar con confirmación; una con registros solo se desactiva.
- [ ] Un guía se puede marcar como dueño; hay como máximo un dueño.
- [ ] La marca de dueño solo se pone o se quita en un guía sin asignaciones ni bonos (este caso rechazado se prueba en 08, cuando existen bonos y asignaciones).
- [ ] Cada trabajador tiene un histórico de sueldo diario con vigencias; las pruebas verifican el sueldo vigente a una fecha.
- [ ] Una persona desactivada no aparece en formularios nuevos, pero conserva su historia.
