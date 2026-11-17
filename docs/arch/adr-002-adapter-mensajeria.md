# ADR-002 · Aislar el proveedor de SMS/WhatsApp detrás de un Adapter

- **Estado:** Aceptada
- **Fecha:** 2026-10-21
- **Autor:** Juan Pablo Quiroz Ortega

## Contexto

RF-008 pide recordatorios y solicitudes de confirmación por SMS o WhatsApp (24 h y 2 h antes), porque en la entrevista se confirmó que los pacientes no revisan notificaciones push pero sí WhatsApp. RF-009 y RF-007 dependen de esos mensajes: exigir confirmación previa tras inasistencias y avisar al primero en lista de espera.

MediSync no controla la API del proveedor (WhatsApp Business API o un gateway de SMS como Twilio). Tiene su propio formato, límites de envío, plantillas aprobadas y su propia forma de avisar que el paciente respondió (webhook).

## Decisión

El dominio solo conoce una interfaz propia, `CanalMensajeria`, con la operación `enviarMensaje(telefono, texto)`. La clase `AdapterWhatsApp` implementa esa interfaz y es la **única** que sabe hablar con la API externa. `Notificacion.enviar()` usa la interfaz, nunca al proveedor directamente.

## Consecuencias

### Positivas

- Cambiar de proveedor (o agregar SMS además de WhatsApp) es escribir otro Adapter; no se toca `Cita`, `Agenda`, `ListaDeEspera` ni las reglas de RF-007 y RF-009.
- En pruebas se puede usar un Adapter falso que no manda mensajes reales.

### Negativas

- Una clase y una interfaz más que mantener, para hoy un solo proveedor.
- La interfaz común solo cubre lo que todos los proveedores saben hacer: si WhatsApp ofrece algo propio (botones de respuesta rápida, por ejemplo), aprovecharlo obliga a ensanchar la interfaz o a romper la abstracción.
- MediSync sigue dependiendo de un tercero: si el proveedor se cae, los recordatorios no salen aunque el Adapter esté bien. El Adapter aísla el código, no la disponibilidad.
