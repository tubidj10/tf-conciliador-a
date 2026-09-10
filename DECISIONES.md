# DECISIONES — la historia de la construcción

## Por qué este caso

Es mi trabajo de todas las semanas y lo hago mal por cansancio, no por falta de criterio. La Entrega 1 fue un resumidor de mails de proveedores; esto es la versión seria del mismo problema: no leer mails, sino cruzar documentos contra una fuente de verdad.

## Qué achiqué antes de empezar

- **No toca el sistema de gestión.** La primera idea era que el agente marcara "aprobado para pago" en el sistema de la empresa. Lo saqué el primer día: es irreversible y no es mi decisión, es de mi jefa.
- **No manda mails.** Redacta. Yo mando.
- **No aprende de las correcciones.** Pensé en que el agente leyera mi `revision_humana.md` de la semana anterior para "aprender". Lo descarté: no puedo verificar qué aprendió, y prefiero que cada cambio de comportamiento pase por el prompt, donde lo puedo leer.

## Iteración 1 — v1 → v2 (13/8 → 15/8): la tolerancia

**Qué pasó:** con la v1 (12 líneas, `prompts/variantes/system_prompt_v1.md`) corrí 9 facturas de una semana vieja. Marcó 6 "con diferencias". Cinco eran centavos: redondeo del IVA por ítem versus por total.

**Texto del agente (corrida de prueba, 14/8):** *"La factura A-0003-00018790 presenta una diferencia de $0,40 en el total respecto de la OC-2026-0498."*

**Qué cambié:** agregué la tolerancia del 0,5 % sobre el total, el procedimiento paso a paso, las tres categorías con nombre, y la búsqueda de la OC por "Ref." / "O.C." / "Orden", porque dos proveedores no escriben "OC".

**Qué aprendí:** el agente no sabía lo que yo sé del negocio hasta que lo escribí. La v1 era una descripción de la tarea; la v2 es un procedimiento.

## Iteración 2 — v2 → v3 (15/8 → 17/8): no inventar valores

**Qué pasó:** con la v2 corrí 8 facturas de otra semana vieja, una de ellas un PDF escaneado torcido. El agente reportó precio unitario $4.890,00 para un ítem cuyo precio era ilegible. Lo había calculado dividiendo el total por la cantidad, y lo presentó como leído.

**Texto del agente (corrida de prueba, 16/8):** *"Precio unitario: $4.890,00 (inferido a partir del subtotal)."* — la palabra "inferido" estaba, pero adentro del JSON el campo decía 4890.00 sin ninguna marca.

**Qué cambié:** la regla "Nunca inventes un valor que no pudiste leer: dejá null" y el campo `confianza` con obligación de explicar en `observaciones`. También el freno automático de más de 5 SIN_OC, porque en esa misma prueba había copiado mal la planilla y el agente siguió procesando contra una hoja vacía.

**Qué aprendí:** un valor calculado que parece leído es peor que un valor ausente. La v3 es la que corrió las tres corridas reales.

## Iteración 3 — v3 → v4 (2/9): las notas de crédito

**Qué pasó:** en la corrida 3 real (1/9) el agente clasificó una nota de crédito como SIN_OC y redactó un reclamo al proveedor por una devolución (ver `corridas/corrida_03/revision_humana.md`).

**Texto del agente (corrida 3, 1/9):** *"Sugerencia: reclamar al proveedor la emisión de la OC correspondiente."*

**Qué cambié:** la v4 agrega la categoría AJUSTE y la regla de que NC y ND no buscan OC sino la factura de referencia en `procesadas.csv`. Agregué AJUSTE al esquema `formato_salida.json` de la v4 (todavía no al del repo, porque el repo documenta lo que corrió).

**Estado:** **no verificado.** No volví a correr la semana del 31/8 con la v4. Lo voy a hacer esta semana; si llego antes de la entrega, va en `corridas/corrida_03b/`.

**Qué aprendí:** mi métrica cuenta clasificaciones corregidas, y 1 de 10 está dentro del criterio. Pero este error tenía un costo de relación con el proveedor que la métrica no ve. Falta una métrica de gravedad.

## Lo que no delegaría ni aunque funcionara perfecto

- Mandar el mail de reclamo. El tono con un proveedor de veinte años es mío.
- Aprobar pagos. Irreversible, y es decisión de mi jefa.

## Resumen de versiones

| Versión | Fecha | Líneas | Cambio principal | Corrió sobre datos reales |
|---|---|---|---|---|
| v1 | 13/8 | 12 | primera descripción | no (prueba) |
| v2 | 15/8 | 31 | tolerancia, procedimiento, "Ref." | no (prueba) |
| v3 | 17/8 | 40 | null en vez de inventar, confianza, freno automático | sí: corridas 1, 2 y 3 |
| v4 | 2/9 | 45 | AJUSTE para NC/ND | **no todavía** |
