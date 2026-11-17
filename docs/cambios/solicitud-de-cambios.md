# SC-001: Anular una marca de cobro

## Estado

Propuesta · analizada en [AI-001](analisis-de-impacto-sc-001.md)

## Quién la pide

Dupla de revisión, en sus comentarios a la especificación de requisitos.

## Qué se pide

> "En RF-012 agregaría que el sistema debería poder deshacer una marca de
> cobro si la recepcionista se equivoca de paciente."

Que la recepcionista pueda corregir un cobro que marcó en la cita
equivocada, sin tener que pedirle a nadie que lo arregle por fuera del
sistema.

## Por qué

Según la entrevista, el puesto de recepción lo cubren dos personas en
turnos y en las horas pico la sala está llena. Marcar como cobrada la cita
de otro paciente es un error realista. Hoy RF-012 permite marcar el cobro,
pero no dice qué pasa si se marcó mal, así que el panel del día queda con
un dato falso y el cobro real del otro paciente no se puede registrar.

## Qué pasa si no se hace

El panel de recepción deja de ser confiable para el cierre del día: hay
citas que aparecen como cobradas sin haberlo sido, y la recepcionista
vuelve a apuntar las correcciones en papel, que es justo lo que MediSync
busca eliminar.

## Tipo de mantenimiento

- [ ] Correctivo
- [ ] Adaptativo
- [x] **Perfectivo** · el sistema funciona como se especificó, pero le falta
  una capacidad que su usuario necesita.
- [ ] Preventivo

## Criterio de aceptación

- La recepcionista puede anular la marca de cobro de una cita indicando el
  motivo.
- La cita regresa a estatus `ATENDIDA` y se puede volver a cobrar.
- El cobro anulado no se borra: sigue visible en el historial con el
  motivo, quién lo anuló y cuándo.
- El médico y el paciente no ven ni pueden usar esta opción.

## Prioridad que sugiere el solicitante

Media. No bloquea la entrega, pero sin ella el panel de cobros del día no
se puede corregir.
