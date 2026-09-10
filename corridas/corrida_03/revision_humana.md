# Revisión humana — corrida 3 (1/9) — salió mal en un caso

Uno de los dos SIN_OC era en realidad una **nota de crédito** (NC-A-0003-00000917) de
Bebidas del Sur, que nos devolvía la diferencia de la corrida 1. El agente la trató como
una factura común con importe negativo, no encontró OC (una nota de crédito no tiene OC:
referencia a una factura), la clasificó SIN_OC y, peor, redactó un borrador pidiendo al
proveedor "la orden de compra correspondiente". Si ese mail salía, le reclamábamos al
proveedor por habernos devuelto plata.

**Dónde lo agarré:** en el punto de supervisión L3 (lectura de borradores antes de enviar).
No se mandó nada.

**Qué hice después:** la iteración 3 de `DECISIONES.md` (prompt v4, categoría AJUSTE).
**Todavía no volví a correr la semana del 31/8 con la v4.**

La diferencia de Jugos del Valle era real. El mail salió sin cambios.

**Clasificaciones corregidas: 1 de 10.** Sigue dentro de mi criterio de éxito (< 1 cada
40), pero el tipo de error —proponer un reclamo por una devolución— es más grave que lo
que la métrica mide.
