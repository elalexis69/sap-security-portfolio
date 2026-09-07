# Caso 11 — Remover transacciones de un rol usado por varios perfiles

## Contexto

Un rol regional traía incluidas un par de transacciones que, en la práctica, solo debían estar disponibles para perfiles específicos (analista de inventario, gerente de contabilidad) — no para todos los perfiles que compartían ese rol. Se solicitó remover esas transacciones del rol en cuestión.

## Problema

A diferencia de agregar un acceso, remover uno tiene un riesgo distinto: si el rol es compartido por varios perfiles o países, hay que estar seguro de qué se está quitando, de dónde exactamente, y confirmar que nadie que sí necesita ese acceso quede sin él.

## Diagnóstico

1. Se identificó el rol maestro donde vivían las transacciones a remover, y se confirmó que esas transacciones también estaban expuestas vía Fiori (catálogo, space y page) — no bastaba con tocar PFCG, había que limpiar ambos lados.
2. Se registró el requerimiento de remoción en la planilla de control, dejando explícito qué se removía y por qué (a diferencia de una adición, una remoción sin ese registro es más difícil de auditar después: "¿por qué ya no está esto?").

## Solución

1. **Fiori**: se removieron las aplicaciones correspondientes del catálogo, del space y de la page, validando después que ya no quedaran visibles en el Launchpad.
2. **PFCG (rol maestro)**: se removieron las transacciones del menú y se eliminaron los accesos asociados en los objetos de autorización correspondientes, se generó el perfil con herencia y se validó el estado consistente.
3. **Multi-país**: al tratarse de un rol maestro con derivados por país, había que confirmar la actualización en **todos** los derivados relevantes — en este caso, durante la propia ejecución surgió la duda explícita de si faltaba replicar el ajuste en el derivado de otro país, lo que obligó a volver atrás y confirmarlo antes de dar por cerrado el cambio.
4. Se transportó el cambio vía ChaRM hasta los ambientes correspondientes.

## Lección / lo que se generalizó

- Remover un acceso de un rol compartido exige primero confirmar **para quién sigue siendo necesario** ese acceso — no solo "para quién ya no lo es". Si el rol sirve a varios perfiles, la remoción tiene que ser quirúrgica, no un recorte general.
- Un acceso expuesto por dos caminos (menú PFCG + catálogo Fiori) hay que removerlo por los dos caminos. Limpiar solo PFCG y dejar la app visible en el Launchpad es un cierre de ticket incompleto, aunque el objeto de autorización ya no lo permita ejecutar.
- En una estructura maestro/derivado multi-país, la pregunta "¿ya cubrí todos los países relevantes?" debería hacerse **antes** de dar el cambio por terminado, no después de haberlo transportado. Duplicar el chequeo de cobertura por país es más barato que descubrir después que un país quedó con el acceso que se suponía debía perder.
