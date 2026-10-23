# Spec Delta

## Purpose

Definir el recorrido observable de onboarding de prueba para personas no bancarizadas de 18 a 21 años en Perú, conforme al [alcance comprometido](../../../../../docs/alcance/alcance_onboarding.md), sin ampliar el [MVP](../../../../../docs/alcance/alcance_mvp.md) ni habilitar operaciones financieras reales.

## ADDED Requirements

### Requirement: Recorrido guiado en web responsive para smartphone

El sistema SHALL mostrar la secuencia de pasos requeridos y el paso actual durante el recorrido de onboarding. La interfaz SHALL adaptarse a la pantalla de smartphone manteniendo legibles y utilizables los campos, mensajes, indicadores y acciones.

#### Scenario: Inicio y avance reconocibles

- **WHEN** una persona abre el onboarding y completa correctamente un paso
- **THEN** puede identificar la secuencia requerida, el paso completado y el paso actual
- **AND** conoce la acción necesaria para continuar.

#### Scenario: Formulario y errores en smartphone

- **WHEN** la persona utiliza una pantalla de smartphone y se muestran mensajes de validación
- **THEN** los campos, ayudas, mensajes y acciones permanecen visibles y utilizables sin quedar recortados ni superpuestos
- **AND** el indicador de avance sigue identificando el paso actual.

### Requirement: Bloqueo de datos obligatorios ausentes o inválidos

El sistema SHALL impedir avanzar o completar el envío con datos requeridos ausentes o inválidos, explicar junto a la información afectada cómo corregirla y permitir reintentar tras la corrección. Las reglas SHALL respetar el contrato de prueba aprobado, sin agregar requisitos KYC productivos.

#### Scenario: Falta un dato obligatorio

- **WHEN** la persona intenta continuar dejando vacío un campo requerido
- **THEN** permanece en el paso actual y recibe un mensaje que identifica el dato faltante y cómo completarlo
- **AND** el paso no se presenta como completado.

#### Scenario: Documento fuera del contrato de prueba

- **WHEN** se intenta continuar con un tipo documental distinto de `TEST` o un documento sin prefijo `TEST-`
- **THEN** el flujo bloquea el avance e indica que deben utilizarse documentos ficticios del contrato
- **AND** no envía esos datos a KYC.

#### Scenario: Corrección satisfactoria

- **WHEN** la persona corrige los datos señalados y todos los requisitos del paso son válidos
- **THEN** puede continuar al siguiente paso sin que se le exijan documentos o controles productivos no aprobados.

### Requirement: Registro de celular y titularidad mediante OTP simulado

El sistema SHALL registrar un celular de prueba y exigir la verificación de su titularidad con un OTP simulado antes del envío correcto de todos los pasos. SHALL mostrar ayuda para completar la verificación y no enviar SMS reales ni utilizar números personales reales.

#### Scenario: Código de prueba válido

- **WHEN** la persona registra un celular ficticio del escenario de prueba y presenta el OTP válido correspondiente
- **THEN** el flujo muestra la titularidad de ese celular como verificada y permite completar ese paso
- **AND** no se envía ningún SMS real.

#### Scenario: Código ausente o incorrecto

- **WHEN** la persona intenta completar la verificación sin código o con un OTP incorrecto para el celular de prueba
- **THEN** el flujo mantiene la titularidad sin verificar, impide completar el paso e indica cómo corregir el código.

#### Scenario: Cambio de celular después de verificar

- **WHEN** la persona cambia el celular de prueba que había verificado
- **THEN** el nuevo celular no aparece como verificado por el OTP del anterior
- **AND** debe verificarse su titularidad antes de completar el recorrido.

### Requirement: Datos ficticios y entorno de simulación

El onboarding y KYC SHALL ejecutarse exclusivamente con el mock de pruebas del grupo asignado y datos ficticios autorizados. El recorrido SHALL informar que es una demo, solicitar datos de prueba y no ejecutar KYC productivo ni operaciones financieras reales.

#### Scenario: Demostración con datos TEST

- **WHEN** se completa un onboarding con el juego de datos de prueba autorizado
- **THEN** la verificación utiliza tipo documental `TEST` y un documento con prefijo `TEST-` en el grupo asignado
- **AND** no utiliza documentos o celulares personales reales ni proveedores productivos
- **AND** la interfaz identifica el carácter simulado del registro y la activación.

### Requirement: Resultado de onboarding coherente con la wallet

El sistema SHALL mostrar el resultado de onboarding y el estado confirmado por el mock: `APPROVED` con wallet `ACTIVE`, `MANUAL_REVIEW` con wallet `PENDING`, o `REJECTED` con wallet `REJECTED`. SHALL explicar el siguiente estado o acción sin presentar una revisión pendiente ni un rechazo como activación.

#### Scenario: Aprobación y activación confirmadas

- **WHEN** el mock devuelve el escenario `APPROVED` y confirma la wallet `ACTIVE`
- **THEN** la persona ve el registro aprobado y la wallet activa de prueba
- **AND** no se ejecuta ningún pago ni operación financiera real.

#### Scenario: Revisión manual pendiente

- **WHEN** el mock devuelve `MANUAL_REVIEW` y la wallet `PENDING`
- **THEN** la persona ve que su registro requiere revisión manual y que la wallet sigue pendiente
- **AND** no recibe una confirmación de wallet activa ni de registro rechazado.

#### Scenario: Rechazo confirmado

- **WHEN** el mock devuelve `REJECTED` con wallet `REJECTED`
- **THEN** la persona ve claramente el rechazo y la ausencia de activación
- **AND** la finalización del envío de datos no se presenta como aprobación.

#### Scenario: Sin confirmación de estado

- **WHEN** no se dispone de una respuesta que confirme el estado de la wallet
- **THEN** el flujo indica que no puede confirmar el resultado
- **AND** no inventa una aprobación, un rechazo ni una revisión manual.

### Requirement: Actualización de una revisión manual resuelta

El recorrido SHALL permitir observar la decisión final de una verificación pendiente resuelta mediante la capacidad existente del mock, sin requerir una consola independiente de soporte. La nueva visualización SHALL coincidir con el estado confirmado de esa misma verificación y wallet.

#### Scenario: Decisión de activar

- **WHEN** una verificación `PENDING` recibe una decisión `ACTIVE` mediante el mock y se vuelve a consultar su resultado en el recorrido
- **THEN** la interfaz deja de mostrar revisión pendiente y muestra la wallet activa de prueba correspondiente.

#### Scenario: Decisión de rechazar

- **WHEN** una verificación `PENDING` recibe una decisión `REJECTED` mediante el mock y se vuelve a consultar su resultado en el recorrido
- **THEN** la interfaz muestra el rechazo de esa wallet y no una activación.
