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

| Caso de uso | Actor(es) |
|---|---|
| Agendar una cita | Paciente |
| Reprogramar una cita | Paciente |
| Cancelar una cita | Paciente |
| Registrar llegada a sala de espera | Recepcionista |
| Marcar cobro de consulta | Recepcionista |
| Reagendar las citas de un médico ausente | Recepcionista |
| Consultar el historial clínico | Médico |
| Registrar la consulta de un paciente | Médico |

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
