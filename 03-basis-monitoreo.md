# BASIS — nivel actual: monitoreo

Autoevaluación honesta. No me sobrevendo: hago **monitoreo**, no administración BASIS completa. Este documento sirve para tener claro qué sé, qué no, y qué me falta si quisiera subir de nivel.

## Lo que sí sé / hago

- Monitoreo básico de la disponibilidad del sistema (¿está arriba o caído?).
- Revisión de jobs (`SM37`) para confirmar que procesos batch relevantes a autorizaciones (ej. sincronizaciones de usuarios, jobs de GRC) corrieron bien.
- Revisión de logs de usuario/sesión relacionados a mis procesos (Firefighter, bloqueos, etc.).
- Identificar cuándo un problema "no es mío" (autorización) y corresponde escalarlo a BASIS real (ej: performance, memoria, problemas de conectividad RFC).

## Lo que NO sé / no he hecho hands-on

- Gestión de transportes a nivel de sistema (STMS) más allá de seguir el estado de un transporte específico.
- Instalación/actualización de sistemas (upgrades, kernel, support packages).
- Tuning de performance (memoria, work processes, buffers).
- Administración de bases de datos subyacente (HANA admin).
- Configuración de conexiones RFC / gateway a nivel de sistema (solo entiendo qué controla `S_RFC` desde el lado de autorizaciones).
- Client copy / client administration (SCC4, SCCL).

## Por qué importa marcarlo así

En una entrevista o conversación de escalada, es mejor decir "monitoreo BASIS, no administración" con precisión que dejar la duda. Sobrevenderse en BASIS es fácil de detectar con dos preguntas técnicas y resta credibilidad a todo lo demás que sí domino (que es bastante).

## Si quisiera subir de nivel en BASIS (no es el foco actual, pero queda anotado)

- [ ] Curso base de SAP BASIS Administration (S/4HANA)
- [ ] Práctica en STMS más allá de seguimiento (crear/liberar transportes)
- [ ] Familiarizarme con SAP HANA Cockpit / administración básica de BD
- [ ] Entender la arquitectura de gateway/RFC a nivel de sistema, no solo el objeto de autorización

No es prioridad inmediata — mi especialización real y donde tengo ventaja competitiva es **Authorization & Roles**, no BASIS. Este documento existe para ser honesto al respecto, no para plantear un plan de estudio en paralelo.
