# Análisis económico

## Cómo medí

Cowork no muestra tokens por conversación, así que los estimé de dos maneras y me quedé con la más alta: (a) contando caracteres de todo lo que entra en una corrida y dividiendo por 3,5 (regla para castellano que me dio el propio agente y verifiqué con el tokenizador de Anthropic sobre una factura), y (b) pegando el contenido de una corrida completa en el contador de tokens de la consola de Anthropic. Las dos dieron números parecidos; uso (b).

## Costo por corrida

| Componente | Tokens de entrada | Notas |
|---|---|---|
| System prompt v3 + esquema de salida | 1.400 | fijo |
| 10 facturas (texto extraído, ~1.500 c/u) | 15.000 | varía 8–12 facturas |
| Hoja OC_2026 (~300 filas × ~45 tokens) | 13.500 | crece ~15 filas por semana |
| `procesadas.csv` | 600 | crece |
| Mensaje del usuario y overhead de herramientas | 2.500 | estimado |
| **Total entrada** | **≈ 33.000** | |
| **Salida** (JSON + resumen + 1 o 2 mails) | **≈ 3.500** | |

A precios de API de Claude Sonnet (USD 3 por millón de tokens de entrada, USD 15 por millón de salida) una corrida cuesta **≈ USD 0,10 + 0,05 = USD 0,15**. En la práctica no pago por token: uso Cowork con la suscripción Pro (USD 20/mes), que ya tenía para la cursada.

## Proyección

| Escenario | Corridas | Tokens/año (entrada) | Costo API/año | Costo real |
|---|---|---|---|---|
| Hoy: 1 corrida semanal | 52 | 1,7 M | ≈ USD 8 | 0 marginal (suscripción) |
| Diaria (si crece el volumen) | 260 | 8,6 M | ≈ USD 40 | suscripción |
| Diaria y planilla filtrada por proveedor | 260 | 3,0 M | ≈ USD 15 | suscripción |

El costo relevante no es el de tokens sino mi tiempo de revisión: unos 20 minutos por corrida, contra las 2 horas que me llevaba conciliar a mano. La planilla es el ítem que más crece; filtrarla por proveedor antes de comparar reduce la entrada a menos de la mitad y es la primera mejora si esto pasa a diario.

## Elección de modelo

Probé las corridas de prueba de la iteración 2 (8 facturas) con los dos modelos disponibles en Cowork:

| Modelo | Clasificaciones correctas | Falsos "inferidos" | Comentario |
|---|---|---|---|
| Sonnet | 8/8 | 0 (con v3) | igual resultado que Opus |
| Opus | 8/8 | 0 (con v3) | más lento, misma salida |

Me quedé con **Sonnet**: es el más chico de los dos que hace bien la tarea, y la diferencia de precio de API (5×) sería relevante si esto pasara a diario y a API. No probé Haiku porque no está disponible en la interfaz que uso; si pasara a API sería lo primero que probaría, con las mismas 8 facturas como set de prueba.
