# Arquitectura de MediSync (modelo C4)

Este documento describe la arquitectura de MediSync en tres niveles del modelo C4: contexto, contenedores y componentes. Las decisiones que explican por qué quedó así están en los registros de decisión de la carpeta `docs/arch/` (`arch/adr-001-monolito-modular.md` y `arch/adr-002-adapter-mensajeria.md`).

## Nivel 1 · Diagrama de contexto

MediSync es un solo sistema que usan tres tipos de persona y que habla con un único sistema externo: el proveedor de mensajería (SMS/WhatsApp) para recordatorios y confirmaciones (RF-008).

La terminal de pago aparece en el diagrama a propósito, pero **sin flecha hacia MediSync**: RF-012 deja claro que el sistema no la reemplaza ni se integra con ella, solo refleja que la recepcionista ya cobró ahí.

```mermaid
flowchart LR
    paciente["<b>Paciente</b><br/>[Persona]<br/>Agenda, confirma, reprograma<br/>o cancela su cita desde el teléfono"]
    medico["<b>Médico</b><br/>[Persona]<br/>Ve su agenda y el expediente<br/>clínico durante la consulta"]
    recepcion["<b>Recepcionista</b><br/>[Persona]<br/>Dos en turnos. Sala de espera,<br/>confirmaciones y cobro del día"]

    medisync["<b>MediSync</b><br/>[Sistema de software]<br/>Agenda citas sin empalmes, envía<br/>recordatorios y guarda el expediente"]

    mensajeria["<b>Proveedor de mensajería</b><br/>[Sistema externo]<br/>API de WhatsApp Business / SMS.<br/>Le entrega el mensaje al paciente"]
    terminal["<b>Terminal de pago física</b><br/>[Sistema externo]<br/>Fuera del alcance, sin integración"]

    paciente -- "Agenda y gestiona sus citas<br/>[HTTPS]" --> medisync
    medico -- "Consulta y registra el expediente<br/>[HTTPS]" --> medisync
    recepcion -- "Registra llegadas y marca cobros<br/>[HTTPS]" --> medisync
    medisync -- "Envía recordatorios y recibe<br/>confirmaciones [HTTPS/REST]" --> mensajeria
    recepcion -. "Cobra con<br/>(fuera de MediSync)" .-> terminal

    classDef person fill:#08427B,color:#fff,stroke:#052E56
    classDef system fill:#1168BD,color:#fff,stroke:#0B4884
    classDef ext fill:#999999,color:#fff,stroke:#6B6B6B
    class paciente,recepcion,medico person
    class medisync system
    class mensajeria,terminal ext
```

## Nivel 2 · Diagrama de contenedores

MediSync es un **monolito modular** (ver ADR-001): una sola API desplegable, dividida por dentro en los cuatro módulos que salieron del análisis (Identidad y acceso, Agenda y citas, Clínico, Recepción y cobro), más dos aplicaciones web y una base de datos.

```mermaid
flowchart LR
    paciente["<b>Paciente</b><br/>[Persona]"]
    recepcion["<b>Recepcionista</b><br/>[Persona]"]
    medico["<b>Médico</b><br/>[Persona]"]

    subgraph medisync["MediSync [Sistema de software]"]
        direction LR
        appPaciente["<b>App de autoservicio</b><br/>[Contenedor: PWA, React]<br/>Agendar, reprogramar, cancelar.<br/>Disponible 24 h (RNF-DISP-001)"]
        panel["<b>Panel del consultorio</b><br/>[Contenedor: Web, React]<br/>Vista operativa (recepción)<br/>y vista clínica (médico)"]
        api["<b>API MediSync</b><br/>[Contenedor: Node.js + NestJS]<br/>Monolito modular: Identidad y acceso,<br/>Agenda y citas, Clínico, Recepción y cobro.<br/>Incluye el programador de recordatorios"]
        db[("<b>Base de datos</b><br/>[Contenedor: PostgreSQL]<br/>Pacientes, citas, agenda,<br/>expedientes, cobros")]
    end

    mensajeria["<b>Proveedor de mensajería</b><br/>[Sistema externo]<br/>WhatsApp Business / SMS"]

    paciente -- "Usa<br/>[HTTPS]" --> appPaciente
    recepcion -- "Usa<br/>[HTTPS]" --> panel
    medico -- "Usa<br/>[HTTPS]" --> panel
    appPaciente -- "Llama a<br/>[HTTPS / JSON]" --> api
    panel -- "Llama a<br/>[HTTPS / JSON]" --> api
    api -- "Lee y escribe<br/>[SQL / TCP]" --> db
    api -- "Envía mensajes vía Adapter<br/>[HTTPS / REST]" --> mensajeria
    mensajeria -- "Confirmación del paciente<br/>[Webhook HTTPS]" --> api

    classDef person fill:#08427B,color:#fff,stroke:#052E56
    classDef container fill:#438DD5,color:#fff,stroke:#2E6295
    classDef ext fill:#999999,color:#fff,stroke:#6B6B6B
    class paciente,recepcion,medico person
    class appPaciente,panel,api,db container
    class mensajeria ext
```

