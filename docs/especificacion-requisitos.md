# Especificación de requisitos · MediSync


## Propósito y alcance


MediSync es un asistente digital para consultorios médicos privados que permite a los pacientes agendar y confirmar sus consultas desde el teléfono a cualquier hora, mientras organiza el día del doctor y del personal de recepción para evitar esperas, empalmes de horario y pérdida de notas de cada visita.

**Dentro del alcance:** registro y autenticación de tres roles con vistas diferenciadas; agendamiento, reprogramación y cancelación de citas por el paciente en tiempo real; recordatorios y confirmaciones automáticas; panel de recepción para sala de espera, confirmaciones y cobro; agenda del médico con validación de empalmes.

**Fuera del alcance:** consultas por videollamada, administración de múltiples sucursales, recetas electrónicas con firma digital certificada ante autoridades sanitarias.

## Usuarios y su contexto

| Usuario | Qué necesita del sistema | Qué le preocupa |
|---|---|---|
| Médico | Consultar el historial clínico de forma inmediata y ver su agenda del día ordenada, sin empalmes. | Que el sistema sea lento durante la consulta o exponga datos médicos confidenciales. |
| Paciente | Agendar, consultar, confirmar o reprogramar citas desde su teléfono sin llamar. | Que el proceso sea confuso o que sus citas no queden realmente registradas. |
| Recepcionista | Un panel central para sala de espera, confirmaciones y cobro del día. | Que el sistema permita citas dobles o sea difícil de usar cuando hay mucha gente esperando. |

*(Enriquecido con la entrevista simulada — ver `guion-entrevista.md` para el detalle completo pregunta por pregunta:)*
- Hoy son dos recepcionistas en turnos (mañana/tarde) compartiendo un solo cuaderno; de ahí salen la mayoría de los empalmes y citas duplicadas, no de que el paciente pida dos veces.
- El cobro hoy se registra también en una terminal de pago física aparte; el sistema no la reemplaza, solo refleja que ya se cobró ahí.
- Los recordatorios funcionan mejor por SMS/WhatsApp que por notificación push, porque es lo que los pacientes ya revisan a diario; por el mismo canal confirman si van a llegar.
- A los pacientes nuevos se les pide llegar 15 minutos antes de su primera cita para registrarlos.
- Antes de marcar inasistencia, la recepcionista espera ~15 minutos de tolerancia; tras 2-3 inasistencias seguidas, exige confirmación por WhatsApp el día antes o no aparta el horario.
- No existe una lista de espera formal — hoy es memoria o notas sueltas de quién preguntó por un horario ya ocupado.
- Las urgencias se insertan entre las citas ya agendadas del día, lo que atrasa a los demás pacientes esa jornada.

## Registro de cambios

| Versión | Fecha | Cambio |
|---|---|---|
| 0.1 | *(fecha)* | Primera versión, basada en `vision-del-producto.md`. Todos los requisitos como supuestos. |
| 0.2 | *(fecha, simulada)* | Entrevista simulada: se confirma el límite de 2h, la restricción de citas duplicadas y la confidencialidad del expediente. Se corrige RF-008 (SMS/WhatsApp en vez de solo push) y se agrega nota sobre la terminal de pago aparte en RF-012. Pendiente sustituir por la entrevista real. |
| 0.3 | *(fecha, simulada)* | Respuestas completas de la entrevista simulada incorporadas. Se agrega RF-016 (registro de pacientes nuevos), se ajusta RF-009 con tolerancia de 15 min y regla de confirmación tras inasistencias repetidas, y se aclara en RF-007 que la lista de espera es informal (el sistema la formaliza). Pendiente sustituir por la entrevista real. |

## Requisitos funcionales

