# Caso 3 — Otorgar acceso de solo lectura a varias transacciones, en varios roles a la vez

## Contexto

Solicitud para que dos perfiles funcionales distintos (planificación de demanda y liderazgo de planificación comercial) pudieran **visualizar** información de precios y condiciones — sin poder crearlas ni modificarlas. El requerimiento impactaba dos roles maestro (cada uno con sus propios derivados por país) al mismo tiempo.

## Problema

Los usuarios necesitaban visibilidad sobre información de precios para su análisis, pero ningún rol existente les daba acceso — y el negocio fue explícito en que **no debía ser un acceso de edición**, solo consulta.

## Diagnóstico

1. Se registró el requerimiento en la planilla de control, identificando los dos roles maestro afectados y sus respectivos derivados por país (mismo patrón que en el [Caso 1](01-ampliar-transaccion-rol-existente.md): el cambio se hace en el maestro, no en cada derivado).
2. Se definieron las transacciones concretas a habilitar y, más importante, el **tipo de acceso exacto**: display-only. Esto cambia qué valores se configuran en los objetos de autorización (actividad `03` — visualizar — en vez de incluir `01`/`02` que permiten crear/modificar).
3. Se revisaron los valores de autorización ya existentes en ambos roles antes de tocar nada, para no pisar configuración previa que pudiera verse afectada por el cambio.

## Solución

1. **ChaRM**: se creó la orden de cambio y se avanzó a través de los ambientes correspondientes.
2. **Fiori**: se actualizaron/crearon los catálogos de ambos roles, agregando los tiles de las transacciones nuevas, y se validó que aparecieran correctamente en el Space y Page de cada rol.
3. **PFCG**: en ambos roles maestro se incluyeron las transacciones, se ajustaron los objetos de autorización dejando explícitamente solo el valor de actividad de visualización (no creación/edición), y se generaron los perfiles con validación de estado consistente ("semáforo verde").
4. Se repitió el mismo tratamiento en los roles derivados heredados (por país), confirmando que el acceso quedara igual de restringido a solo-lectura en cada uno.
5. Transporte hasta el ambiente correspondiente, con la orden y roles asignados a la tarea de ChaRM.

## Lección / lo que se generalizó

- Cuando el negocio pide "que puedan ver X", la palabra clave es "ver": hay que fijar explícitamente la actividad en display-only en los objetos de autorización, no asumir que el generador de PFCG lo va a dejar así por defecto (a veces propone valores más amplios).
- Un mismo requerimiento que impacta **dos roles distintos en paralelo** (cada uno con sus propios derivados) no es "el doble de trabajo lineal": conviene tratarlo como dos cambios coordinados dentro de la misma orden de ChaRM, para que ambos lleguen a producción juntos y se prueben con el mismo criterio.
- Revisar los valores de autorización *ya existentes* antes de modificar un rol evita el error clásico de "arreglé lo pedido pero rompí algo que ya funcionaba".
