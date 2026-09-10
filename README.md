# Conciliador de facturas de proveedores

**Trabajo final · Programación de y con Agentes de IA · MBA UCEMA · 2026 2T**
**Autora:** Lucía Ferreyra
**Herramienta:** Claude (Cowork) con una carpeta local conectada y el conector de Google Drive (Sheets)
**Video (2:40):** link en el campus. Muestra la corrida 2 completa y el punto de supervisión humana.

## Qué construí

Trabajo en administración en una distribuidora mayorista de bebidas que recibe unas 40 facturas de proveedores por semana. La conciliación factura ↔ orden de compra (OC) la hacía a mano en una planilla y cada semana se me escapaban dos o tres diferencias de precio o cantidad, que después había que reclamar tarde.

El agente lee las facturas (PDF) que llegan a una carpeta, las cruza contra la planilla de órdenes de compra, clasifica cada una en **CONCILIADA / CON_DIFERENCIAS / SIN_OC**, escribe un informe JSON y deja redactados los mails de reclamo. **No manda mails ni marca nada como "aprobado para pago": eso queda del lado humano** (ver `gobierno_riesgos.md`).

Criterio de éxito que me fijé: menos de 1 factura mal clasificada por cada 40, medido por mi revisión semanal del informe.

## Cómo se lo pedí

El contrato completo está en `prompts/`. Es la versión 3 del system prompt; las versiones 1 y 2 están en `prompts/variantes/` y la historia de por qué cambió cada una está en `DECISIONES.md`. El user prompt de cada corrida es siempre el mismo: *"Procesá las facturas nuevas de esta semana."*

Las tres reglas que más me costó llegar a formular fueron: la tolerancia del 0,5 % sobre el total (la v1 marcaba diferencias por centavos de redondeo de IVA), la de "nunca inventes un valor que no pudiste leer: dejá null" (en la v2 el agente completaba el precio unitario dividiendo total sobre cantidad cuando el PDF estaba borroso), y el freno automático si más de 5 facturas quedan SIN_OC en una corrida.

## Cómo usarlo

1. Clonar el repo y abrir la carpeta en Cowork (conectarla).
2. Conectar el conector de Google Drive y abrir `datos/OC_2026_demo.csv` como hoja de Google Sheets con el nombre `OC_2026`.
3. Cargar `prompts/system_prompt.md` como instrucción del proyecto.
4. Copiar los archivos de `corridas/corrida_01/entrada/facturas/` a `facturas_entrantes/`.
5. Enviar el mensaje de `prompts/user_prompt.md`.
6. Comparar lo que quede en `salidas/` con `corridas/corrida_01/salida.json`.

No hace falta API, saldo ni terminal. Todo corre dentro de Cowork.

## Contexto y memoria

Lo que el agente necesita para trabajar y de dónde sale cada cosa:

- **Contexto fijo:** `prompts/system_prompt.md` y `formato_salida.json`. Cowork los carga en cada conversación.
- **Facturas de la semana:** las descargo yo del mail a `facturas_entrantes/`. Cada corrida es una conversación nueva.
- **Órdenes de compra:** la hoja `OC_2026`, leída por el conector al empezar la corrida. No la copio al contexto porque cambia todas las semanas.
- **Memoria entre corridas:** `procesadas.csv`, un archivo plano con las facturas ya procesadas. Es deliberadamente un CSV: puedo abrirlo y ver qué se procesó. No hay memoria conversacional entre corridas; cada semana el agente arranca de cero salvo por ese archivo.

Tamaño típico: 8 a 12 facturas de una página y una planilla de ~300 filas. Nunca tuve problemas de largo. Si la planilla crece mucho, la solución es que el agente filtre la hoja por proveedor antes de comparar, cosa que hoy no hace.

## Qué funciona

Las tres corridas de `corridas/` son reales (semanas del 17/8, 24/8 y 31/8). Las dos primeras salieron sin ninguna clasificación corregida. La tercera tuvo un error de clasificación real —una nota de crédito tratada como factura— que agarré en el punto de supervisión y que está contado en `DECISIONES.md` y en `corridas/corrida_03/revision_humana.md`.

Los datos de las corridas están anonimizados: los proveedores reales tienen nombres ficticios y los CUIT están reemplazados. Los importes y las diferencias son los reales.

## Qué falta o qué falló

- Los fletes y servicios sin OC caen todos en SIN_OC y ensucian el informe. Falta una lista de proveedores "sin OC esperada".
- Notas de crédito y débito: descubierto en la corrida 3. Agregué la regla al prompt (v4, en `prompts/variantes/`) pero **todavía no volví a correr la semana del 31/8 con la v4**. Queda documentado como pendiente.
- PDFs escaneados de mala calidad: en pruebas previas el agente marcó `confianza: baja` correctamente dos veces, pero una vez leyó un "8" como "3" en una cantidad y no lo marcó.
- Mi métrica (facturas mal clasificadas) no distingue un error inofensivo de uno que podría costar una relación con un proveedor. Falta una métrica de gravedad.

## Estructura y evidencia

- `prompts/` — system prompt v3 (el que corrió las tres corridas), user prompt, y variantes v1, v2 y v4.
- `corridas/corrida_0N/` — por corrida: `entrada/` (manifiesto de facturas, texto extraído de las facturas relevantes, recorte de la hoja OC), `salida.json`, `resumen.txt`, `fecha.txt`, `borradores_mail/` y `revision_humana.md`.
- `DECISIONES.md` — la historia: cuatro versiones del prompt, qué falló en cada una, qué achiqué.
- `analisis_economico.md` — tokens por corrida, costo, proyección y elección de modelo.
- `gobierno_riesgos.md` — sistemas que toca, permisos, niveles L0–L4, qué puede salir mal, quién firma.
- `formato_salida.json` — esquema de la salida.
- `datos/OC_2026_demo.csv` y `procesadas.csv` — planilla de demostración (ficticia) y memoria entre corridas.

## Autoría

Trabajo individual. Los datos de la empresa están anonimizados; los nombres de proveedores son ficticios.