| ID | Formulación | Criterio de aceptación | Origen | Prioridad | Relaciones |
|---|---|---|---|---|---|
| RF-001 | El sistema debe permitir el registro y autenticación de tres roles (paciente, médico, recepcionista), cada uno con una vista y permisos distintos. El rol recepcionista debe soportar más de una sesión simultánea, ya que el puesto se cubre en turnos. | Un usuario autenticado solo puede acceder a las pantallas de su rol; dos recepcionistas distintas pueden ver el mismo panel del día al mismo tiempo. | Confirmado por cliente (entrevista simulada) | Alta | Base para todos los demás requisitos (control de acceso por rol) |
| RF-002 | El sistema debe permitir a la recepcionista buscar a un paciente por su nombre para gestionar su cita. | Al ingresar al menos 3 caracteres del nombre, el sistema muestra coincidencias en menos de 2 segundos. | Supuesto (Visión del producto) | Media | Apoya a RF-011 |
| RF-003 | El paciente debe poder agendar una cita eligiendo médico, fecha y hora entre los horarios disponibles en tiempo real. | El horario elegido queda reservado y visible en la agenda del médico inmediatamente después de confirmar. | Supuesto (Visión del producto) | Alta | RF-004, RF-008, RF-010. Realizado por CU-01 Agendar una cita |
| RF-004 | El sistema debe validar que no exista empalme de horario antes de confirmar cualquier cita nueva. | Un intento de agendar sobre un horario ya ocupado es rechazado y se muestran los horarios libres más cercanos del mismo médico. | Supuesto (Visión del producto) | Alta | RF-003, RF-005. Ligado a RNF-CONS-001 |
| RF-005 | El paciente debe poder reprogramar una cita ya agendada a otro horario disponible. | Al reprogramar, el horario anterior se libera y el nuevo queda reservado en la misma operación. | Supuesto (Visión del producto) | Media | RF-003, RF-004. Realizado por CU Reprogramar una cita |
| RF-006 | El paciente debe poder cancelar una cita agendada. | La cancelación queda registrada con fecha y hora, y el estatus de la cita cambia a "cancelada". | Confirmado por cliente (entrevista simulada) | Media | RF-007, RF-009. Realizado por CU Cancelar una cita |
| RF-007 | Cuando un paciente cancela con al menos 2 horas de anticipación, el sistema debe liberar automáticamente ese horario y ofrecerlo al primer paciente en una lista de espera por médico y fecha (hoy esa lista es informal — recepción la lleva de memoria o en notas sueltas; el sistema debe formalizarla). | El horario liberado aparece como disponible en menos de 5 segundos y se notifica al primer paciente en espera. | Confirmado por cliente (entrevista simulada) | Alta | RF-006. Ligado a RNF-CONS-002 |
| RF-008 | El sistema debe enviar recordatorios y solicitudes de confirmación automáticas por SMS o WhatsApp antes de cada consulta (no solo notificación push, porque la mayoría de los pacientes no revisa la app a diario). | El paciente recibe el recordatorio por SMS/WhatsApp en la ventana configurada (ej. 24 h y 2 h antes) y puede confirmar desde el mismo mensaje. | Confirmado por cliente (entrevista simulada; corrige el canal original) | Media | RF-003, RF-009 |
| RF-009 | Si un paciente no llega a una cita, sin haberla cancelado con al menos 2 horas de anticipación, el sistema debe esperar una tolerancia de 15 minutos tras la hora agendada y después registrarlo como inasistencia, notificando a recepción. Si un paciente acumula 2 o más inasistencias, sus citas futuras deben exigir confirmación por SMS/WhatsApp el día anterior antes de mantenerle el horario apartado. | La inasistencia queda visible en el historial del paciente y genera una notificación a la recepcionista pasados los 15 minutos de tolerancia; al tercer registro de inasistencia, el sistema marca la cuenta del paciente como "requiere confirmación previa". | Confirmado por cliente (entrevista simulada; agrega la tolerancia de 15 min y la regla de confirmación tras inasistencias repetidas) | Alta | RF-006, RF-008 |
| RF-010 | El sistema debe impedir que un paciente tenga dos citas activas con el mismo médico el mismo día. | El intento de crear una segunda cita en esas condiciones es rechazado con un mensaje explicando el motivo. | Confirmado por cliente (entrevista simulada; la dupla reportó que esto ya les ha pasado por error al agendar por teléfono) | Alta | RF-003. Ligado a RNF-CONS-001 |
| RF-011 | La recepcionista debe poder registrar la llegada de un paciente a la sala de espera. | El estatus de la cita cambia a "en sala de espera" y queda visible en el panel de recepción. | Supuesto (Visión del producto) | Media | RF-002. Realizado por CU Registrar llegada a sala de espera |
| RF-012 | La recepcionista debe poder marcar el cobro de cada consulta del día. El sistema no reemplaza la terminal de pago física que ya usan; solo refleja que el cobro ya se hizo ahí. | Una cita marcada como cobrada queda identificada visualmente distinta del resto en el panel del día. | Confirmado por cliente (entrevista simulada; aclara que el cobro real sigue siendo en terminal aparte) | Media | RF-011. Realizado por CU Marcar cobro de consulta |
| RF-013 | El médico debe poder consultar el historial clínico completo del paciente antes o durante la consulta. | Al abrir la cita desde la vista clínica, el médico ve diagnósticos previos, notas de evolución y recetas anteriores. | Confirmado por cliente (entrevista simulada) | Alta | RNF-SEG-001. Realizado por CU Consultar el historial clínico |
| RF-014 | El médico debe poder registrar notas, diagnóstico y receta de la consulta actual. | Lo registrado queda guardado en el expediente del paciente y disponible en consultas futuras. | Supuesto (Visión del producto) | Alta | RF-013. Realizado por CU Registrar la consulta de un paciente |
| RF-015 | La recepcionista debe poder reagendar en bloque todas las citas del día de un médico que reporta ausencia. | Al activar el reagendado masivo, el sistema lista las citas afectadas y permite reasignar cada una o marcarla pendiente de contacto telefónico. | Supuesto (Visión del producto) | Media | RF-003, RF-004. Realizado por CU Reagendar citas de un médico ausente |
| RF-016 | Al agendar la primera cita de un paciente nuevo, el sistema debe indicarle que debe llegar 15 minutos antes de su horario para completar su registro. | La confirmación de la cita de un paciente marcado como "primera vez" incluye el aviso de llegar 15 minutos antes. | Confirmado por cliente (entrevista simulada) | Media | RF-003. Precondición de CU-01 Agendar una cita para pacientes nuevos |

