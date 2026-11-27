# Sin salidas: la asignación es diaria, por guía y servicio

El diseño original registraba cada salida del bote y asignaba a cada guía personas de ítems de venta específicos, con un máximo de 2 por guía. Decidimos no registrar salidas: la **Asignación** es "el guía X llevó N personas del servicio Y en el día D", sin vínculo con ventas concretas y sin tope de personas por guía. El registro se hace a posteriori y solo sirve para pagar comisiones y analizar el negocio; para eso basta guía × servicio × día, y el detalle por salida duplicaba trabajo de carga sin responder ninguna pregunta del administrador.

## Consecuencias

- La coherencia con lo vendido se verifica por **día y servicio**: Σ asignaciones = personas que bucearon. Si no cuadra, el sistema avisa pero permite guardar.
- No se puede analizar uso del bote ni cantidad de salidas. Recuperarlo exigiría reingresar datos históricos.
