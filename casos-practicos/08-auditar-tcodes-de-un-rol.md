# Caso 8 — Auditar qué transacciones otorga un rol (y comparar dos usuarios)

## Contexto

Se necesitaba comparar los accesos de dos usuarios para entender qué transacciones les otorgaban ciertos roles asignados — típico al investigar por qué dos personas con puestos similares tienen comportamiento distinto en el sistema, o al validar qué hace exactamente un rol antes de asignarlo a alguien más.

## Problema

Un rol, por su nombre, no siempre deja claro qué transacciones incluye realmente. Consultar directamente tablas técnicas (como `AGR_TCODES`) es posible pero menos confiable, porque no necesariamente refleja el menú tal como está configurado en PFCG.

## Diagnóstico

1. Se identificó el nombre exacto del rol a analizar para cada usuario en la comparación.
2. Se determinó que la forma más confiable de obtener el listado real de transacciones era vía `SUIM` → **Roles** → **Roles by Complex Selection Criteria**, usando la opción de **Transaction Assignments** sobre el resultado — que refleja el menú configurado en PFCG, no una consulta indirecta a tablas.

## Solución

1. Se buscó cada rol en `SUIM` (Roles by Complex Selection Criteria) y se seleccionó el botón de **Transaction Assignments** para obtener el listado de T-Codes que el rol otorga.
2. Se repitió el mismo procedimiento para los roles de ambos usuarios, permitiendo comparar directamente qué transacciones tenía uno que el otro no, y viceversa.
3. Se aplicó el mismo método, en dirección inversa, en un caso relacionado: partiendo de un T-Code puntual (`MIRO`) y usando `SUIM` → **By Transaction Assignment in Menu** para encontrar qué roles lo contenían, priorizando de nuevo un rol Z de negocio específico por sobre un rol SAP estándar de alcance amplio (mismo criterio que en el [Caso 7](07-encontrar-rol-desde-tcode-faltante.md)).

## Lección / lo que se generalizó

- `SUIM` funciona en ambas direcciones y ambas son útiles: "¿qué transacciones tiene este rol?" y "¿qué roles tienen esta transacción?" — vale la pena tener claro cuál pregunta se está respondiendo antes de elegir el reporte.
- El método funciona igual para roles simples y compuestos, con una salvedad: si un rol compuesto no muestra transacciones directamente, hay que revisar los roles simples que lo componen — el compuesto es solo un contenedor.
- Comparar accesos entre dos usuarios "a ojo" (mirando nombres de rol) es poco confiable cuando los nombres no son autoexplicativos. Comparar el resultado real de transacciones otorgadas es lo que realmente responde la pregunta de negocio ("¿por qué uno puede hacer X y el otro no?").
- Este mismo procedimiento sirve como base para documentar perfiles funcionales — no solo para resolver un ticket puntual, sino para ir armando referencia reutilizable de "qué hace cada rol" en lugar de tener que volver a investigarlo cada vez.
