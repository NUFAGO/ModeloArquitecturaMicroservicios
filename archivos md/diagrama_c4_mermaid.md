# Diagramas C4 - Versión Mermaid
Sistema de Gestión de Licitaciones y Presupuestos

## Nivel 1: Diagrama de Contexto (Simplificado)

```mermaid
graph TB
    subgraph Usuarios
        U1[Gestor de Licitaciones]
        U2[Ingeniero de Costos]
        U3[Auditor]
        U4[Director de Proyecto]
    end
    
    subgraph Sistema Principal
        SYS[Sistema de Gestión de<br/>Licitaciones y Presupuestos]
    end
    
    subgraph Sistemas Externos
        ERP[Sistema ERP]
        DOCS[Sistema de Documentos]
    end
    
    U1 -->|Gestiona licitaciones<br/>y presupuestos| SYS
    U2 -->|Administra partidas<br/>y recursos| SYS
    U3 -->|Consulta logs| SYS
    U4 -->|Consulta obras| SYS
    
    SYS -->|Exporta datos<br/>financieros| ERP
    SYS -->|Almacena<br/>documentación| DOCS
    
    style SYS fill:#1168bd,color:#fff
    style ERP fill:#999,color:#fff
    style DOCS fill:#999,color:#fff
```

## Nivel 2: Diagrama de Contenedores

```mermaid
graph TB
    USER[Usuario del Sistema]
    
    subgraph Sistema["Sistema de Gestión de Licitaciones y Presupuestos"]
        WEB[Aplicación Web<br/>React.js]
        API[API REST<br/>Node.js, Express]
        AUTH[Servicio de<br/>Autenticación<br/>JWT]
        DB[(Base de Datos<br/>MongoDB/PostgreSQL)]
        CACHE[(Cache<br/>Redis)]
        NOTIF[Servicio de<br/>Notificaciones]
    end
    
    ERP[Sistema ERP]
    EMAIL[Servicio de Email]
    
    USER -->|HTTPS| WEB
    WEB -->|JSON/HTTPS| API
    WEB -->|JWT/HTTPS| AUTH
    
    API -->|SQL/NoSQL| DB
    API -->|Redis Protocol| CACHE
    API -->|Message Queue| NOTIF
    AUTH -->|SQL/NoSQL| DB
    
    NOTIF -->|SMTP| EMAIL
    API -->|REST API| ERP
    
    style WEB fill:#1168bd,color:#fff
    style API fill:#1168bd,color:#fff
    style AUTH fill:#1168bd,color:#fff
    style DB fill:#1168bd,color:#fff
    style CACHE fill:#1168bd,color:#fff
    style NOTIF fill:#1168bd,color:#fff
```

## Nivel 3: Arquitectura de Componentes (Backend)

```mermaid
graph TB
    subgraph Frontend
        WEB[Aplicación Web]
    end
    
    subgraph "API Backend - Capa de Controladores"
        LICT_CTRL[Controlador<br/>Licitaciones]
        PRES_CTRL[Controlador<br/>Presupuestos]
        PART_CTRL[Controlador<br/>Partidas]
        REC_CTRL[Controlador<br/>Recursos]
        EST_CTRL[Controlador<br/>Estructura]
        AUD_CTRL[Controlador<br/>Auditoría]
    end
    
    subgraph "Capa de Servicios de Negocio"
        LICT_SVC[Servicio<br/>Licitaciones]
        PRES_SVC[Servicio<br/>Presupuestos]
        PART_SVC[Servicio<br/>Partidas]
        REC_SVC[Servicio<br/>Recursos]
        VER_SVC[Servicio<br/>Versionamiento]
        AUD_SVC[Servicio<br/>Auditoría]
    end
    
    subgraph "Capa de Acceso a Datos"
        LICT_REPO[Repositorio<br/>Licitaciones]
        PRES_REPO[Repositorio<br/>Presupuestos]
        PART_REPO[Repositorio<br/>Partidas]
        REC_REPO[Repositorio<br/>Recursos]
        LOG_REPO[Repositorio<br/>Logs]
    end
    
    DB[(Base de Datos)]
    
    WEB --> LICT_CTRL
    WEB --> PRES_CTRL
    WEB --> PART_CTRL
    WEB --> REC_CTRL
    WEB --> EST_CTRL
    WEB --> AUD_CTRL
    
    LICT_CTRL --> LICT_SVC
    PRES_CTRL --> PRES_SVC
    PART_CTRL --> PART_SVC
    REC_CTRL --> REC_SVC
    EST_CTRL --> PRES_SVC
    AUD_CTRL --> AUD_SVC
    
    PRES_SVC --> LICT_SVC
    PART_SVC --> PRES_SVC
    REC_SVC --> VER_SVC
    REC_SVC --> AUD_SVC
    PRES_SVC --> AUD_SVC
    
    LICT_SVC --> LICT_REPO
    PRES_SVC --> PRES_REPO
    PART_SVC --> PART_REPO
    REC_SVC --> REC_REPO
    VER_SVC --> REC_REPO
    AUD_SVC --> LOG_REPO
    
    LICT_REPO --> DB
    PRES_REPO --> DB
    PART_REPO --> DB
    REC_REPO --> DB
    LOG_REPO --> DB
    
    style LICT_SVC fill:#63b3ed,color:#000
    style PRES_SVC fill:#63b3ed,color:#000
    style PART_SVC fill:#63b3ed,color:#000
    style REC_SVC fill:#63b3ed,color:#000
    style VER_SVC fill:#63b3ed,color:#000
    style AUD_SVC fill:#63b3ed,color:#000
```

