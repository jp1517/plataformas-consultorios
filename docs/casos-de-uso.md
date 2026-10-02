# Casos de uso · MediSync

## Diagrama

Ver `docs/diagramas/casos-de-uso.drawio` (editable) y `docs/diagramas/casos-de-uso.png` (exportado).

**Actores:** Paciente, Recepcionista, Médico.

| Caso de uso | Actor(es) | Requisitos que realiza |
|---|---|---|
| CU-01 Agendar una cita | Paciente | RF-003, RF-004, RF-008, RF-010 |
| CU-02 Reprogramar una cita | Paciente | RF-003, RF-004, RF-005 |
| CU-03 Cancelar una cita | Paciente | RF-006, RF-007, RF-009 |
| CU-04 Registrar llegada a sala de espera | Recepcionista | RF-002, RF-011 |
| CU-05 Marcar cobro de consulta | Recepcionista | RF-011, RF-012 |
| CU-06 Reagendar las citas de un médico ausente | Recepcionista | RF-003, RF-004, RF-015 |
| CU-07 Consultar el historial clínico | Médico | RF-013 |
| CU-08 Registrar la consulta de un paciente | Médico | RF-013, RF-014 |

---

## CU-01 · Agendar una cita

| Campo | Detalle |
|---|---|
| **Actor principal** | Paciente |
| **Objetivo** | Reservar un espacio de atención con un médico en una fecha y hora determinadas. |
| **Precondición** | El paciente ya está registrado en el sistema. |
| **Escenario principal** | 1. El paciente abre la vista de autoservicio y elige médico. <br>2. El sistema muestra los horarios disponibles de ese médico. <br>3. El paciente elige fecha y hora. <br>4. El sistema verifica que ese espacio esté libre y que el paciente no tenga ya otra cita activa con ese médico ese día. <br>5. El sistema registra la cita y la muestra en la agenda del médico y en el panel de recepción. <br>6. El sistema programa los recordatorios automáticos previos a la cita. |
| **Flujos alternos** | 3a. El horario elegido ya está ocupado: el sistema muestra los horarios libres más cercanos del mismo médico. <br>4a. El paciente ya tiene una cita activa con ese médico ese día: el sistema rechaza el intento y explica el motivo. <br>4b. Es una urgencia: el sistema permite registrarla sin horario fijo y la marca como urgencia para que recepción la acomode. |
| **Postcondición** | La cita queda registrada y visible en la agenda del médico y en el panel de recepción. |
| **Requisitos que realiza** | RF-003, RF-004, RF-008, RF-010 |

---

## CU-02 · Reprogramar una cita

| Campo | Detalle |
|---|---|
| **Actor principal** | Paciente |
| **Objetivo** | Mover una cita ya agendada a un nuevo horario disponible, sin perder su lugar. |
| **Precondición** | El paciente tiene al menos una cita agendada y activa (no cancelada, no pasada). |
| **Escenario principal** | 1. El paciente abre "Mis citas" y elige la cita que quiere mover. <br>2. El sistema muestra los horarios disponibles del mismo médico. <br>3. El paciente elige un nuevo horario. <br>4. El sistema verifica que el nuevo horario esté libre y que no genere una segunda cita activa con el mismo médico el mismo día. <br>5. El sistema libera el horario anterior y reserva el nuevo en la misma operación. <br>6. El sistema actualiza los recordatorios programados para la nueva fecha y hora. |
| **Flujos alternos** | 3a. El nuevo horario elegido ya está ocupado: el sistema muestra los horarios libres más cercanos del mismo médico. <br>4a. El paciente intenta reprogramar a una hora donde ya tiene otra cita activa con ese médico: el sistema rechaza el cambio y explica el motivo. <br>5a. El paciente cancela la operación antes de confirmar: el sistema conserva la cita original sin cambios. |
| **Postcondición** | La cita queda con el nuevo horario, y el horario anterior aparece disponible para otros pacientes. |
| **Requisitos que realiza** | RF-003, RF-004, RF-005 |

---

## CU-03 · Cancelar una cita

| Campo | Detalle |
|---|---|
| **Actor principal** | Paciente |
| **Objetivo** | Liberar un horario que ya no va a usar, para no acumular inasistencias ni bloquear el espacio a otros pacientes. |
| **Precondición** | El paciente tiene al menos una cita agendada y activa. |
| **Escenario principal** | 1. El paciente abre "Mis citas" y elige la cita que quiere cancelar. <br>2. El sistema muestra una confirmación con el aviso de qué pasará con el horario liberado. <br>3. El paciente confirma la cancelación. <br>4. El sistema cambia el estatus de la cita a "cancelada" con fecha y hora de la cancelación. <br>5. El sistema libera el horario y lo ofrece al primer paciente en lista de espera para ese médico y fecha. |
| **Flujos alternos** | 3a. El paciente cancela con menos de 2 horas de anticipación: el sistema igual acepta la cancelación, pero no libera el horario a la lista de espera, por el poco tiempo restante. <br>2a. El paciente se arrepiente en la pantalla de confirmación: el sistema regresa a "Mis citas" sin cambios. |
| **Postcondición** | La cita queda marcada como cancelada, y si hubo tiempo suficiente, el horario queda disponible u ofrecido a otro paciente. |
| **Requisitos que realiza** | RF-006, RF-007, RF-009 |

---

## CU-04 · Registrar llegada a sala de espera

