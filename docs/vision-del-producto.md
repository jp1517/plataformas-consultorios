# Visión del producto

---

**Autor: Juan Pablo Quiroz Ortega**
**Fecha de la última versión**
**Repositorio:**

---

## 1. Descripción del sistema

**Nombre del sistema: MediSync**

**Descripción: Es un asistente digital para consultorios médicos que ayuda a los pacientes a agendar y confirmar sus consultas desde el teléfono a cualquier hora, mientras organiza el día del doctor y del personal de recepción para que nadie espere de más, no se empalmen los horarios y no se pierdan las notas de cada visita.**

---

## 2. Problema y usuarios


**El problema: Los consultorios médicos privados pierden mucho dinero y tiempo por pacientes que faltan a sus citas sin avisar o que llegan a deshoras, provocando salas de espera saturadas, citas empalmadas y desorganización general. Esto le sirve tanto al médico y al personal de recepción (que reducen el estrés administrativo y optimizan su jornada) como a los pacientes (que evitan largas esperas y procesos tediosos para conseguir un turno).**

**Cómo se resuelve hoy sin el sistema: La recepcionista anota todo en una libreta de papel o en una hoja de cálculo básica. Para intentar que los pacientes asistan, pasa horas del día enviando mensajes uno por uno o haciendo llamadas directas para reconfirmar. Si un paciente cancela de último momento o simplemente no llega, ese espacio de tiempo se pierde por completo porque no hay forma ágil de reasignarlo a alguien más en lista de espera; además, el médico tiene que buscar manualmente entre carpetas físicas de papel para revisar el historial antes de hacer pasar a la persona.**

**Usuarios del sistema:**

| Tipo de usuario | Qué necesita del sistema | Qué le preocupa |
|---|---|---|
|Médico |Consultar el historial clínico de forma inmediata y ver su agenda del día ordenada en tiempo real sin empalmes. |Que el sistema sea lento durante la consulta, le haga perder tiempo llenando campos innecesarios o exponga datos médicos confidenciales.|
| Paciente|Agendar, consultar, confirmar o reprogramar sus citas desde su teléfono de manera rápida y sin tener que llamar. |Que el proceso sea confuso, que sus citas no queden realmente registradas o que le cobren penalizaciones por fallas en la plataforma. |
|Recepcionista/Secretaria |Un panel central para registrar llegadas a sala de espera, monitorear confirmaciones de asistencia y gestionar cobros del día. |Que el sistema permita citas dobles en el mismo horario, sea difícil de usar cuando hay mucha gente esperando o se caiga la conexión. |


**Un conflicto entre usuarios:**

Lo que quiere el Médico:
El médico necesita ver en una sola pantalla todo el detalle clínico del paciente (diagnósticos anteriores, notas de evolución, recetas previas y antecedentes familiares) para tomar decisiones médicas rápidas y certeras durante los pocos minutos que dura la consulta.

Por qué le estorba a la Recepcionista y al Paciente:
A la recepcionista toda esa información clínica le estorba visualmente, ya que satura la pantalla con datos técnicos que ella no necesita y solo busca validar rápidamente la hora de la cita, marcar la llegada y cobrar. Además, al paciente le resultaría confuso e invasivo ver terminología médica compleja en su flujo de reserva o consulta de turnos.

La decisión de diseño:

Implementar una arquitectura de vistas segmentada por roles:
Vista Operativa (Secretaria): Interfaz ágil y minimalista centrada únicamente en el flujo de la sala de espera, estatus de confirmación y cobro, ocultando por completo las notas médicas.
Vista Clínica (Médico): Espacio de trabajo enfocado exclusivamente en el expediente clínico interactivo y la cronología médica del paciente.
Vista Autoservicio (Paciente): Portal simplificado enfocado únicamente en la selección de horarios, recordatorios y gestión de su propia cita.

---

## 3. Alcance


### Dentro del alcance

- Registro y autenticación de tres roles (paciente, médico, recepcionista) con vistas y permisos diferenciados.
- Agendamiento, reprogramación y cancelación de citas por parte del paciente desde su teléfono, con horarios disponibles en tiempo real.
- Recordatorios y confirmaciones automáticas de citas (notificación push o mensaje) antes de la consulta.
- Panel de recepción para registrar la llegada del paciente a sala de espera, ver el estatus de confirmación del día y marcar el cobro de cada consulta.
- Agenda del médico en tiempo real, con validación automática para que no se empalmen dos citas en el mismo horario.

### Explícitamente fuera del alcance

- Consultas por videollamada.
- Administración de múltiples sucursales o cadenas de consultorios desde una sola cuenta.
- Recetas electrónicas con firma digital certificada ante autoridades sanitarias.

**Por qué queda fuera:**

