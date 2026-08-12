# Caso 2 — Reasignar roles de aprobación por ausencia o reemplazo de titular

## Contexto

Dos variantes del mismo patrón, resueltas con el mismo enfoque:

- **Reemplazo permanente**: un usuario deja el puesto y otro lo reemplaza; hay que trasladarle los accesos de aprobación de compras/facturas.
- **Cobertura temporal**: el titular de un rol de aprobación se ausenta por un período acotado (vacaciones, licencia) y alguien más debe cubrir esa función mientras tanto, con fecha de término definida.

## Problema

Un proceso de negocio (aprobación de Solicitudes de Compra o de facturas) no puede detenerse porque la persona que normalmente aprueba no está disponible. Se necesita que otro usuario tenga exactamente los mismos permisos de aprobación, sin heredar accesos de más ni de menos.

## Diagnóstico

1. Se identificaron con precisión los roles que tenía asignado el titular (no "roles similares", sino los mismos roles exactos que le dan la capacidad de aprobar) — vía revisión de asignación de roles del usuario en SU01.
2. En el caso de cobertura temporal, se definió una **fecha de término explícita** para la asignación (no un acceso indefinido) — evita que el acceso quede vigente después de que el titular vuelve, que es un caso típico de "privilege creep" (ver [`01-conceptos-autorizacion.md`](../01-conceptos-autorizacion.md)).
3. En el caso de reemplazo permanente, se identificó que había **procesos en curso** (solicitudes ya iniciadas por el titular anterior) que debían traspasarse formalmente al nuevo aprobador — esto no es un tema de PFCG, es de continuidad operativa, y quedó pendiente de coordinación directa entre el usuario nuevo y el equipo funcional dueño del proceso.

## Solución

1. **SU01**: asignación directa de los roles de aprobación correspondientes al usuario que cubre o reemplaza, sin generar rol nuevo (los roles ya existían, solo cambia a quién están asignados).
2. Para cobertura temporal: asignación con fecha de inicio y fin definidas en la propia asignación de rol (no un rol permanente que hay que acordarse de remover manualmente).
3. Validación de que la asignación quedó correctamente reflejada en el ambiente productivo y que el usuario puede acceder al Launchpad/transacciones esperadas.
4. Notificación a las partes involucradas (usuario, y en el caso de reemplazo, el equipo funcional) de que el acceso ya está activo.

## Lección / lo que se generalizó

- Una solicitud de "dale los mismos accesos que tenía Fulano" no se resuelve copiando roles al voleo — primero hay que confirmar exactamente qué roles tenía el titular, no asumir por el nombre del puesto.
- Las coberturas temporales **siempre** deberían llevar fecha de término en la asignación misma. Es la diferencia entre "acceso de emergencia trazable" y "acceso que alguien tiene que acordarse de quitar" — este segundo caso es donde se acumulan la mayoría de los hallazgos de auditoría de accesos.
- El acceso técnico (rol asignado) y la continuidad del proceso de negocio (qué pasa con las tareas que quedaron a medio camino) son dos cosas distintas — dar el acceso no resuelve automáticamente lo segundo, y vale la pena dejarlo explícito en el ticket en vez de asumir que "alguien más lo verá".