| Campo | Detalle |
|---|---|
| **Actor principal** | Recepcionista |
| **Objetivo** | Dejar constancia de que el paciente ya llegó al consultorio y está esperando su turno. |
| **Precondición** | Existe una cita agendada para ese paciente en el día actual. |
| **Escenario principal** | 1. La recepcionista busca al paciente por nombre en el panel del día. <br>2. El sistema muestra la cita correspondiente con su estatus actual. <br>3. La recepcionista marca "Registrar llegada". <br>4. El sistema cambia el estatus de la cita a "en sala de espera" y lo refleja en el panel del día. |
| **Flujos alternos** | 1a. El paciente no tiene cita agendada para hoy (llegó sin avisar): la recepcionista no puede registrar su llegada por este flujo y debe agendarlo primero como urgencia. <br>3a. El paciente llega antes de su horario: el sistema permite registrar la llegada igual, y el estatus "en sala de espera" queda visible aunque falte tiempo para la hora agendada. |
| **Postcondición** | La cita queda visible como "en sala de espera" en el panel de recepción. |
| **Requisitos que realiza** | RF-002, RF-011 |

---

## CU-05 · Marcar cobro de consulta

| Campo | Detalle |
|---|---|
| **Actor principal** | Recepcionista |
| **Objetivo** | Registrar que una consulta ya fue cobrada, para llevar el control del día. |
| **Precondición** | La cita existe y su estatus es "en sala de espera" o posterior (la consulta ya ocurrió o está por ocurrir). |
| **Escenario principal** | 1. La recepcionista selecciona la cita del paciente en el panel del día. <br>2. La recepcionista marca "Marcar cobro". <br>3. El sistema cambia el estatus de cobro de esa cita a "cobrada" y la distingue visualmente del resto. |
| **Flujos alternos** | 2a. La recepcionista marca el cobro por error en la cita equivocada: el sistema permite deshacer la marca de cobro desde la misma vista mientras siga siendo el mismo día. |
| **Postcondición** | La cita queda identificada como cobrada en el panel del día. |
| **Requisitos que realiza** | RF-011, RF-012 |

---

## CU-06 · Reagendar las citas de un médico ausente

| Campo | Detalle |
|---|---|
| **Actor principal** | Recepcionista |
| **Objetivo** | Mover en bloque todas las citas del día de un médico que no se va a presentar, para no perder esos pacientes. |
| **Precondición** | El médico tiene al menos una cita agendada para el día en que se reporta su ausencia. |
| **Escenario principal** | 1. La recepcionista marca al médico como ausente para el día actual. <br>2. El sistema lista todas las citas de ese médico agendadas para ese día. <br>3. La recepcionista elige, cita por cita, reasignarla a un nuevo horario disponible (del mismo médico en otra fecha, o de otro médico) o marcarla como pendiente de contacto telefónico. <br>4. El sistema actualiza el estatus de cada cita según lo elegido. |
| **Flujos alternos** | 3a. No hay horarios disponibles cercanos para reasignar: la recepcionista marca la cita como pendiente de contacto telefónico. <br>3b. El paciente de una cita ya había sido marcado como "en sala de espera" antes de saberse la ausencia: el sistema lo señala como caso prioritario dentro de la lista. |
| **Postcondición** | Todas las citas del médico ausente quedan reasignadas o marcadas como pendientes, ninguna se pierde sin registro. |
| **Requisitos que realiza** | RF-003, RF-004, RF-015 |

---

## CU-07 · Consultar el historial clínico

| Campo | Detalle |
|---|---|
| **Actor principal** | Médico |
| **Objetivo** | Revisar los antecedentes del paciente antes o durante la consulta para dar un diagnóstico informado. |
| **Precondición** | El paciente tiene una cita agendada con ese médico y el expediente ya tiene al menos un registro previo (o es su primera vez, en cuyo caso el historial aparece vacío). |
| **Escenario principal** | 1. El médico abre su agenda del día y selecciona al paciente de la cita en turno. <br>2. El sistema muestra el historial clínico completo: diagnósticos previos, notas de evolución y recetas anteriores, ordenados por fecha. |
| **Flujos alternos** | 2a. Es la primera consulta del paciente: el sistema muestra el historial vacío con un aviso de "sin consultas previas" en vez de una lista. |
| **Postcondición** | El médico queda con el contexto clínico del paciente antes de iniciar la consulta actual. |
| **Requisitos que realiza** | RF-013 |

---

## CU-08 · Registrar la consulta de un paciente

| Campo | Detalle |
|---|---|
| **Actor principal** | Médico |
| **Objetivo** | Dejar constancia de lo ocurrido en la consulta actual, para que quede disponible en futuras visitas. |
| **Precondición** | El médico ya consultó (o decidió omitir) el historial clínico del paciente para esta cita. |
| **Escenario principal** | 1. El médico abre el formulario de consulta desde la cita en turno. <br>2. El médico captura notas de evolución, diagnóstico y receta. <br>3. El médico guarda la consulta. <br>4. El sistema almacena lo capturado en el expediente del paciente, visible en consultas futuras. |
| **Flujos alternos** | 3a. El médico intenta guardar sin capturar diagnóstico: el sistema no permite guardar y señala el campo faltante. <br>3b. El médico necesita interrumpir la captura a medio llenar: el sistema conserva un borrador y permite retomarlo antes de cerrar la cita. |
| **Postcondición** | La consulta queda registrada en el expediente del paciente, consultable en citas futuras. |
| **Requisitos que realiza** | RF-013, RF-014 |