---

## Requisitos no funcionales

| ID | Atributo | Formulación | Métrica | Justificación |
|---|---|---|---|---|
| RNF-SEG-001 | Confidencialidad | El expediente clínico (diagnósticos, notas, recetas, antecedentes) solo debe ser visible para el rol médico. | 0 accesos no autorizados detectados en auditoría por trimestre. | El tipo de sistema (de información, a la medida) exige confidencialidad por manejar datos médicos sensibles; una fuga rompe la confianza en el consultorio y puede tener consecuencias legales para el médico. |
| RNF-DISP-001 | Disponibilidad (autoservicio) | La vista de autoservicio para que el paciente agende, reprograme o cancele citas debe estar disponible las 24 horas. | Uptime mínimo de 99% mensual. | Confirmado: hoy ya le llegan mensajes de pacientes por WhatsApp en la noche preguntando por horarios, aunque la recepcionista solo responde hasta el día siguiente — el autoservicio sí debe estar disponible de madrugada aunque recepción no esté trabajando a esa hora. |
| RNF-DISP-002 | Disponibilidad (recepción) | El panel de recepción debe permanecer operativo durante todo el horario de atención del consultorio. | Uptime de 99.5% en el horario declarado (ej. 8:00–20:00). | Si el sistema cae durante consultas, la recepcionista vuelve al papel y se pierden citas o cobros. |
| RNF-CONS-001 | Consistencia de agenda | El sistema debe rechazar cualquier intento de agendar dos citas en el mismo horario para el mismo médico. | Tiempo de validación menor a 1 segundo desde que se confirma el intento de agendado. | Dos pacientes en el mismo horario repite exactamente el problema que el sistema busca resolver. |
| RNF-CONS-002 | Liberación de horarios | Al cancelar una cita, el horario debe quedar liberado y visible como disponible en la agenda. | Máximo 5 segundos entre la cancelación y la actualización de disponibilidad. | Un horario cancelado que nunca se reasigna es tiempo perdido, uno de los problemas centrales del proyecto. |

---

## Tabla de trazabilidad (requisitos ↔ casos de uso)

| Requisito | Caso de uso que lo realiza |
|---|---|
| RF-003, RF-004, RF-008, RF-010, RF-016 | Agendar una cita |
| RF-003, RF-004, RF-005 | Reprogramar una cita |
| RF-006, RF-007, RF-009 | Cancelar una cita |
| RF-002, RF-011 | Registrar llegada a sala de espera |
| RF-011, RF-012 | Marcar cobro de consulta |
| RF-003, RF-004, RF-015 | Reagendar citas de un médico ausente |
| RF-013 | Consultar el historial clínico |
| RF-013, RF-014 | Registrar la consulta de un paciente |
| RF-001 | (transversal a todos los casos de uso — control de acceso) |


Revisión de la dupla
Comentarios:

Revisé los requisitos funcionales y en general se entienden bien. pero hay dos observaciones: 

* En RF-009 me parece correcta la tolerancia de 15 minutos, así lo manejamos.
* En RF-012 agregaría que el sistema debería poder deshacer una marca de cobro si la recepcionista se equivoca de paciente.
* El detalle de CU-01 con el flujo alterno de "horario ocupado" se siente muy real.
* Me gustaría que el de "Reagendar citas de un médico ausente" (CU-06) aclarara qué pasa si ningún horario alterno funciona para el paciente, porque a veces simplemente no hay dónde moverlo ese día.

Cambios solicitados y aplicados:

Ninguno que cambie el alcance. la observación de RF-012 queda como mejora para una siguiente versión, no bloquea la entrega.

