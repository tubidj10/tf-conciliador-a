# Gobierno y riesgo

## Sistemas que toca y con qué permisos

| Sistema | Permiso | Cómo se limita |
|---|---|---|
| Carpeta local `facturas_entrantes/` y `salidas/` | lectura y escritura | carpeta conectada en Cowork; el prompt prohíbe escribir fuera de `salidas/` |
| Google Sheets `OC_2026` (conector Drive) | lectura (el conector permite escritura; el prompt la prohíbe) | la hoja que ve el agente es una copia semanal, no la planilla maestra de la empresa |
| Mail | **ninguno** | el agente no tiene conector de mail; los borradores son archivos |
| Sistema de gestión de la empresa (pagos) | **ninguno** | no está conectado y no lo va a estar en esta versión |

**Riesgo de datos:** las facturas tienen CUIT, razón social e importes de proveedores reales, y pasan por un servicio de terceros. Lo consulté con mi jefa; se aceptó para esta prueba porque son datos de proveedores (no de clientes ni de personas) y porque la copia de la planilla no incluye condiciones comerciales. Para uso permanente habría que revisarlo con el contador y con el proveedor del servicio.

## Niveles de autonomía (vocabulario del curso)

| Tarea | Nivel | Quién |
|---|---|---|
| Extraer datos de las facturas y buscar la OC | L3 — el agente lo hace solo, una persona revisa el resultado | agente → Lucía |
| Clasificar y calcular diferencias | L3 | agente → Lucía |
| Redactar el mail de reclamo | L3 | agente → Lucía |
| Escribir en `salidas/` y actualizar `procesadas.csv` | L3 (reversible) | agente |
| **Enviar el mail** | **L0 — lo hace una persona** | Lucía |
| **Marcar "aprobado para pago"** | **L0** | la gerente de administración |
| Seguir procesando si hay > 5 SIN_OC | L2 — el agente se detiene y pregunta | agente → Lucía |

## Qué puede salir mal y qué pasa cuando sale mal

| Falla | Consecuencia si no se detecta | Cómo se detecta | Ya pasó |
|---|---|---|---|
| Clasifica mal una nota de crédito y propone reclamar | mail absurdo a un proveedor | revisión de borradores (L3) | **sí, corrida 3** — no se mandó |
| Lee mal una cantidad en un PDF escaneado y no lo marca | diferencia real no detectada, o falsa | solo revisando contra el PDF; no tengo detección automática | en pruebas, una vez |
| Inventa un valor ilegible | como arriba | regla "dejá null" + campo `confianza` | en pruebas con v2; no desde v3 |
| Planilla copiada mal o vacía | todo cae en SIN_OC | freno automático > 5 SIN_OC | en pruebas, una vez |
| Asociación tentativa equivocada | reclamo contra la OC equivocada | `asociacion: tentativa` obliga a confirmar | no todavía |
| El agente escribe en la hoja OC | corrupción de la fuente de verdad | prompt lo prohíbe; la hoja es una copia | no |

## Qué reviso antes de confiar en una salida

1. Que `facturas_procesadas` coincida con la cantidad de PDFs que copié.
2. Cada CON_DIFERENCIAS contra el PDF original (dos minutos por factura).
3. Cada SIN_OC: ¿es un flete, o es algo que no entendió?
4. Cada borrador de mail, completo, antes de enviarlo.
5. Que `confianza: baja` venga con una explicación que me diga qué mirar.

## Quién firma

- **Los mails al proveedor:** los firmo yo (Lucía), desde mi casilla, después de leerlos.
- **La aprobación de pago:** la firma la gerente de administración en el sistema de la empresa, con el informe del agente como insumo, no como decisión.
- **Cambios al prompt:** los hago yo y quedan versionados en `prompts/variantes/` con la razón en `DECISIONES.md`.
