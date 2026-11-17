# AI-001 · Análisis de impacto de SC-001: anular una marca de cobro

La solicitud completa está en [`solicitud-de-cambios.md`](solicitud-de-cambios.md).

## La solicitud

En la revisión de requisitos, la dupla pidió:

> "En RF-012 agregaría que el sistema debería poder deshacer una marca de cobro si la recepcionista se equivoca de paciente."

**Tipo de cambio:** mantenimiento perfectivo (no corrige un defecto del sistema, agrega una capacidad que hace falta).

Hoy, cuando la recepcionista marca como cobrada la cita equivocada, no hay forma de corregirlo. Con dos recepcionistas por turno y la sala llena, el error es realista.

## Requisito nuevo propuesto

**RF-017.** La recepcionista debe poder anular una marca de cobro indicando el motivo. La anulación no borra el cobro original: lo deja marcado como anulado, con quién lo anuló y cuándo.

*Criterio de aceptación:* la cita regresa a estatus `ATENDIDA`, el cobro anulado sigue visible en el historial con su motivo, y la cita se puede volver a cobrar.

## Qué habría que tocar

| Pieza | ¿Cambia? | Qué cambia |
|---|---|---|
| Especificación de requisitos | Sí | Agregar RF-017 y su fila en la tabla de trazabilidad. |
| Diagrama de casos de uso | Sí | Nuevo CU-09 "Anular cobro" como «extend» de CU-05 Marcar cobro. |
| Diagrama de clases | Sí | `Recepcionista +anularCobro(Cita, string) void`; `Cobro` gana `-anulado: bool`, `-motivoAnulacion: string`, `-anuladoPor: string` y `+anular(string) void`. La composición `Cita "1" *-- "0..1" Cobro` pasa a `"0..*"`, porque una cita puede tener un cobro anulado y otro válido. |
| Diagrama de estados | Sí | Nueva transición `Cobrada → Atendida : anularCobro() [con motivo]`. |
| Diagrama de secuencia (CU-01) | No | Agendar una cita no toca el cobro. |
| C4 contexto y contenedores | No | Todo vive en el módulo Recepción y cobro de la misma API; no hay contenedor ni sistema externo nuevo. |
| ADR-001 y ADR-002 | No | No cambia ninguna decisión de arquitectura. |
| Código | Sí | Solo el módulo Recepción y cobro de la API, una migración en la tabla de cobros y un botón en el panel del consultorio. |

## Estimación de esfuerzo

| Tarea | Horas |
|---|---|
| RF-017, criterio de aceptación y trazabilidad | 0.5 |
| CU-09 en el diagrama de casos de uso | 0.5 |
| Diagrama de clases (Cobro, Recepcionista, multiplicidad) | 0.5 |
| Diagrama de estados (transición nueva) | 0.25 |
| API: operación `anularCobro` en Recepción y cobro + migración de base de datos | 3 |
| Panel del consultorio: botón "Anular cobro" con campo de motivo | 1.5 |
| Pruebas: cobrar, anular, volver a cobrar, historial visible | 2 |
| **Total** | **≈ 8 horas (rango 6 a 12)** |

**De dónde sale el número:** de partir el cambio en las piezas de la tabla de arriba y estimar cada una por separado. La referencia para el código es CU-05 Marcar cobro, que es la operación espejo de esta y tiene el mismo tamaño. El rango es amplio porque la parte de código todavía no está construida.

## Opciones

- **Opción A · Anular el cobro dentro de MediSync** (la que se analiza arriba): ≈ 8 horas, solo el módulo Recepción y cobro.
- **Opción B · Integrar MediSync con la terminal de pago** para que el cobro y su reversa vengan de la terminal. Descartada: rompe el alcance (RF-012 dice que no se integra con la terminal), agrega un sistema externo nuevo al diagrama de contexto y un segundo Adapter.

## Decisión

**Sí aceptaría hoy la Opción A**, con una condición.

- Está contenido en un solo módulo (Recepción y cobro), no toca la arquitectura ni el caso de uso crítico, que es agendar.
- Corrige un error que la dupla dijo que sí pasa, no uno hipotético.
- Es barato: alrededor de un día de trabajo de una persona.

**La condición:** que sea *anular* y no *borrar*. Es dinero; si un cobro se puede borrar sin rastro, el cambio abre un hueco peor que el error que corrige. Por eso RF-017 exige motivo, quién y cuándo.

**Costo escondido que hay que aceptar:** este cambio revierte una decisión de la versión 2 del diagrama de clases, donde le quité el estatus a `Cobro` porque "si existe, ya se cobró". Con la anulación eso deja de ser cierto.
