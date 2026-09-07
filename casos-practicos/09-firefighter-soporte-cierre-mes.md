# Caso 9 — Firefighter para soporte de cierre de mes (varias actividades, una sola ventana)

## Contexto

Durante el cierre de mes, una persona del equipo de finanzas necesitaba apoyar en varias actividades puntuales que no correspondían a su acceso habitual: reasignar documentos pendientes de aprobación de otro usuario, apoyar en revaluación fiscal de activos fijos, y ayudar a configurar una variante para ejecución de pagos. Ninguna de estas tareas era parte de su rol permanente.

## Problema

Se necesitaba habilitar temporalmente varias capacidades distintas para una sola persona, durante una ventana acotada de días (el período de cierre), sin crear tres solicitudes de acceso permanente separadas para tareas que solo se necesitaban una vez.

## Diagnóstico

Se determinó que el caso calzaba exactamente con el propósito de un **Firefighter ID**: múltiples actividades de soporte, de alcance conocido y acotado en el tiempo, con necesidad de trazabilidad de lo que se hizo — más que con dar de alta accesos permanentes nuevos (ver [`01-conceptos-autorizacion.md`](../01-conceptos-autorizacion.md), sección 5, y el [Caso 4](04-asignacion-firefighter.md) para el mecanismo base).

## Solución

1. Se activó el Firefighter ID correspondiente para el usuario, con vigencia acotada a los días del cierre de mes (no al mes completo, solo a la ventana real necesaria).
2. Durante esa vigencia se asignaron, además, un par de **roles técnicos base** requeridos únicamente para poder operar con el Firefighter activo — asignados solo por la duración del Firefighter, según política de accesos de emergencia (no de forma permanente).
3. Con el acceso activo, se ejecutaron las tres actividades de soporte (reasignación de aprobaciones pendientes, apoyo en revaluación de activos, configuración de variante de pagos) bajo el mismo Firefighter, en la misma ventana.
4. Cada actividad fue validada por el responsable de ese proceso específico (no una validación genérica al final, sino una por cada tipo de actividad).
5. Se dejó registro explícito de quién aprobó el uso del Firefighter y quién hizo seguimiento posterior, además de las evidencias de activación, asignación y validación.

## Lección / lo que se generalizó

- Un Firefighter no tiene que limitarse a "una tarea puntual": puede cubrir varias actividades relacionadas dentro de la misma ventana de tiempo, siempre que todas calcen dentro del alcance de ese Firefighter ID y de la ventana de vigencia acotada.
- Los roles técnicos necesarios para operar el Firefighter (roles base de activación) deben tener la misma vigencia acotada que el propio Firefighter — no conviene que sobrevivan al período de soporte "por si se necesitan de nuevo".
- Cuando el soporte cubre actividades de distintos procesos (aprobaciones, activos fijos, pagos), cada una necesita su propia validación por el dueño de ese proceso — el hecho de que todo haya ocurrido "bajo el mismo Firefighter" no reemplaza la validación funcional específica de cada tarea.
- Registrar explícitamente aprobador y responsable de seguimiento (más allá del auditor técnico del Firefighter ID) deja un rastro más completo para una eventual revisión — especialmente cuando el soporte tocó varios procesos de negocio distintos en la misma ventana.
