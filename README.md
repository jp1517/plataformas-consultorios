# MediSync

**Autor:** Juan Pablo Quiroz Ortega  
**Nombre del sistema:** MediSync  
**Materia:** Ingeniería de Software I (SIS3407) · Universidad Anáhuac Querétaro

## Descripción

MediSync es un asistente digital para consultorios médicos privados. Permite que los pacientes agenden, confirmen, reprogramen y cancelen sus citas desde el teléfono a cualquier hora, mientras organiza el día del médico y del personal de recepción: valida que no se empalmen los horarios, manda recordatorios por SMS o WhatsApp, reasigna los huecos que deja una cancelación a quien está en lista de espera y guarda el expediente clínico, visible solo para el médico.

## Documentación

Documentos

  * [`Visión del producto`](docs/vision-del-producto.md)
  * [`Guion de la entrevista`](docs/guion-entrevista.md)
  * [`Especificación de requisitos`](docs/especificacion-requisitos.md)
  * [`Casos de uso`](docs/casos-de-uso.md)

Arquitectura

  * [`Arquitectura C4 (contexto, contenedores y componentes)`](docs/arquitectura-c4.md)
  * [`ADR-001 Monolito modular`](docs/arch/adr-001-monolito-modular.md)
  * [`ADR-002 Adapter para SMS/WhatsApp`](docs/arch/adr-002-adapter-mensajeria.md)

Modelo UML

  * [`Clases, secuencia (CU-01) y estados de la cita`](docs/uml-medisync.md)

Cambios

  * [`SC-001 Solicitud de cambio: anular una marca de cobro`](docs/cambios/solicitud-de-cambios.md)
  * [`AI-001 Análisis de impacto de SC-001`](docs/cambios/analisis-de-impacto-sc-001.md)

Diagramas

  * [`Casos de uso (Mermaid)`](docs/diagramas/casos-de-uso.md)
  * [`Casos de uso.drawio`](docs/diagramas/casos-de-uso-2.drawio)
  * [`Casos de uso.png`](docs/diagramas/casos-de-uso.drawio.png)

Prácticas de clase

  * La carpeta [`diagramas/`](diagramas/) tiene los ejercicios de Mermaid hechos en clase (flujo, secuencia, clases, estados, ER, C4). No son parte del diseño de MediSync.