## Modelo de Dominio - Entidades Principales

```mermaid
graph LR
    subgraph "Gestión de Licitaciones"
        LIC[LICITACION<br/>- id<br/>- titulo<br/>- estado<br/>- fecha]
    end
    
    subgraph "Gestión de Presupuestos"
        PRES[PRESUPUESTO<br/>- id<br/>- titulo<br/>- licitacion_id<br/>- fecha]
        OBRA[OBRA<br/>- id<br/>- titulo<br/>- presupuesto_id]
    end
    
    subgraph "Estructura Presupuestaria (Plantillas)"
        TIT[TITULO_PRESUPUESTO<br/>- id<br/>- titulo<br/>- descripcion]
        SUB[SUBTITULO_PRESUPUESTO<br/>- id<br/>- subtitulo<br/>- titulo_id<br/>- descripcion]
        SUBSUB[SUB_SUBTITULO_PRESUPUESTO<br/>- id<br/>- sub_subtitulo<br/>- subtitulo_id]
    end
    
    subgraph "Partidas y Recursos"
        PART[PARTIDA<br/>- id<br/>- titulo<br/>- presupuesto_id]
        REC[RECURSOS<br/>- id<br/>- codigo]
        PART_REC[PARTIDA_RECURSO<br/>- id<br/>- recurso_id<br/>- partida_id<br/>- costo<br/>- costo_meta]
        VER[PARTIDA_RECURSO_VERSION<br/>- id<br/>- partida_recurso_id<br/>- costo<br/>- fecha]
    end
    
    subgraph "Auditoría"
        LOG[LOG_PRESUPUESTO<br/>- id<br/>- recurso_id<br/>- usuario_id<br/>- motivo<br/>- fecha]
    end
    
    LIC -->|1:N| PRES
    PRES -->|1:N| OBRA
    PRES -->|1:N| PART
    
    TIT -->|1:N| SUB
    SUB -->|1:N| SUBSUB
    
    PART -->|N:M| TIT
    PART -->|N:M| SUB
    PART -->|N:M| SUBSUB
    
    PART -->|1:N| PART_REC
    REC -->|1:N| PART_REC
    PART_REC -->|1:N| VER
    
    PRES -.->|audita| LOG
    
    style LIC fill:#fbbf24,color:#000
    style PRES fill:#60a5fa,color:#000
    style TIT fill:#a78bfa,color:#000
    style SUB fill:#a78bfa,color:#000
    style SUBSUB fill:#a78bfa,color:#000
    style PART fill:#34d399,color:#000
    style REC fill:#f87171,color:#000
    style LOG fill:#94a3b8,color:#000
```

## Flujo de Datos Principal

```mermaid
sequenceDiagram
    participant U as Usuario
    participant W as Web App
    participant API as API Backend
    participant PS as Servicio Presupuestos
    participant PAS as Servicio Partidas
    participant RS as Servicio Recursos
    participant AS as Servicio Auditoría
    participant DB as Base de Datos
    
    U->>W: Crear Presupuesto
    W->>API: POST /presupuestos
    API->>PS: crearPresupuesto()
    PS->>DB: Insertar PRESUPUESTO
    PS->>DB: Asociar estructura (títulos, subtítulos)
    PS->>AS: Registrar log de creación
    AS->>DB: Insertar LOG_PRESUPUESTO
    DB-->>PS: Confirmación
    PS-->>API: Presupuesto creado
    API-->>W: 201 Created
    W-->>U: Presupuesto creado exitosamente
    
    U->>W: Agregar Partida con Recursos
    W->>API: POST /partidas
    API->>PAS: crearPartida()
    PAS->>DB: Insertar PARTIDA
    PAS->>DB: Asociar con estructura
    PAS->>RS: agregarRecursos()
    RS->>DB: Insertar PARTIDA_RECURSO
    RS->>DB: Crear versión inicial
    RS->>AS: Registrar cambios
    AS->>DB: Insertar LOG
    DB-->>API: Confirmación
    API-->>W: 201 Created
    W-->>U: Partida agregada
```

