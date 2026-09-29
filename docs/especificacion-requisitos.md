# Especificación de requisitos · MediSync

> Campo **Origen** pendiente de actualizar tras la entrevista en vivo con la dupla: marca cada requisito como *Confirmado por cliente* o dejarlo como *Supuesto (Visión del producto)* según corresponda.

## Propósito y alcance

*(Retomado de `vision-del-producto.md`, Unidad 1.)*

MediSync es un asistente digital para consultorios médicos privados que permite a los pacientes agendar y confirmar sus consultas desde el teléfono a cualquier hora, mientras organiza el día del doctor y del personal de recepción para evitar esperas, empalmes de horario y pérdida de notas de cada visita.

**Dentro del alcance:** registro y autenticación de tres roles con vistas diferenciadas; agendamiento, reprogramación y cancelación de citas por el paciente en tiempo real; recordatorios y confirmaciones automáticas; panel de recepción para sala de espera, confirmaciones y cobro; agenda del médico con validación de empalmes.

**Fuera del alcance:** consultas por videollamada, administración de múltiples sucursales, recetas electrónicas con firma digital certificada ante autoridades sanitarias.

## Usuarios y su contexto

| Usuario | Qué necesita del sistema | Qué le preocupa |
|---|---|---|
| Médico | Consultar el historial clínico de forma inmediata y ver su agenda del día ordenada, sin empalmes. | Que el sistema sea lento durante la consulta o exponga datos médicos confidenciales. |
| Paciente | Agendar, consultar, confirmar o reprogramar citas desde su teléfono sin llamar. | Que el proceso sea confuso o que sus citas no queden realmente registradas. |
| Recepcionista | Un panel central para sala de espera, confirmaciones y cobro del día. | Que el sistema permita citas dobles o sea difícil de usar cuando hay mucha gente esperando. |

*(Sección pendiente de enriquecer con lo que salga de la entrevista en vivo — por ejemplo, cómo maneja hoy la recepcionista las urgencias sin horario, o qué tan seguido cambia de dueño un expediente.)*

## Registro de cambios

| Versión | Fecha | Cambio |
|---|---|---|
| 0.1 | *(fecha)* | Primera versión, basada en `vision-del-producto.md`. Todos los requisitos como supuestos. |
| 0.2 | *(fecha)* | Pendiente: actualizar Origen y agregar lo confirmado en la entrevista con la dupla. |

## Requisitos funcionales

**RF-001**
Formulación: El sistema debe permitir el registro y autenticación de tres roles (paciente, médico, recepcionista), cada uno con una vista y permisos distintos.
Criterio de aceptación: Un usuario autenticado solo puede acceder a las pantallas correspondientes a su rol; el intento de acceder a otra vista es rechazado.
Origen: Supuesto (Visión del producto).
Prioridad: Alta.
Relaciones: Base para todos los demás requisitos (control de acceso por rol).

**RF-002**
Formulación: El sistema debe permitir a la recepcionista buscar a un paciente por su nombre para gestionar su cita.
Criterio de aceptación: Al ingresar al menos 3 caracteres del nombre, el sistema muestra coincidencias en menos de 2 segundos.
Origen: Supuesto (Visión del producto).
Prioridad: Media.
Relaciones: Apoya a RF-011 (registrar llegada).

**RF-003**
Formulación: El paciente debe poder agendar una cita eligiendo médico, fecha y hora entre los horarios disponibles en tiempo real.
Criterio de aceptación: El horario elegido queda reservado y visible en la agenda del médico inmediatamente después de confirmar.
Origen: Supuesto (Visión del producto).
Prioridad: Alta.
Relaciones: RF-004, RF-008, RF-010. Realizado por CU-01 Agendar una cita.

**RF-004**
Formulación: El sistema debe validar que no exista empalme de horario antes de confirmar cualquier cita nueva.
Criterio de aceptación: Un intento de agendar sobre un horario ya ocupado es rechazado y se muestran los horarios libres más cercanos del mismo médico.
Origen: Supuesto (Visión del producto).
Prioridad: Alta.
Relaciones: RF-003, RF-005. Ligado a RNF-CONS-001.

**RF-005**
Formulación: El paciente debe poder reprogramar una cita ya agendada a otro horario disponible.
Criterio de aceptación: Al reprogramar, el horario anterior se libera y el nuevo queda reservado en la misma operación.
Origen: Supuesto (Visión del producto).
Prioridad: Media.
Relaciones: RF-003, RF-004. Realizado por CU Reprogramar una cita.

**RF-006**
Formulación: El paciente debe poder cancelar una cita agendada.
Criterio de aceptación: La cancelación queda registrada con fecha y hora, y el estatus de la cita cambia a "cancelada".
Origen: Supuesto (Visión del producto).
Prioridad: Media.
Relaciones: RF-007, RF-009. Realizado por CU Cancelar una cita.

**RF-007**
Formulación: Cuando un paciente cancela con al menos 2 horas de anticipación, el sistema debe liberar automáticamente ese horario y ofrecerlo al primer paciente en lista de espera para ese médico y esa fecha.
Criterio de aceptación: El horario liberado aparece como disponible en menos de 5 segundos y se notifica al primer paciente en espera.
Origen: Supuesto (Visión del producto).
Prioridad: Alta.
Relaciones: RF-006. Ligado a RNF-CONS-002.

**RF-008**
Formulación: El sistema debe enviar recordatorios y solicitudes de confirmación automáticas (notificación push o mensaje) antes de cada consulta.
Criterio de aceptación: El paciente recibe el recordatorio en la ventana de tiempo configurada (por ejemplo, 24 h y 2 h antes) y puede confirmar desde el mismo mensaje.
Origen: Supuesto (Visión del producto).
Prioridad: Media.
Relaciones: RF-003, RF-009.

