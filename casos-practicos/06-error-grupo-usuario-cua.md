# Caso 6 — Resolver "User group does not exist" en la distribución de CUA

## Contexto

En un paisaje SAP con **CUA (Central User Administration)** — un sistema central que administra usuarios y distribuye los cambios a varios sistemas "hijos" — un cambio de grupo de usuario hecho desde el sistema central no se estaba propagando a uno de los sistemas hijos.

## Problema

Al modificar un usuario desde la CUA y distribuir el cambio, el log de distribución mostraba el error:

```
User group <grupo> does not exist
```

El cambio no se replicaba al sistema hijo afectado, y el usuario quedaba con su grupo anterior en ese sistema — inconsistente respecto al resto del paisaje.

## Diagnóstico

1. Se confirmó que el grupo de usuario sí existía en el sistema central (CUA), lo que descartaba un error de tipeo o de configuración en el origen.
2. Se identificó la causa real: **CUA no crea automáticamente los grupos de usuario en los sistemas hijos**. Solo distribuye la *asignación* de un usuario a un grupo — el grupo mismo tiene que existir de antemano, localmente, en cada sistema hijo.
3. Se verificó qué sistemas formaban parte del modelo de distribución de esa CUA (transacción `SCUG`), para saber en cuáles había que confirmar la existencia del grupo.

## Solución

1. Se ingresó al sistema hijo afectado y se creó el grupo de usuario faltante mediante la transacción `SUGR` (nombre del grupo + descripción).
2. Se repitió la verificación/creación en cada sistema perteneciente al modelo de CUA, no solo en el que había fallado — para evitar que el mismo error apareciera después en otro sistema hijo con la misma configuración pendiente.
3. Se volvió a modificar el usuario desde la CUA (`SU01`), reasignando el grupo, y se distribuyó el cambio nuevamente.
4. Se validó el log de distribución, confirmando que el error ya no aparecía y que el cambio se había propagado correctamente.

## Lección / lo que se generalizó

- En un modelo de CUA, un objeto que existe en el sistema central no significa que exista en todos los sistemas hijos — varios objetos (grupos de usuario, algunos parámetros) requieren mantenimiento local en cada sistema, aunque la administración del usuario en sí sea centralizada.
- Ante un error de distribución de CUA, el primer diagnóstico útil es diferenciar "no existe en ningún lado" de "existe en el central pero no en el hijo" — son causas y soluciones distintas.
- `SCUG` es la forma correcta de confirmar qué sistemas pertenecen al modelo antes de "ir arreglando de a uno" reactivamente — evita descubrir el mismo problema en un tercer sistema una semana después.
