# Glosario — SAP Authorization Management

Términos ordenados alfabéticamente. Pensado como referencia rápida y repaso para la certificación C_SEC.

| Término | Definición corta |
|---|---|
| **Authorization Object** (Objeto de autorización) | Estructura que agrupa campos que controlan un tipo de acceso (ej: `S_TCODE` controla transacciones). Cada rol se compone de varios de estos con valores asignados. |
| **CHARM** | Change Request Management — proceso formal en Solution Manager para gestionar cambios (incluyendo autorizaciones) desde su creación hasta el deploy en producción. |
| **Composite Role** (Rol compuesto) | Rol que agrupa varios roles simples, usado para representar un perfil de puesto completo. |
| **Derived Role** (Rol derivado) | Rol que hereda menú y autorizaciones de un rol maestro, variando solo los valores de campos organizacionales (org levels). |
| **Firefighter / EAM** (Emergency Access Management) | Mecanismo (típicamente vía SAP GRC) para otorgar acceso temporal elevado, con logging y revisión posterior obligatoria. |
| **GRC Access Control** | Suite de SAP para gobernanza de accesos: detección automática de SoD, workflows de request/approval, Firefighter, revisión periódica de accesos (User Access Review). |
| **Master Role** (Rol maestro) | Rol "padre" del cual cuelgan los roles derivados. |
| **Org Level** (Nivel organizacional) | Campo de un rol (ej: sociedad, centro, área de ventas) que se deja abierto en el rol maestro y se fija en cada rol derivado. |
| **PFCG** | Transacción central de SAP para crear y mantener roles (Profile Generator). |
| **Privilege Creep** | Acumulación de accesos innecesarios en un usuario a lo largo del tiempo (típicamente por cambios de puesto sin revisión de roles). |
| **Profile** (Perfil) | Resultado técnico compilado al generar un rol en PFCG; es lo que efectivamente se asigna al usuario detrás de escena. |
| **RFC** | Remote Function Call — mecanismo de comunicación entre sistemas SAP (o SAP y terceros), controlado por el objeto `S_RFC`. |
| **Single Role** (Rol simple) | Unidad base de un rol: un menú de transacciones con sus autorizaciones asociadas. |
| **SoD** (Segregation of Duties / Segregación de funciones) | Principio de control: ninguna persona debería poder ejecutar sola dos funciones que en conjunto representan un riesgo (ej: crear proveedor + aprobar su pago). |
| **SOX** | Sarbanes-Oxley Act — regulación (EE.UU., aplica a empresas cotizadas o sus filiales) que exige controles auditables sobre procesos financieros, incluyendo accesos SAP. |
| **SU01** | Transacción para administrar usuarios (creación, bloqueo, asignación de roles). |
| **SUIM** | Herramienta de reporting de seguridad (User Information System) — para consultar qué usuarios tienen qué roles/autorizaciones. |
| **Transport** (Transporte) | Paquete que mueve un cambio de configuración/desarrollo (incluye roles) de un ambiente SAP a otro (DEV → QAS → PRD). |
| **User Access Review** | Revisión periódica (trimestral/anual típicamente) de qué accesos tiene cada usuario, para detectar y remover lo que ya no corresponde. |