**Videollamada:** no ataca el problema (que es de citas presenciales), solo suma trabajo.
**Multi-sucursal:** tu diseño es para un consultorio, no una cadena — meterlo complica el sistema sin necesidad.
**Recetas con firma digital certificada:** es un tema legal/regulatorio (COFEPRIS, firma avanzada), no de software — fuera del alcance de un proyecto de este tamaño.

---

## 4. Tipo de sistema y restricciones


**Tipo de sistema:** De información


**Por qué es de ese tipo:** Es sistema de información porque su función principal es guardar, mostrar y aplicar reglas sobre datos (citas, pacientes, expedientes). No controla hardware ni pone vidas en riesgo. Su naturaleza real es gestionar información.

**Atributos de calidad que impone:**

| Atributo | Por qué importa en mi caso | Qué pasa si no se cumple |
|---|---|---|
| Confidencialidad / Seguridad| El expediente clínico contiene datos médicos sensibles que solo el médico debe poder ver; la recepción y otros pacientes nunca deben tener acceso a ellos.| Se filtran datos médicos de un paciente, lo que rompe la confianza en el consultorio y puede tener consecuencias legales para el médico.|
| Disponibilidad| Los pacientes deben poder agendar citas a cualquier hora del día, y recepción depende del sistema durante todo el horario de atención.| Si el sistema cae en horario de consultas, la recepcionista vuelve al papel y se pierden citas o pagos; si cae de noche, el paciente no puede agendar.|
| Consistencia / Integridad de los datos| La agenda del médico no debe permitir dos citas en el mismo horario, y una cita cancelada debe liberar el espacio de forma correcta.| Dos pacientes llegan a la misma hora o un horario cancelado nunca se reasigna, repitiendo exactamente el problema que el sistema busca resolver.|

**Reglas de negocio que ya identifiqué:**

*Instrucción: reglas que no son obvias desde fuera y que alguien que conoce el dominio tendría que explicarte. Si no encuentras ninguna, tu caso puede ser demasiado simple.*

1. Si un paciente falta a una cita sin cancelarla con al menos cierto tiempo de anticipación (por ejemplo, 2 horas), el sistema debe registrarlo como inasistencia y notificar a recepción, quien decide si requiere confirmación telefónica antes de dejarlo agendar de nuevo.
2. Cuando un paciente cancela con suficiente anticipación, su horario se libera automáticamente y se ofrece al primer paciente en la lista de espera para ese médico y esa fecha.
3. Un paciente no puede tener dos citas activas con el mismo médico el mismo día; el sistema debe rechazar el intento de duplicado.

---

## 5. Ciclo de vida elegido


**Modelo elegido:** Incremental

**Por qué le conviene a este proyecto:** Se va a construir MediSync por partes. Se hace esto porque el médico, la recepcionista y el paciente quieren cosas distintas del sistema, así que seguramente se tendrán que ajustar pantallas y reglas sobre la marcha. No es un proyecto crítico, ir mostrando avances y corrigiendo lo que no funcione.

*Instrucción: argumenta con las características reales de tu caso. Estabilidad de los requisitos, disponibilidad del cliente, nivel de riesgo, tamaño del equipo, frecuencia de entregas esperada.*

### Alternativas descartadas

**Alternativa 1:** Cascada

*Por qué la descarté:*  No la usé porque este modelo necesita que tengas TODOS los requisitos claros y fijos desde el principio, antes de empezar a programar. En mi caso ya sé que eso no va a pasar, porque los tres usuarios quieren cosas diferentes y voy a tener que ir ajustando. Además con cascada no se ve nada funcionando hasta el final, y eso es arriesgado si tengo poco tiempo.

**Alternativa 2:** Espiral

*Por qué la descarté:* No la usé porque este modelo es para proyectos grandes y riesgosos, donde en cada vuelta tienes que hacer un análisis formal de riesgos. Para un proyecto escolar de este tamaño es demasiado trámite y proceso — no le suma nada, solo lo hace más lento.

---

## Antes de entregar

Reviso que el documento cumpla lo siguiente:

- [ ] La descripción del apartado 1 se entiende sin ser del área
- [ ] Hay al menos dos tipos de usuario con necesidades distintas
- [ ] Identifiqué un conflicto real entre usuarios
- [ ] El alcance dice qué queda fuera, no solo qué queda dentro
- [ ] Las exclusiones son específicas, no genéricas
- [ ] Identifiqué el tipo de sistema y al menos dos atributos de calidad
- [ ] Anoté al menos tres reglas de negocio no obvias
- [ ] Justifiqué el ciclo de vida contra dos alternativas descartadas
- [ ] El documento está en mi repositorio y se puede leer desde el navegador
- [ ] Borré todas las instrucciones en cursiva de la plantilla
