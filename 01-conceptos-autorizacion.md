# Conceptos base de SAP Authorization Management

## 1. El modelo de autorización de SAP

SAP controla qué puede hacer un usuario a través de una cadena:

```
Usuario (SU01) → Roles asignados → Objetos de autorización → Campos de autorización → Valores
```

Un usuario nunca tiene permisos "sueltos": siempre los recibe a través de **roles**, y cada rol agrupa **objetos de autorización** (p. ej. `S_TCODE` para transacciones, `F_BKPF_BUK` para sociedad en Finanzas) con sus valores permitidos.

## 2. Tipos de roles (PFCG)

| Tipo | Qué es | Cuándo se usa |
|---|---|---|
| **Rol simple (single)** | Un solo rol con menú de transacciones + autorizaciones propias | Base de todo: un proceso o función concreta |
| **Rol compuesto (composite)** | Agrupa varios roles simples | Perfil de usuario tipo "Comprador LATAM" = suma de roles simples |
| **Rol derivado (derived)** | Hereda el menú y autorizaciones de un rol maestro, pero permite valores distintos en campos organizacionales (org levels) | Mismo rol para distintos países/sociedades/centros, cambiando solo el org level |
| **Rol maestro** | El rol "padre" del que cuelgan los derivados | Se usa cuando el mismo proceso se repite en varias unidades organizativas |

**Por qué importan los roles derivados en un contexto multi-país (LATAM):** en vez de mantener N roles casi idénticos (uno por país/sociedad), se mantiene un rol maestro y N derivados que solo cambian el org level (sociedad, centro, área de ventas, etc.). Reduce el esfuerzo de mantenimiento y el riesgo de inconsistencias.

## 3. PFCG — la transacción central

`PFCG` (Profile Generator) es donde se crean y mantienen los roles:

- **Pestaña Menu**: transacciones, reportes, links que el usuario verá.
- **Pestaña Authorizations**: genera automáticamente los objetos de autorización según las transacciones del menú, con valores por defecto que hay que revisar/ajustar caso a caso.
- **Pestaña User**: asignación de usuarios al rol (aunque en procesos maduros esto se hace vía workflow/catálogo, no manual).
- **Generar perfil**: el paso final que compila el rol en un perfil de autorización efectivo.

Errores típicos al construir un rol:
- Dejar el objeto `S_TCODE` con transacciones que no están en el menú (rol "más permisivo" de lo que aparenta).
- No revisar campos con `*` (todos los valores) que quedaron así por default del generador.
- Copiar un rol existente sin limpiar autorizaciones heredadas que ya no aplican.

## 4. Segregación de funciones (SoD — Segregation of Duties)

Un usuario no debería poder ejecutar, sin control, dos funciones que en conjunto representan un riesgo (ej: **crear un proveedor** + **aprobar el pago a ese proveedor**). Eso es un conflicto de SoD.

- Se detecta comparando combinaciones de transacciones/roles de un usuario contra una matriz de riesgo (manual con Excel, o automatizado con **SAP GRC Access Control**).
- Si un conflicto es inevitable por el tamaño del equipo, se documenta como **riesgo aceptado con control mitigante** (ej: revisión mensual de esos casos por un tercero).
- Es uno de los primeros puntos que revisa Auditoría/SOX en cualquier compañía cotizada o con matriz que exige compliance.

## 5. Firefighter / Emergency Access Management (EAM)

Acceso temporal y elevado para resolver incidentes que requieren permisos que el usuario no tiene en su día a día (ej: un consultor funcional necesita entrar a corregir un dato en producción a las 11pm).

Cómo funciona en general (vía SAP GRC / Firefighter ID):
1. El usuario solicita (o tiene pre-asignado) un **Firefighter ID** — un usuario técnico con permisos amplios para un módulo/proceso.
2. Se "logea" al Firefighter ID por un tiempo limitado (sesión con log activo).
3. Todo lo que hace bajo ese ID queda **registrado y logueado** (transacciones ejecutadas, cambios realizados).
4. Al cerrar la sesión, se genera un log de revisión (**Firefighter Log Review**) que un supervisor/controller debe revisar y aprobar, dejando constancia de qué se hizo y por qué.

Esto resuelve un problema real: dar acceso amplio permanente es un riesgo de SoD; no dar acceso nunca bloquea la operación en incidentes. Firefighter da acceso amplio pero **trazable, temporal y auditado**.

## 6. Objetos de autorización más comunes (referencia rápida)

| Objeto | Controla |
|---|---|
| `S_TCODE` | Qué transacciones puede ejecutar el usuario |
| `S_USER_GRP` | Sobre qué grupos de usuarios puede operar un administrador (SU01) |
| `S_USER_PRO` | Qué perfiles puede asignar un administrador |
| `S_USER_AGR` | Qué roles puede asignar/mantener un administrador |
| `S_RFC` | Acceso vía RFC (interfaces, conexiones entre sistemas) |
| `S_DEVELOP` | Acceso a desarrollo (ABAP Workbench) — normalmente restringido fuera de Desarrollo |

## 7. Ciclo de vida de un usuario y su relación con roles

- **Onboarding**: alta en SU01 + asignación de roles según el puesto (normalmente vía catálogo/workflow, no ad-hoc).
- **Cambio de puesto / traslado**: revisión de roles — quitar lo que ya no corresponde, agregar lo nuevo. Es el punto donde más se acumulan accesos innecesarios si no hay disciplina ("privilege creep").
- **Offboarding**: bloqueo de usuario + remoción de roles. Debe ser inmediato el día de salida, no "cuando alguien se acuerde".

---
*Nivel de este documento: conocimiento aplicado a diario en operación (roles, PFCG, Firefighter, SoD básico). GRC Access Control automatizado: conocimiento teórico, sin configuración hands-on.*
