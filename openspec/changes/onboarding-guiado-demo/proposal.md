# Proposal

## Why

La demo necesita un contrato verificable para que una persona de 18 a 21 años no bancarizada en Perú pueda completar un onboarding guiado y comprender el estado real de su wallet. El [alcance de onboarding comprometido](../../../docs/alcance/alcance_onboarding.md) delimita ese recorrido y permite medir su duración sin confundirla con la revisión KYC, la activación ni los resultados de un lanzamiento productivo.

## What Changes

### Alcance incluido

- Especificar un flujo web responsive para smartphone con pasos requeridos, avance visible, validaciones y mensajes accionables.
- Registrar un celular ficticio y verificar su titularidad mediante OTP simulado, sin números personales ni SMS reales.
- Utilizar exclusivamente KYC mock y datos de prueba: documentos de tipo `TEST` con prefijo `TEST-`.
- Distinguir los resultados `APPROVED`, `MANUAL_REVIEW` y `REJECTED`, y los estados contractuales de wallet `ACTIVE`, `PENDING` y `REJECTED`.
- Comprobar la resolución de revisiones pendientes usando la capacidad existente del mock, sin una consola independiente.
- Registrar inicio, finalización de pasos y cierre del flujo; reportar la mediana hasta el envío correcto de todos los datos y separar revisión KYC y activación.

### Fuera de alcance

- Pagos a contactos o comercios, QR, historial, comprobantes, callbacks e idempotencia de pagos.
- Reversos, disputas, soporte de pagos, conciliación y operaciones de ledger.
- Consola independiente para revisión manual, KYC real, documentos reales, SMS reales e integraciones productivas.
- Medición de checkout, MAU o CAC; garantías de alcanzar objetivos de lanzamiento o activación de operaciones financieras reales.
- Diseño técnico, decisiones de implementación y plan de tareas en esta etapa.

### Comportamiento que debe preservarse

El baseline es el [alcance del MVP](../../../docs/alcance/alcance_mvp.md) junto con la [épica de negocio](../../../docs/business/epic/peru-wallet-mvp.sp.md); la [historia de onboarding](../../../docs/user-stories/user-onboarding-mvp-completion.sp.md) y el [contrato del mock](../../../docs/openapi.json) aportan contexto de aceptación.

- Conservar el onboarding guiado, el bloqueo ante datos inválidos, los mensajes de corrección y los eventos de avance y cierre.
- No presentar una revisión pendiente ni un rechazo como activación. La aprobación debe corresponder a una wallet confirmada `ACTIVE`.
- Mantener la frontera de duración ya definida por el MVP: inicio del recorrido hasta el envío correcto de los datos; KYC y activación se reportan por separado.
- Mantener el uso exclusivo de datos `TEST`, el aislamiento al grupo asignado y la ausencia de dinero real.
- No modificar los contratos de pagos, límites, idempotencia, historial, soporte ni conciliación que el MVP general incluye: simplemente no se implementan en este cambio.

### Coherencia y delimitaciones frente al baseline

- La épica contempla pagos QR y objetivos trimestrales; el MVP de demo ya excluye QR y no mide checkout, MAU ni CAC. Este cambio respeta esa delimitación y no elimina capacidades del contrato general.
- La historia relaciona activación con el acceso a pagos y pide analítica comercial. El alcance de onboarding limita la entrega a mostrar el estado confirmado de la wallet y medir el recorrido; no agrega una pantalla ni operación de pagos ni analítica comercial.
- Los 8 a 3 minutos siguen siendo una referencia de producto, no un umbral cuya consecución pueda declararse con una demo ni un límite de espera para KYC.
- El segmento de 18 a 21 años es el público de la demo, no una nueva regla de validación de edad ni un requisito documental.
- No se proporcionó un Issue con un contrato alternativo: estas delimitaciones se sustentan en el alcance comprometido y no autorizan cambios adicionales.

## Capabilities

### New Capabilities

- `wallet-onboarding`: recorrido responsive guiado, validación de datos de prueba, celular y OTP simulados, resultados KYC y estado confirmado de wallet.
- `onboarding-measurement`: eventos y medición observable del recorrido del usuario, con tiempos KYC y activación diferenciados.

### Modified Capabilities

Ninguna. No hay capacidades existentes bajo `openspec/specs/`; estas specs formalizan el alcance aprobado sin afirmar que ya exista una implementación funcional.

## Impact

- Afecta al contrato observable del futuro recorrido de onboarding y a sus evidencias de aceptación. El repositorio contiene un generador CrewAI; las instrucciones del agente no equivalen a una interfaz implementada ni a pruebas ejecutadas.
- El mock soporta KYC, consulta de wallet y decisión de revisión pendiente. `APPROVED` corresponde a `ACTIVE`; `MANUAL_REVIEW`, a `PENDING`. Las etiquetas de escenario no sustituyen el estado real.
- **Dependencia pendiente:** el OpenAPI actual no define celular ni OTP. La aceptación integral del recorrido requiere acordar una capacidad de simulación para ese requisito; esta propuesta no inventa endpoints, persistencia, formato de celular, longitud del código, caducidad ni política de reintentos. La carencia del mock no permite omitir la verificación ni afirmar que ya está disponible.
- No se modifica el OpenAPI, el código funcional, los documentos baseline ni otros repositorios. No se decide arquitectura ni tecnología; quedan deliberadamente sin crear los artefactos de diseño y tareas.
