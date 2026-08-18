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

*Instrucción: lo que escribes en "fuera del alcance" es lo que después evita que el proyecto crezca sin control. Sé específico: "reportes" no dice nada, "reportes de ventas mensuales exportables a PDF" sí.*

### Dentro del alcance

-
-
-
-

### Explícitamente fuera del alcance

-
-
-

**Por qué queda fuera:**

*Instrucción: para al menos una de las exclusiones, explica la razón. Puede ser tiempo, complejidad, o que no aporta al problema central.*

---

## 4. Tipo de sistema y restricciones

*Instrucción: identifica de qué tipo es tu sistema y qué te obliga a garantizar ese tipo. Un sistema de información y un sistema crítico no se diseñan igual.*

**Tipo de sistema:**

*(De información · Embebido · Crítico · Web y SaaS · De datos y análisis)*

**Por qué es de ese tipo:**

**Atributos de calidad que impone:**

| Atributo | Por qué importa en mi caso | Qué pasa si no se cumple |
|---|---|---|
| | | |
| | | |
| | | |

**Reglas de negocio que ya identifiqué:**

*Instrucción: reglas que no son obvias desde fuera y que alguien que conoce el dominio tendría que explicarte. Si no encuentras ninguna, tu caso puede ser demasiado simple.*

1.
2.
3.

---

## 5. Ciclo de vida elegido

*Instrucción: este apartado se trabaja en la semana 3, después de ver los modelos de desarrollo. La justificación pesa más que la elección: no hay un modelo correcto, hay uno defendible para tu caso.*

**Modelo elegido:**

**Por qué le conviene a este proyecto:**

*Instrucción: argumenta con las características reales de tu caso. Estabilidad de los requisitos, disponibilidad del cliente, nivel de riesgo, tamaño del equipo, frecuencia de entregas esperada.*

### Alternativas descartadas

**Alternativa 1:**

*Por qué la descarté:*

**Alternativa 2:**

*Por qué la descarté:*

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
