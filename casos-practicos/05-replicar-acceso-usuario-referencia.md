# Caso 5 — Restablecer acceso replicando roles de un usuario de referencia

## Contexto

Tras una actualización del sistema, un usuario perdió el acceso a un conjunto de aplicaciones Fiori que normalmente usaba (en este caso, herramientas de integración con SAP Business Objects for Office). Se solicitó restablecerle el acceso "igual al de un compañero que sí lo tiene" — un patrón habitual cuando dos personas ocupan un rol de negocio equivalente.

## Problema

El usuario ya no tenía disponibles las aplicaciones necesarias para su trabajo diario, tras una actualización que le hizo perder autorizaciones que antes sí tenía. La solicitud no especificaba roles exactos, solo "que quede como el compañero X".

## Diagnóstico

1. Se identificó al usuario de referencia (el compañero cuyo acceso funcionaba correctamente) y se revisaron **todos** los roles que tenía asignados en el ambiente correspondiente — no solo los que "sonaban" relacionados a Business Objects, para no dejar afuera un rol base necesario.
2. Se confirmó que el conjunto de roles del usuario de referencia era coherente con un perfil de negocio ya conocido (mismo tipo de puesto), lo que validaba que replicarlo completo era seguro y no iba a sobre-otorgar acceso.
3. Se determinó que el caso no requería tocar PFCG ni ajustar objetos de autorización — los roles ya existían y estaban correctamente configurados; el problema era puramente de asignación al usuario afectado.

## Solución

1. Se asignaron al usuario afectado, vía administración de usuarios (SU01), los mismos roles que tenía el usuario de referencia en el ambiente correspondiente.
2. No fue necesario generar ninguna orden de transporte ni modificar roles — al ser asignación de roles ya existentes, el cambio se reflejó de inmediato.
3. Se validó que la asignación quedara correctamente aplicada.
4. Se notificó al solicitante y al usuario de referencia para que confirmaran la validación funcional del acceso restablecido.

## Lección / lo que se generalizó

- "Dale el mismo acceso que tiene fulano" es una solicitud común y, cuando el usuario de referencia realmente ocupa un puesto equivalente, es válida — pero exige revisar el **set completo** de roles del usuario de referencia, no adivinar cuáles "deberían" ser relevantes.
- No todo problema de acceso requiere tocar PFCG. Antes de pensar en modificar un rol, vale la pena confirmar si el rol correcto ya existe y el problema es solo de asignación — es más rápido, más seguro (no introduce cambios nuevos que probar) y no requiere transporte.
- Este tipo de caso es también una alerta temprana: si una actualización de sistema hace que un usuario pierda accesos que antes tenía sin que nadie lo haya solicitado, vale la pena revisar si afectó a más gente del mismo perfil, no solo a quien reportó el problema.
