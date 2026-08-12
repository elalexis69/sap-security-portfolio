# Proceso end-to-end: de la solicitud al acceso otorgado

Flujo real que sigo en el día a día para atender una solicitud de rol/acceso, genérico y anonimizado.

## Vista general

```
Jira (solicitud) → Catálogo de servicio → CHARM (Change Request) → Testing → Deployment → Cierre
```

## 1. Solicitud (Jira)

- El usuario o su manager crea un ticket en Jira pidiendo un acceso (nuevo rol, cambio de rol, Firefighter, etc.).
- El ticket debe traer: qué necesita, por qué (justificación de negocio), y quién aprueba (manager o dueño del proceso).
- Sin aprobación formal registrada, el ticket no avanza — es el primer control de SoD/compliance.

## 2. Catálogo de servicio

- Muchas solicitudes recurrentes están estandarizadas en un **catálogo**: roles predefinidos para perfiles típicos (ej: "Analista de Compras Regional"), con su aprobador ya definido.
- Si la solicitud calza con el catálogo → proceso más rápido, ya está pre-aprobado el "qué".
- Si es un caso no catalogado (rol custom, combinación atípica) → requiere análisis manual y validación extra de SoD antes de seguir.

## 3. CHARM (Change Request Management)

CHARM es el proceso formal de gestión de cambios en Solution Manager que rige cómo se mueve una modificación de autorización desde que se define hasta que llega a producción. Estados típicos por los que pasa un cambio:

1. **Creación del Change Request**: se documenta qué se va a modificar (rol nuevo, cambio en rol existente, ampliación de org level, etc.), vinculado al ticket de Jira de origen.
2. **Desarrollo/configuración**: el cambio se construye en el sistema de desarrollo (DEV) — se crea o ajusta el rol en PFCG.
3. **Revisión/aprobación técnica**: alguien (par o líder) revisa que el cambio esté bien construido y no introduzca un conflicto de SoD no controlado.
4. **Transporte a Quality/Testing (QAS)**: el cambio se empaqueta en un transporte y se mueve al ambiente de pruebas.
5. **Testing**: se valida que el rol otorgue exactamente lo esperado — ni de más ni de menos. Idealmente lo prueba el usuario final o alguien del equipo funcional dueño del proceso.
6. **Aprobación de paso a Producción**: firma/aprobación formal (normalmente del Change Manager o similar) para autorizar el transporte a PRD.
7. **Deployment a Producción**: se transporta el cambio a PRD en la ventana definida.
8. **Asignación al usuario**: ya con el rol disponible en PRD, se asigna al usuario final.
9. **Cierre del ticket**: se actualiza Jira confirmando que el acceso está otorgado, y se notifica al solicitante.

## 4. Por qué existe esta cadena (y no se hace directo en producción)

- **Trazabilidad**: cada cambio de autorización queda vinculado a un ticket, un transporte y un aprobador — auditable en cualquier momento.
- **Control de calidad**: probar en QAS antes de PRD evita otorgar accesos mal configurados (de más, que es un riesgo; o de menos, que genera un nuevo ticket).
- **Compliance/SOX**: en empresas auditadas, un cambio de autorización sin este rastro documentado es directamente un hallazgo de auditoría.

## 5. Puntos donde suelo intervenir directamente

- Construcción del rol en PFCG (menú + revisión de autorizaciones generadas).
- Validación de que el org level (país/sociedad/centro) esté correctamente parametrizado en roles derivados.
- Otorgamiento y revisión de logs de **Firefighter ID** (ver [`01-conceptos-autorizacion.md`](01-conceptos-autorizacion.md) sección 5).
- Diagnóstico cuando un usuario reporta "no tengo acceso a X" — identificar si falta el rol, falta el org level correcto, o es un problema de otro tipo (ej: bloqueo de usuario).

## 6. Errores recurrentes que he visto (y cómo se detectan)

| Síntoma | Causa típica | Cómo se confirma |
|---|---|---|
| Usuario no ve una transacción esperada | Rol no asignado, o rol asignado pero sin generar perfil actualizado | Revisar SU01 → roles asignados, y comparar con PFCG del rol |
| Usuario ve la transacción pero le falta permiso sobre un dato (ej: una sociedad) | Org level mal parametrizado en el rol derivado | Revisar valores de campo organizacional en PFCG del derivado |
| Cambio "no llegó" a producción | Transporte no liberado o liberado pero no importado en el ambiente destino | Revisar cola de transportes / estado del Change Request en CHARM |
| Acceso duplicado o excesivo | Rol antiguo no removido en un cambio de puesto | Revisión de roles asignados vs. puesto actual del usuario |