**RF-009**
Formulación: Si un paciente falta a una cita sin cancelarla con al menos 2 horas de anticipación, el sistema debe registrarlo como inasistencia y notificar a recepción.
Criterio de aceptación: La inasistencia queda visible en el historial del paciente y genera una notificación a la recepcionista el mismo día.
Origen: Supuesto (Visión del producto).
Prioridad: Alta.
Relaciones: RF-006, RF-008.

**RF-010**
Formulación: El sistema debe impedir que un paciente tenga dos citas activas con el mismo médico el mismo día.
Criterio de aceptación: El intento de crear una segunda cita en esas condiciones es rechazado con un mensaje explicando el motivo.
Origen: Supuesto (Visión del producto).
Prioridad: Alta.
Relaciones: RF-003. Ligado a RNF-CONS-001.

**RF-011**
Formulación: La recepcionista debe poder registrar la llegada de un paciente a la sala de espera.
Criterio de aceptación: El estatus de la cita cambia a "en sala de espera" y queda visible en el panel de recepción.
Origen: Supuesto (Visión del producto).
Prioridad: Media.
Relaciones: RF-002. Realizado por CU Registrar llegada a sala de espera.

**RF-012**
Formulación: La recepcionista debe poder marcar el cobro de cada consulta del día.
Criterio de aceptación: Una cita marcada como cobrada queda identificada visualmente distinta del resto en el panel del día.
Origen: Supuesto (Visión del producto).
Prioridad: Media.
Relaciones: RF-011. Realizado por CU Marcar cobro de consulta.

**RF-013**
Formulación: El médico debe poder consultar el historial clínico completo del paciente antes o durante la consulta.
Criterio de aceptación: Al abrir la cita desde la vista clínica, el médico ve diagnósticos previos, notas de evolución y recetas anteriores del paciente.
Origen: Supuesto (Visión del producto).
Prioridad: Alta.
Relaciones: RNF-SEG-001. Realizado por CU Consultar el historial clínico.

**RF-014**
Formulación: El médico debe poder registrar notas, diagnóstico y receta de la consulta actual.
Criterio de aceptación: Lo registrado queda guardado en el expediente del paciente y disponible en consultas futuras.
Origen: Supuesto (Visión del producto).
Prioridad: Alta.
Relaciones: RF-013. Realizado por CU Registrar la consulta de un paciente.

**RF-015**
Formulación: La recepcionista debe poder reagendar en bloque todas las citas del día de un médico que reporta ausencia.
Criterio de aceptación: Al activar el reagendado masivo, el sistema lista las citas afectadas y permite reasignar cada una a un nuevo horario o marcarla como pendiente de contacto telefónico.
Origen: Supuesto (Visión del producto).
Prioridad: Media.
Relaciones: RF-003, RF-004. Realizado por CU Reagendar citas de un médico ausente.

---

## Requisitos no funcionales

**RNF-SEG-001** · Confidencialidad
Formulación: El expediente clínico (diagnósticos, notas, recetas, antecedentes) solo debe ser visible para el rol médico.
Métrica: 0 accesos no autorizados detectados en auditoría por trimestre.
Justificación: El tipo de sistema (de información, a la medida) exige confidencialidad porque maneja datos médicos sensibles; una fuga rompe la confianza en el consultorio y puede tener consecuencias legales para el médico.

**RNF-DISP-001** · Disponibilidad (autoservicio)
Formulación: La vista de autoservicio para que el paciente agende, reprograme o cancele citas debe estar disponible las 24 horas.
Métrica: Uptime mínimo de 99% mensual.
Justificación: Los pacientes deben poder agendar a cualquier hora, incluyendo fuera del horario de atención.

**RNF-DISP-002** · Disponibilidad (recepción)
Formulación: El panel de recepción debe permanecer operativo durante todo el horario de atención del consultorio.
Métrica: Uptime de 99.5% en el horario de atención declarado (por ejemplo, 8:00–20:00).
Justificación: Si el sistema cae durante consultas, la recepcionista vuelve al papel y se pierden citas o cobros.

**RNF-CONS-001** · Consistencia de agenda
Formulación: El sistema debe rechazar cualquier intento de agendar dos citas en el mismo horario para el mismo médico.
Métrica: Tiempo de validación menor a 1 segundo desde que se confirma el intento de agendado.
Justificación: Dos pacientes en el mismo horario repite exactamente el problema que el sistema busca resolver.

**RNF-CONS-002** · Liberación de horarios
Formulación: Al cancelar una cita, el horario debe quedar liberado y visible como disponible en la agenda.
Métrica: Máximo 5 segundos entre la cancelación y la actualización de disponibilidad.
Justificación: Un horario cancelado que nunca se reasigna es tiempo perdido, uno de los problemas centrales del proyecto.

---

## Tabla de trazabilidad (requisitos ↔ casos de uso)

| Requisito | Caso de uso que lo realiza |
|---|---|
| RF-003, RF-004, RF-008, RF-010 | Agendar una cita |
| RF-003, RF-004, RF-005 | Reprogramar una cita |
| RF-006, RF-007, RF-009 | Cancelar una cita |
| RF-002, RF-011 | Registrar llegada a sala de espera |
| RF-011, RF-012 | Marcar cobro de consulta |
| RF-003, RF-004, RF-015 | Reagendar citas de un médico ausente |
| RF-013 | Consultar el historial clínico |
| RF-013, RF-014 | Registrar la consulta de un paciente |
| RF-001 | (transversal a todos los casos de uso — control de acceso) |

*Revisar tras la entrevista: si algún RF no aparece en ningún caso de uso, o algún caso de uso no tiene RF asociado, es señal de que falta un requisito o sobra un caso de uso (ver la reflexión del material del curso sobre el punto 7).*
