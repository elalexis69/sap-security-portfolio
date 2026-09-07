# Caso 10 — Restringir un acceso a un valor específico dentro de un objeto de autorización

## Contexto

Dos requerimientos distintos, mismo patrón: el negocio no pedía "dar acceso a una transacción" en términos generales, sino habilitar una transacción **solo para un subconjunto muy específico de datos**:

- Habilitar la transacción de gestión de órdenes de transporte de stock (STO), pero solo para un tipo de documento particular, no para todos los tipos de documento que esa transacción puede manejar.
- Habilitar un tipo de movimiento de inventario específico dentro de un objeto de autorización que ya controlaba varios tipos de movimiento.

## Problema

Agregar la transacción o el objeto de autorización "a secas" habría dado acceso a un conjunto de datos más amplio del que el negocio realmente necesitaba — exactamente el tipo de sobre-otorgamiento que hay que evitar, incluso cuando la solicitud es legítima y acotada en su intención.

## Diagnóstico

En ambos casos, el objeto de autorización ya existía en el rol (u otro objeto de la misma familia), y el rol ya tenía configurados otros valores en ese mismo campo. El trabajo real no era "agregar un objeto nuevo", sino **agregar un valor puntual a un campo ya existente**, sin tocar ni reemplazar los valores que ya estaban configurados para otros procesos.

## Solución

1. Se identificó el objeto de autorización exacto y el campo específico donde debía agregarse el nuevo valor (tipo de documento, o tipo de movimiento, según el caso) — no el objeto completo, sino el campo puntual dentro de él.

   ![Mockup ilustrativo de PFCG](mockups/pfcg-mockup.svg)

2. Se agregó el valor nuevo al campo, revisando explícitamente que los valores ya existentes no se modificaran ni se sobreescribieran — el ajuste debía ser aditivo, no un reemplazo.
3. Se generó el perfil y se validó el estado consistente ("semáforo verde").
4. En el caso de la restricción por tipo de documento, además se probó con un usuario de prueba antes de dar por cerrado el cambio, confirmando que solo se veía el subconjunto de datos esperado (no el universo completo que la transacción podía mostrar).
5. Se transportó el cambio a través de ChaRM hasta los ambientes correspondientes.

## Lección / lo que se generalizó

- No toda solicitud de acceso es "todo o nada" a nivel de transacción — muchas veces la restricción real que pide el negocio vive un nivel más abajo, en el valor de un campo específico dentro de un objeto de autorización ya existente.
- Cuando se ajusta un campo que ya tiene valores configurados, el riesgo no es solo "olvidar agregar el valor nuevo" sino **pisar sin querer** los valores que ya estaban ahí sirviendo a otro proceso. Revisar el estado *antes* de tocar el campo es tan importante como el cambio mismo.
- Probar con un usuario de prueba antes de cerrar el ticket es la única forma real de confirmar que la restricción funciona como se pidió — verificar "el objeto tiene el valor correcto" en PFCG no es lo mismo que confirmar que el usuario final ve exactamente lo que debería ver y nada más.
