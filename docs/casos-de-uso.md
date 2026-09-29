# Casos de uso · MediSync

## Diagrama

Ver `docs/diagramas/casos-de-uso.drawio` (editable) y exportar `casos-de-uso.png` desde draw.io antes de subir al repositorio, siguiendo la estructura pedida:

```
docs/
├── especificacion-requisitos.md
└── diagramas/
    ├── casos-de-uso.drawio   ← editable
    └── casos-de-uso.png      ← se ve en GitHub
```

**Actores:** Paciente, Recepcionista, Médico.

**Casos de uso (8):**

| Caso de uso | Actor(es) | Requisitos que realiza |
|---|---|---|
| Agendar una cita | Paciente | RF-003, RF-004, RF-008, RF-010 |
| Reprogramar una cita | Paciente | RF-003, RF-004, RF-005 |
| Cancelar una cita | Paciente | RF-006, RF-007, RF-009 |
| Registrar llegada a sala de espera | Recepcionista | RF-002, RF-011 |
| Marcar cobro de consulta | Recepcionista | RF-011, RF-012 |
| Reagendar las citas de un médico ausente | Recepcionista | RF-003, RF-004, RF-015 |
| Consultar el historial clínico | Médico | RF-013 |
| Registrar la consulta de un paciente | Médico | RF-013, RF-014 |

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
