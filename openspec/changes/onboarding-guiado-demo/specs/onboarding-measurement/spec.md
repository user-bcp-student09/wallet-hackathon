# Spec Delta

## Purpose

Permitir comprobar la duración del onboarding de la demo según el [alcance comprometido](../../../../../docs/alcance/alcance_onboarding.md) y el [baseline MVP](../../../../../docs/alcance/alcance_mvp.md), diferenciando el tiempo del recorrido del usuario de la revisión KYC y la activación de la wallet.

## ADDED Requirements

### Requirement: Eventos observables del recorrido

El sistema SHALL registrar eventos de inicio del onboarding, finalización correcta de cada paso y cierre del flujo, con instante y asociación al mismo recorrido de prueba. Los eventos SHALL permitir distinguir un envío correcto de un flujo incompleto sin incluir documentos, celulares ni OTP en la salida analítica.

#### Scenario: Recorrido completo

- **WHEN** la persona inicia el onboarding, completa todos los pasos y envía correctamente sus datos
- **THEN** se pueden consultar el instante de inicio, las finalizaciones de cada paso y el cierre por envío correcto del mismo recorrido
- **AND** la secuencia permite identificar qué pasos se completaron.

#### Scenario: Validación fallida

- **WHEN** un paso permanece bloqueado por un dato obligatorio ausente, un valor inválido o una verificación OTP no completada
- **THEN** ese intento no genera una finalización correcta del paso ni un cierre por envío correcto
- **AND** no aparece como un onboarding completado.

#### Scenario: Protección de los datos de prueba en analítica

- **WHEN** se consultan los eventos registrados
- **THEN** permiten relacionar los hitos del recorrido sin mostrar el documento, celular ni código OTP utilizado.

### Requirement: Frontera de duración del flujo del usuario

El sistema SHALL medir la duración desde el inicio del recorrido, cuando se presenta el primer paso, hasta la confirmación del envío correcto de los datos de todos los pasos, incluida la verificación de celular. SHALL excluir las esperas posteriores de revisión KYC y activación, sin usar su resultado para reemplazar el instante de fin.

#### Scenario: Envío correcto seguido de revisión manual

- **WHEN** un recorrido comienza a las 10:00, termina el envío correcto de todos sus datos a las 10:02 y queda en revisión manual hasta las 10:12
- **THEN** su duración de flujo del usuario es de 120 segundos
- **AND** no es de 720 segundos ni permanece sin duración por seguir pendiente la wallet.

#### Scenario: Aprobación y rechazo posteriores al envío

- **WHEN** dos recorridos han enviado correctamente todos sus datos y uno resulta aprobado y el otro rechazado en KYC
- **THEN** ambos conservan la duración hasta su respectivo envío correcto
- **AND** ni la aprobación ni el rechazo alteran esa frontera o implican por sí mismos una wallet activa.

#### Scenario: Envío no completado

- **WHEN** la persona no completa todos los pasos o no se confirma el envío correcto de sus datos
- **THEN** el recorrido no tiene una duración de finalización válida para calcular la mediana
- **AND** no se sustituye su fin por un intento de envío, un error ni un instante estimado.

### Requirement: Mediana reproducible de los recorridos completados

La salida de la demo SHALL reportar la mediana y la cantidad de recorridos con inicio y envío correcto observables en el conjunto de pruebas identificado. SHALL contar cada recorrido una sola vez, excluir los incompletos e identificar la ausencia de muestras; no presentar el objetivo de 3 minutos como un resultado medido.

#### Scenario: Conjunto impar con recorrido incompleto

- **WHEN** el conjunto contiene recorridos completos de 60, 120 y 240 segundos y otro sin envío correcto
- **THEN** la salida indica 3 muestras válidas y una mediana de 120 segundos
- **AND** identifica la exclusión del recorrido incompleto y no lo cuenta como duración cero.

#### Scenario: Conjunto par

- **WHEN** el conjunto contiene únicamente dos recorridos completos de 60 y 120 segundos
- **THEN** la mediana reportada es de 90 segundos y la cantidad de muestras válidas es 2.

#### Scenario: Repetición de una observación

- **WHEN** se vuelve a consultar o aparece repetida la evidencia del mismo cierre por envío correcto
- **THEN** ese recorrido sigue aportando una sola muestra y no altera la mediana por duplicación.

#### Scenario: Sin muestras completas

- **WHEN** ningún recorrido del conjunto tiene inicio y envío correcto confirmados
- **THEN** la salida informa 0 muestras válidas y mediana no disponible
- **AND** no reporta cero segundos ni 3 minutos como mediana.

### Requirement: Tiempos de revisión KYC y activación separados

El sistema SHALL reportar por separado el tiempo desde el inicio de la verificación KYC hasta su decisión final y el tiempo desde el envío correcto hasta la activación confirmada. SHALL identificar los hitos utilizados; una revisión o activación todavía pendiente SHALL mostrarse sin duración final, y una wallet rechazada sin tiempo de activación.

#### Scenario: Revisión manual con activación posterior

- **WHEN** el envío correcto y el inicio de KYC ocurren a las 10:02, la decisión final de revisión a las 10:12 y la activación se confirma a las 10:13
- **THEN** la salida distingue 10 minutos de revisión KYC y 11 minutos hasta la activación
- **AND** esos intervalos no se suman entre sí ni se agregan a la duración del flujo del usuario.

#### Scenario: Revisión pendiente

- **WHEN** un recorrido tiene envío correcto pero KYC y wallet permanecen pendientes
- **THEN** la salida conserva el tiempo del usuario y muestra revisión y activación aún sin duración final
- **AND** no inventa hitos finales ni asigna duración cero a esperas inconclusas.

#### Scenario: Verificación rechazada

- **WHEN** una verificación llega a una decisión final de rechazo
- **THEN** se puede reportar su tiempo de revisión KYC a partir de los hitos observados
- **AND** la activación se identifica como no realizada, sin duración de activación ficticia.

### Requirement: Referencia de producto sin métricas comerciales de demo

La salida SHALL identificar la reducción de 8 a 3 minutos como referencia de producto, separada de la mediana observada en la demo. SHALL limitar las mediciones de esta entrega al onboarding, sin reportar impacto en checkout, MAU ni CAC ni afirmar el cumplimiento de metas de un lanzamiento productivo.

#### Scenario: Presentación de resultados de la demo

- **WHEN** se presenta un conjunto de pruebas y su mediana observada
- **THEN** el resultado diferencia esa evidencia del objetivo de producto de 3 minutos
- **AND** no afirma haber reducido checkout de 30% a 8%, alcanzado MAU de 1,000 a 2,000 ni obtenido CAC de PEN 10.