## Nivel 3 · Diagrama de componentes de la API

Por dentro, la API son los cuatro módulos del monolito modular (ADR-001) más el Adapter de mensajería (ADR-002) y el programador que dispara recordatorios y revisa la tolerancia de 15 minutos. Las flechas son llamadas a función dentro del mismo proceso, no viajes por red.

```mermaid
flowchart LR
    apps["<b>App de autoservicio y<br/>Panel del consultorio</b><br/>[Contenedores web]"]

    subgraph api["API MediSync [Contenedor: Node.js + NestJS]"]
        direction LR
        ident["<b>Identidad y acceso</b><br/>[Componente]<br/>Usuario, Paciente, Médico,<br/>Recepcionista. Vistas por rol"]
        agenda["<b>Agenda y citas</b><br/>[Componente]<br/>Cita, Agenda, ListaDeEspera,<br/>Notificacion. Valida empalmes"]
        clinico["<b>Clínico</b><br/>[Componente]<br/>ExpedienteClinico.<br/>Solo rol médico"]
        cobro["<b>Recepción y cobro</b><br/>[Componente]<br/>Llegadas y Cobro"]
        sched["<b>Programador</b><br/>[Componente: cron]<br/>Recordatorios 24 h y 2 h,<br/>tolerancia de 15 min"]
        adapter["<b>AdapterWhatsApp</b><br/>[Componente: patrón Adapter]<br/>Implementa CanalMensajeria"]
    end

    db[("<b>Base de datos</b><br/>[PostgreSQL]")]
    ext["<b>Proveedor de mensajería</b><br/>[Sistema externo]"]

    apps -- "HTTPS / JSON" --> ident
    ident -- "autoriza por rol" --> agenda
    ident -- "autoriza solo médico" --> clinico
    ident -- "autoriza recepción" --> cobro
    cobro -- "cambia estatus de la cita" --> agenda
    sched -- "dispara recordatorios" --> agenda
    agenda -- "enviarMensaje()" --> adapter
    adapter -- "HTTPS / REST" --> ext
    agenda -- "SQL" --> db
    clinico -- "SQL" --> db
    cobro -- "SQL" --> db
    ident -- "SQL" --> db

    classDef comp fill:#85BBF0,color:#000,stroke:#5D82A8
    classDef cont fill:#438DD5,color:#fff,stroke:#2E6295
    classDef ext fill:#999999,color:#fff,stroke:#6B6B6B
    class ident,agenda,clinico,cobro,sched,adapter comp
    class apps,db cont
    class ext ext
```

## Cómo concuerda con el resto del modelo

- Los cuatro módulos de la API son los mismos que agrupan las clases del diagrama de clases (`docs/uml-medisync.md`).
- La flecha "Envía mensajes vía Adapter" es la interfaz `CanalMensajeria` y su implementación `AdapterWhatsApp` del diagrama de clases, y aparece como mensaje `enviarMensaje()` en el diagrama de secuencia (`docs/uml-medisync.md`).
- El webhook de confirmación es lo que dispara la transición `Agendada → Confirmada` en el diagrama de estados de la cita (`docs/uml-medisync.md`).
