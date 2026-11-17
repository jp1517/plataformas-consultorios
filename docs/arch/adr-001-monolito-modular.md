# ADR-001 · Construir MediSync como monolito modular

- **Estado:** Aceptada
- **Fecha:** 2026-10-21
- **Autor:** Juan Pablo Quiroz Ortega

## Contexto

MediSync atiende a un solo consultorio privado (la administración de varias sucursales quedó fuera del alcance). Lo construye una dupla: dos personas, sin experiencia operando servicios distribuidos en producción.

Del análisis de sustantivos salieron cuatro grupos de clases que cambian juntas y que coinciden con cómo la especificación ya agrupa los requisitos:

| Módulo | Clases | Requisitos |
|---|---|---|
| Identidad y acceso | Usuario, Paciente, Médico, Recepcionista | RF-001 |
| Agenda y citas | Cita, Agenda, ListaDeEspera, Notificacion | RF-003 a RF-010, RF-015, RF-016 |
| Clínico | ExpedienteClinico | RF-013, RF-014, RNF-SEG-001 |
| Recepción y cobro | Cobro | RF-002, RF-011, RF-012 |

Hay dos requisitos de disponibilidad distintos: el autoservicio del paciente debe estar arriba las 24 horas (RNF-DISP-001, 99 % mensual) y el panel de recepción solo en horario de atención (RNF-DISP-002, 99.5 % de 8:00 a 20:00). Además, agendar y liberar horarios tiene que ser consistente: nunca dos citas en el mismo horario del mismo médico (RNF-CONS-001) y un horario cancelado debe verse libre en menos de 5 segundos (RNF-CONS-002).

## Decisión

MediSync se construye como **un monolito modular**: una sola API en Node.js + NestJS, desplegada como una unidad, con una sola base de datos PostgreSQL. Por dentro, la API se divide en los cuatro módulos de la tabla. Cada módulo expone una interfaz pública y los demás solo pueden llamarlo a través de ella, nunca tocando sus tablas directamente.

## Alternativas consideradas

- **Monolito sin módulos.** Más rápido de arrancar, pero las fronteras que ya están claras en la especificación se perderían en el código, y el día que haya que separar algo no habría por dónde cortar.
- **Microservicios (un servicio por módulo).** Descartado. Con dos personas, cada servicio se queda sin dueño; agendar una cita pasaría de ser una llamada a función a viajar por red entre Identidad, Agenda y Notificaciones; y la validación de empalmes (RNF-CONS-001) dejaría de ser una transacción para volverse consistencia eventual, que es justo el problema que el sistema existe para resolver.

## Consecuencias

### Positivas

- Agendar una cita (validar empalme, reservar horario, crear la cita) cabe en **una sola transacción de base de datos**, así que RNF-CONS-001 se cumple sin coordinación entre servicios.
- Un solo despliegue y un solo servidor que operar, algo que una dupla sí puede sostener.
- Las fronteras por módulo dejan listo el corte para el día que haga falta separar uno.

### Negativas

- **Un solo punto de falla.** Si la API se cae, se caen a la vez el autoservicio del paciente y el panel de recepción. No se puede dar al autoservicio una disponibilidad mayor que a recepción, aunque RNF-DISP-001 la pida más alta.
- **Escala todo junto.** Un pico de agendamientos nocturnos consume los mismos recursos que usa recepción de día; no se puede escalar solo Agenda y citas.
- **Las fronteras dependen de disciplina.** Nada técnico impide que alguien importe directo las tablas de otro módulo; si eso pasa, el monolito modular se degrada a monolito simple sin que nadie lo note.
- **Despliegue acoplado.** Un cambio pequeño en Recepción y cobro obliga a desplegar también Agenda y citas.

### Señal para revisar esta decisión

Separar **Agenda y citas** como primer servicio el día que un pico de autoservicio vuelva lento el panel de recepción en horario de atención, o cuando ese módulo necesite desplegarse mucho más seguido que los demás.
