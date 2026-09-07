# Patrones recurrentes — síntesis de los 11 casos documentados

Este documento no repite lo que ya está en cada caso — junta lo que se repite **entre** ellos. Son los principios que uso en la práctica, no teoría copiada de un manual. Sirve para dos cosas: tenerlos a mano en una entrevista sin tener que releer los 11 casos, y como checklist rápido antes de tocar un rol.

## 1. El cambio va en el rol maestro, nunca en el derivado

Aparece en: [Caso 1](casos-practicos/01-ampliar-transaccion-rol-existente.md), [Caso 10](casos-practicos/10-restringir-valor-de-campo-en-objeto.md), [Caso 11](casos-practicos/11-remover-acceso-rol-compartido.md).

Cuando un rol tiene estructura maestro/derivado (multi-país), el ajuste — agregar, restringir o remover — se hace una sola vez en el maestro y se hereda. Editar un derivado directamente "para no tener que tocar los demás países" genera inconsistencia silenciosa: funciona hoy, pero el próximo cambio al maestro no se sabe si pisa o convive con ese parche local.

## 2. Cada acceso es una decisión de alcance, no un "que funcione"

Aparece en: [Caso 3](casos-practicos/03-acceso-solo-lectura-multiples-roles.md), [Caso 7](casos-practicos/07-encontrar-rol-desde-tcode-faltante.md), [Caso 10](casos-practicos/10-restringir-valor-de-campo-en-objeto.md), [Caso 11](casos-practicos/11-remover-acceso-rol-compartido.md).

El primer rol que resuelve el síntoma casi nunca es el correcto. El criterio siempre es el más acotado que resuelve el problema real: actividad display-only cuando piden "ver", un rol Z de negocio específico en vez de un SAP estándar amplio, un valor de campo puntual en vez del objeto completo. Asignar de más para "no tener que volver a tocarlo" es la forma más común de terminar con usuarios sobre-privilegiados.

## 3. La vigencia acotada es el control real en accesos temporales

Aparece en: [Caso 2](casos-practicos/02-cobertura-ausencia-reemplazo-aprobador.md), [Caso 4](casos-practicos/04-asignacion-firefighter.md), [Caso 9](casos-practicos/09-firefighter-soporte-cierre-mes.md).

Ya sea una cobertura de ausencia o un Firefighter, el mecanismo que realmente evita que el acceso se vuelva permanente sin control es la fecha de término explícita en la asignación misma — no la buena memoria de alguien para removerlo después. En Firefighter, esto se combina con un auditor activo que revisa el log: la vigencia limita el "cuándo", el auditor controla el "qué se hizo".

## 4. Verificar antes de tocar, siempre

Aparece en: [Caso 2](casos-practicos/02-cobertura-ausencia-reemplazo-aprobador.md), [Caso 3](casos-practicos/03-acceso-solo-lectura-multiples-roles.md), [Caso 5](casos-practicos/05-replicar-acceso-usuario-referencia.md), [Caso 6](casos-practicos/06-error-grupo-usuario-cua.md), [Caso 7](casos-practicos/07-encontrar-rol-desde-tcode-faltante.md), [Caso 10](casos-practicos/10-restringir-valor-de-campo-en-objeto.md).

Antes de copiar los roles de un usuario de referencia: confirmar cuáles son exactamente, no asumir por el nombre del puesto. Antes de agregar un valor a un campo: revisar qué valores ya tiene, para no pisarlos. Antes de asumir que un grupo de usuario "no existe": confirmar si no existe en ningún lado o solo en el sistema hijo. Antes de asignar un rol por un T-Code faltante: confirmar el T-Code real con `SU53`, no con la descripción del ticket. El patrón es siempre el mismo: el diagnóstico previo es lo que evita el segundo ticket.

## 5. Multi-país y multi-sistema exigen un checklist de cobertura explícito

Aparece en: [Caso 1](casos-practicos/01-ampliar-transaccion-rol-existente.md), [Caso 6](casos-practicos/06-error-grupo-usuario-cua.md), [Caso 10](casos-practicos/10-restringir-valor-de-campo-en-objeto.md), [Caso 11](casos-practicos/11-remover-acceso-rol-compartido.md).

"¿Ya cubrí todos los países / todos los sistemas hijos?" tiene que preguntarse *antes* de cerrar el ticket, no después. En CUA, un grupo de usuario tiene que existir localmente en cada sistema hijo, y `SCUG` permite confirmar el universo completo en vez de ir descubriendo sistemas por accidente. En roles maestro/derivado, un cambio revisado dos veces (¿CL10?, ¿MX10?) es más barato que uno transportado a medias.

## 6. Un ticket no está cerrado hasta la validación funcional real

Aparece en: [Caso 1](casos-practicos/01-ampliar-transaccion-rol-existente.md), [Caso 3](casos-practicos/03-acceso-solo-lectura-multiples-roles.md), [Caso 4](casos-practicos/04-asignacion-firefighter.md), [Caso 7](casos-practicos/07-encontrar-rol-desde-tcode-faltante.md), [Caso 9](casos-practicos/09-firefighter-soporte-cierre-mes.md), [Caso 10](casos-practicos/10-restringir-valor-de-campo-en-objeto.md).

"El transporte llegó a producción" y "el usuario confirmó que funciona" son dos hechos distintos, y solo el segundo cierra el ticket de verdad. Cuando hay varias actividades bajo un mismo cambio (Caso 9), cada una necesita su propia validación del dueño de ese proceso — no una validación genérica al final.

## 7. Un acceso expuesto por más de un camino se ajusta por los dos caminos

Aparece en: [Caso 1](casos-practicos/01-ampliar-transaccion-rol-existente.md), [Caso 3](casos-practicos/03-acceso-solo-lectura-multiples-roles.md), [Caso 11](casos-practicos/11-remover-acceso-rol-compartido.md).

Cuando una transacción está disponible tanto por el menú de PFCG como por un tile de Fiori, agregar o remover solo en uno de los dos deja el ticket a medias — sea porque el usuario no ve la app aunque el objeto de autorización ya lo permita, o porque la app sigue visible en el Launchpad aunque el objeto de autorización ya no lo permita ejecutar.

## 8. Diagnóstico con herramienta correcta antes que solución

Aparece en: [Caso 6](casos-practicos/06-error-grupo-usuario-cua.md), [Caso 7](casos-practicos/07-encontrar-rol-desde-tcode-faltante.md), [Caso 8](casos-practicos/08-auditar-tcodes-de-un-rol.md).

`SU53` / `STAUTHTRACE` para confirmar qué objeto de autorización falta realmente, `SUIM` para ir de rol→transacciones o de transacción→roles (funciona en ambas direcciones), `SCUG` para confirmar el alcance de un modelo de CUA. En los tres casos, el patrón es no adivinar la causa desde la descripción del ticket — hay una transacción de SAP que da la respuesta directa, y usarla es más rápido que iterar a ciegas.

---

*Estos 8 patrones no son una lista cerrada — cada caso nuevo que se documente en [`casos-practicos/`](casos-practicos/) es una oportunidad de confirmar uno existente o de encontrar uno nuevo.*
