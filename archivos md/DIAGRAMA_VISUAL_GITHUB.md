# 📊 Diagramas C4 Visuales - Sistema de Gestión de Licitaciones y Presupuestos

> **✅ Este archivo se visualiza correctamente en GitHub**  
> Los diagramas Mermaid se renderizan automáticamente al abrir este archivo en GitHub.

---

## 📍 Nivel 1: Diagrama de Contexto

**¿Qué hace el sistema y quién lo usa?**

```mermaid
graph TB
    subgraph "👥 USUARIOS"
        U1[👤 Gestor de Licitaciones<br/>Administra licitaciones<br/>y crea presupuestos]
        U2[👤 Ingeniero de Costos<br/>Gestiona partidas<br/>recursos y versiones]
        U3[👤 Auditor<br/>Supervisa cambios<br/>y revisa logs]
        U4[👤 Director de Proyecto<br/>Consulta obras<br/>y presupuestos]
    end
    
    subgraph "💼 SISTEMA PRINCIPAL"
        SYS[🏗️ Sistema de Gestión de<br/>Licitaciones y Presupuestos<br/><br/>Gestiona el ciclo completo de licitaciones,<br/>presupuestos, partidas y recursos<br/>para proyectos de construcción]
    end
    
    subgraph "🔗 SISTEMAS EXTERNOS"
        ERP[💰 Sistema ERP<br/>Contabilidad y Finanzas]
        DOCS[📁 Sistema de Documentos<br/>Almacenamiento técnico]
    end
    
    U1 -->|Gestiona licitaciones<br/>y presupuestos<br/>HTTPS| SYS
    U2 -->|Administra partidas<br/>y recursos<br/>HTTPS| SYS
    U3 -->|Consulta logs<br/>HTTPS| SYS
    U4 -->|Consulta obras<br/>HTTPS| SYS
    
    SYS -->|Exporta datos<br/>financieros<br/>API REST| ERP
    SYS -->|Almacena<br/>documentación<br/>API REST| DOCS
    
    style SYS fill:#1168bd,stroke:#0d47a1,stroke-width:3px,color:#fff
    style U1 fill:#4caf50,stroke:#2e7d32,stroke-width:2px,color:#fff
    style U2 fill:#4caf50,stroke:#2e7d32,stroke-width:2px,color:#fff
    style U3 fill:#4caf50,stroke:#2e7d32,stroke-width:2px,color:#fff
    style U4 fill:#4caf50,stroke:#2e7d32,stroke-width:2px,color:#fff
    style ERP fill:#757575,stroke:#424242,stroke-width:2px,color:#fff
    style DOCS fill:#757575,stroke:#424242,stroke-width:2px,color:#fff
```

### 📝 Descripción Nivel 1
- **Sistema Central**: Gestiona licitaciones, presupuestos, partidas y recursos
- **4 Tipos de Usuarios**: Cada uno con roles y permisos específicos
- **Integración Externa**: Conecta con ERP para finanzas y sistema documental

---

## 🏗️ Nivel 2: Diagrama de Contenedores

**¿Cuáles son las piezas principales del sistema?**

```mermaid
graph TB
    USER[👤 Usuario del Sistema<br/>Gestores, Ingenieros, Auditores]
    
    subgraph "💼 SISTEMA DE GESTIÓN DE LICITACIONES Y PRESUPUESTOS"
        WEB[🌐 Aplicación Web<br/>React.js<br/><br/>Interfaz de usuario responsiva<br/>para gestión de licitaciones,<br/>presupuestos y recursos]
        
        API[⚙️ API REST<br/>Node.js + Express<br/><br/>Proporciona funcionalidad<br/>de negocio mediante<br/>API REST]
        
        AUTH[🔐 Servicio de Autenticación<br/>Node.js + JWT<br/><br/>Gestiona autenticación<br/>y autorización<br/>de usuarios]
        
        DB[(💾 Base de Datos Principal<br/>MongoDB / PostgreSQL<br/><br/>Almacena licitaciones,<br/>presupuestos, partidas,<br/>recursos y relaciones)]
        
        CACHE[(⚡ Cache<br/>Redis<br/><br/>Cache de consultas<br/>frecuentes y sesiones)]
        
        NOTIF[📧 Servicio de Notificaciones<br/>Node.js<br/><br/>Envía notificaciones<br/>sobre cambios en<br/>presupuestos]
    end
    
    ERP[💰 Sistema ERP<br/>Sistema empresarial]
    EMAIL[📬 Servicio de Email<br/>Notificaciones por correo]
    
    USER -->|Usa<br/>HTTPS| WEB
    WEB -->|Realiza llamadas API<br/>JSON/HTTPS| API
    WEB -->|Autentica<br/>JWT/HTTPS| AUTH
    
    API -->|Lee y escribe datos<br/>SQL/NoSQL| DB
    API -->|Lee/Escribe cache<br/>Redis Protocol| CACHE
    API -->|Envía eventos<br/>Message Queue| NOTIF
    AUTH -->|Valida credenciales<br/>SQL/NoSQL| DB
    
    NOTIF -->|Envía emails<br/>SMTP| EMAIL
    API -->|Exporta datos<br/>REST API| ERP
    
    style WEB fill:#1976d2,stroke:#0d47a1,stroke-width:2px,color:#fff
    style API fill:#1976d2,stroke:#0d47a1,stroke-width:2px,color:#fff
    style AUTH fill:#1976d2,stroke:#0d47a1,stroke-width:2px,color:#fff
    style DB fill:#1976d2,stroke:#0d47a1,stroke-width:2px,color:#fff
    style CACHE fill:#1976d2,stroke:#0d47a1,stroke-width:2px,color:#fff
    style NOTIF fill:#1976d2,stroke:#0d47a1,stroke-width:2px,color:#fff
    style USER fill:#4caf50,stroke:#2e7d32,stroke-width:2px,color:#fff
    style ERP fill:#757575,stroke:#424242,stroke-width:2px,color:#fff
    style EMAIL fill:#757575,stroke:#424242,stroke-width:2px,color:#fff
```

### 📝 Descripción Nivel 2
- **Frontend**: Aplicación web en React.js
- **Backend**: API REST con Node.js/Express
- **Autenticación**: JWT para seguridad
- **Persistencia**: Base de datos + Cache Redis
- **Notificaciones**: Sistema asíncrono de alertas

---

## ⚙️ Nivel 3: Diagrama de Componentes (Backend API)

**¿Cómo está organizado internamente el backend?**

```mermaid
graph TB
    WEB[🌐 Aplicación Web]
    
    subgraph "🎮 CAPA DE CONTROLADORES"
        LICT_CTRL[📋 Controlador<br/>Licitaciones]
        PRES_CTRL[💰 Controlador<br/>Presupuestos]
        PART_CTRL[📊 Controlador<br/>Partidas]
        REC_CTRL[🔧 Controlador<br/>Recursos]
        EST_CTRL[🏗️ Controlador<br/>Estructura]
        AUD_CTRL[📜 Controlador<br/>Auditoría]
    end
    
    subgraph "💼 CAPA DE SERVICIOS DE NEGOCIO"
        LICT_SVC[Servicio<br/>Licitaciones]
        PRES_SVC[Servicio<br/>Presupuestos]
        PART_SVC[Servicio<br/>Partidas]
        REC_SVC[Servicio<br/>Recursos]
        VER_SVC[Servicio<br/>Versionamiento]
        AUD_SVC[Servicio<br/>Auditoría]
    end
    
    subgraph "💾 CAPA DE ACCESO A DATOS"
        LICT_REPO[Repositorio<br/>Licitaciones]
        PRES_REPO[Repositorio<br/>Presupuestos]
        PART_REPO[Repositorio<br/>Partidas]
        REC_REPO[Repositorio<br/>Recursos]
        LOG_REPO[Repositorio<br/>Logs]
    end
    
    DB[(💾 Base de Datos<br/>PostgreSQL/MongoDB)]
    
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
    
    PRES_SVC -.->|Valida licitación| LICT_SVC
    PART_SVC -.->|Valida presupuesto| PRES_SVC
    REC_SVC -.->|Gestiona versiones| VER_SVC
    REC_SVC -.->|Registra cambios| AUD_SVC
    PRES_SVC -.->|Registra cambios| AUD_SVC
    
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
    
    style LICT_SVC fill:#42a5f5,stroke:#1976d2,stroke-width:2px,color:#000
    style PRES_SVC fill:#42a5f5,stroke:#1976d2,stroke-width:2px,color:#000
    style PART_SVC fill:#42a5f5,stroke:#1976d2,stroke-width:2px,color:#000
    style REC_SVC fill:#42a5f5,stroke:#1976d2,stroke-width:2px,color:#000
    style VER_SVC fill:#42a5f5,stroke:#1976d2,stroke-width:2px,color:#000
    style AUD_SVC fill:#42a5f5,stroke:#1976d2,stroke-width:2px,color:#000
```

### 📝 Descripción Nivel 3
**Arquitectura en 3 Capas:**
1. **Controladores**: Manejan peticiones HTTP
2. **Servicios**: Contienen lógica de negocio
3. **Repositorios**: Acceso a base de datos

**Patrones Implementados:**
- ✅ Controller Pattern
- ✅ Service Layer Pattern  
- ✅ Repository Pattern
- ✅ Dependency Injection

---

## 🗃️ Modelo de Dominio - Entidades Principales

**Relaciones entre entidades del sistema**

```mermaid
graph LR
    subgraph "📋 LICITACIONES"
        LIC[LICITACION<br/>━━━━━━━<br/>• id<br/>• titulo<br/>• estado<br/>• fecha]
    end
    
    subgraph "💰 PRESUPUESTOS"
        PRES[PRESUPUESTO<br/>━━━━━━━<br/>• id<br/>• titulo<br/>• licitacion_id<br/>• fecha]
        OBRA[OBRA<br/>━━━━━━━<br/>• id<br/>• titulo<br/>• presupuesto_id]
    end
    
    subgraph "🏗️ ESTRUCTURA (Plantillas Reutilizables)"
        TIT[TITULO_PRESUPUESTO<br/>━━━━━━━<br/>• id<br/>• titulo<br/>• descripcion]
        SUB[SUBTITULO_PRESUPUESTO<br/>━━━━━━━<br/>• id<br/>• subtitulo<br/>• titulo_id<br/>• descripcion]
        SUBSUB[SUB_SUBTITULO<br/>━━━━━━━<br/>• id<br/>• sub_subtitulo<br/>• subtitulo_id]
    end
    
    subgraph "📊 PARTIDAS Y RECURSOS"
        PART[PARTIDA<br/>━━━━━━━<br/>• id<br/>• titulo<br/>• presupuesto_id]
        REC[RECURSOS<br/>━━━━━━━<br/>• id<br/>• codigo]
        PART_REC[PARTIDA_RECURSO<br/>━━━━━━━<br/>• id<br/>• recurso_id<br/>• partida_id<br/>• costo<br/>• costo_meta]
        VER[PARTIDA_RECURSO_VERSION<br/>━━━━━━━<br/>• id<br/>• partida_recurso_id<br/>• costo<br/>• fecha]
    end
    
    subgraph "📜 AUDITORÍA"
        LOG[LOG_PRESUPUESTO<br/>━━━━━━━<br/>• id<br/>• recurso_id<br/>• usuario_id<br/>• motivo<br/>• fecha]
    end
    
    LIC -->|1:N| PRES
    PRES -->|1:N| OBRA
    PRES -->|1:N| PART
    
    TIT -->|1:N| SUB
    SUB -->|1:N| SUBSUB
    
    PART -.->|N:M<br/>usa plantilla| TIT
    PART -.->|N:M<br/>usa plantilla| SUB
    PART -.->|N:M<br/>usa plantilla| SUBSUB
    
    PART -->|1:N| PART_REC
    REC -->|1:N| PART_REC
    PART_REC -->|1:N<br/>versiona| VER
    
    PRES -.->|audita| LOG
    
    style LIC fill:#fbbf24,stroke:#f59e0b,stroke-width:2px,color:#000
    style PRES fill:#60a5fa,stroke:#3b82f6,stroke-width:2px,color:#000
    style TIT fill:#a78bfa,stroke:#8b5cf6,stroke-width:2px,color:#000
    style SUB fill:#a78bfa,stroke:#8b5cf6,stroke-width:2px,color:#000
    style SUBSUB fill:#a78bfa,stroke:#8b5cf6,stroke-width:2px,color:#000
    style PART fill:#34d399,stroke:#10b981,stroke-width:2px,color:#000
    style REC fill:#f87171,stroke:#ef4444,stroke-width:2px,color:#000
    style LOG fill:#94a3b8,stroke:#64748b,stroke-width:2px,color:#fff
```

### 📝 Descripción del Modelo
**Entidades Clave:**
- 🟡 **Licitaciones**: Punto de partida del proceso
- 🔵 **Presupuestos**: Asociados a licitaciones, contienen partidas
- 🟣 **Estructura**: Plantillas reutilizables (títulos, subtítulos)
- 🟢 **Partidas**: Componentes del presupuesto con estructura
- 🔴 **Recursos**: Materiales/servicios con versionamiento de costos
- ⚫ **Logs**: Auditoría completa de cambios

---

## 🔄 Flujo de Datos Principal

**Secuencia típica de creación de presupuesto con partidas**

```mermaid
sequenceDiagram
    participant U as 👤 Usuario
    participant W as 🌐 Web App
    participant API as ⚙️ API Backend
    participant PS as 💼 Servicio Presupuestos
    participant PAS as 💼 Servicio Partidas
    participant RS as 💼 Servicio Recursos
    participant AS as 📜 Servicio Auditoría
    participant DB as 💾 Base de Datos
    
    rect rgb(200, 230, 255)
        Note over U,DB: 1️⃣ CREACIÓN DE PRESUPUESTO
        U->>W: Crear Presupuesto
        W->>API: POST /presupuestos
        API->>PS: crearPresupuesto()
        PS->>DB: Insertar PRESUPUESTO
        PS->>DB: Asociar estructura (títulos, subtítulos)
        PS->>AS: Registrar log de creación
        AS->>DB: Insertar LOG_PRESUPUESTO
        DB-->>PS: ✅ Confirmación
        PS-->>API: Presupuesto creado
        API-->>W: 201 Created
        W-->>U: ✅ Presupuesto creado exitosamente
    end
    
    rect rgb(200, 255, 200)
        Note over U,DB: 2️⃣ CREACIÓN DE PARTIDA CON RECURSOS
        U->>W: Agregar Partida con Recursos
        W->>API: POST /partidas
        API->>PAS: crearPartida()
        PAS->>DB: Insertar PARTIDA
        PAS->>DB: Asociar con estructura
        PAS->>RS: agregarRecursos()
        RS->>DB: Insertar PARTIDA_RECURSO
        RS->>DB: Crear versión inicial (v1)
        RS->>AS: Registrar cambios
        AS->>DB: Insertar LOG
        DB-->>API: ✅ Confirmación
        API-->>W: 201 Created
        W-->>U: ✅ Partida agregada
    end
    
    rect rgb(255, 230, 200)
        Note over U,DB: 3️⃣ ACTUALIZACIÓN DE COSTO
        U->>W: Actualizar costo de recurso
        W->>API: PUT /partidas/{id}/recursos/{id}/costo
        API->>RS: actualizarCosto()
        RS->>DB: Actualizar PARTIDA_RECURSO.costo
        RS->>DB: Crear nueva versión (v2)
        RS->>AS: Registrar cambio con motivo
        AS->>DB: Insertar LOG
        DB-->>API: ✅ Confirmación
        API-->>W: 200 OK
        W-->>U: ✅ Costo actualizado (v2 creada)
    end
```

### 📝 Descripción del Flujo
1. **Fase 1**: Usuario crea presupuesto y asocia estructura
2. **Fase 2**: Se agregan partidas con recursos iniciales (versión 1)
3. **Fase 3**: Actualizaciones de costos generan nuevas versiones
4. **Auditoría**: Cada operación se registra en LOG_PRESUPUESTO

---

## 🎯 Conceptos Arquitectónicos Clave

### 1. 🔄 Sistema de Plantillas (Template Pattern)

```mermaid
graph TD
    subgraph "📚 PLANTILLAS (Se crean una vez)"
        T1[Título: Obra Civil]
        T2[Título: Instalaciones]
        S1[Subtítulo: Movimiento de Tierras]
        S2[Subtítulo: Cimentación]
    end
    
    subgraph "📋 PRESUPUESTO A"
        PA[Presupuesto<br/>Proyecto Alpha]
    end
    
    subgraph "📋 PRESUPUESTO B"
        PB[Presupuesto<br/>Proyecto Beta]
    end
    
    T1 -.->|reutiliza| PA
    S1 -.->|reutiliza| PA
    T1 -.->|reutiliza| PB
    T2 -.->|reutiliza| PB
    S2 -.->|reutiliza| PB
    
    style T1 fill:#a78bfa,stroke:#8b5cf6,stroke-width:2px
    style T2 fill:#a78bfa,stroke:#8b5cf6,stroke-width:2px
    style S1 fill:#c4b5fd,stroke:#a78bfa,stroke-width:2px
    style S2 fill:#c4b5fd,stroke:#a78bfa,stroke-width:2px
    style PA fill:#60a5fa,stroke:#3b82f6,stroke-width:2px
    style PB fill:#60a5fa,stroke:#3b82f6,stroke-width:2px
```

**Ventajas:**
- ✅ No duplicación de datos
- ✅ Consistencia en toda la organización
- ✅ Fácil actualización centralizada
- ✅ Reutilización máxima

### 2. 📦 Sistema de Versionamiento

```mermaid
graph LR
    subgraph "🔧 RECURSO: Cemento Portland"
        PR[PARTIDA_RECURSO<br/>━━━━━━━<br/>costo actual: $145.00<br/>costo meta: $140.00]
    end
    
    subgraph "📜 HISTORIAL DE VERSIONES"
        V1[Versión 1<br/>━━━━━━━<br/>$150.00<br/>01/01/2024]
        V2[Versión 2<br/>━━━━━━━<br/>$148.00<br/>15/02/2024]
        V3[Versión 3<br/>━━━━━━━<br/>$145.00<br/>20/03/2024<br/>ACTUAL]
    end
    
    PR -->|historial| V1
    PR -->|historial| V2
    PR -->|historial| V3
    
    style PR fill:#f87171,stroke:#ef4444,stroke-width:3px,color:#000
    style V1 fill:#e0e0e0,stroke:#9e9e9e,stroke-width:2px
    style V2 fill:#e0e0e0,stroke:#9e9e9e,stroke-width:2px
    style V3 fill:#4caf50,stroke:#2e7d32,stroke-width:3px,color:#fff
```

**Ventajas:**
- ✅ Trazabilidad completa
- ✅ Auditoría de cambios
- ✅ Análisis de tendencias
- ✅ Rollback si es necesario

### 3. 📋 Auditoría Integral

```mermaid
graph TB
    subgraph "🔄 OPERACIONES DEL SISTEMA"
        OP1[Crear Presupuesto]
        OP2[Modificar Partida]
        OP3[Actualizar Costo]
        OP4[Eliminar Recurso]
    end
    
    subgraph "📜 REGISTRO DE AUDITORÍA"
        LOG[LOG_PRESUPUESTO<br/>━━━━━━━<br/>👤 usuario_id<br/>🔧 recurso_id<br/>📝 motivo<br/>📅 fecha<br/>🔍 detalles]
    end
    
    OP1 -->|registra| LOG
    OP2 -->|registra| LOG
    OP3 -->|registra| LOG
    OP4 -->|registra| LOG
    
    style LOG fill:#94a3b8,stroke:#64748b,stroke-width:3px,color:#fff
    style OP1 fill:#42a5f5,stroke:#1976d2,stroke-width:2px
    style OP2 fill:#42a5f5,stroke:#1976d2,stroke-width:2px
    style OP3 fill:#42a5f5,stroke:#1976d2,stroke-width:2px
    style OP4 fill:#42a5f5,stroke:#1976d2,stroke-width:2px
```

**Información Registrada:**
- 👤 **Quién**: Usuario que realizó la acción
- 🔧 **Qué**: Recurso/entidad afectada
- 📝 **Por qué**: Motivo del cambio
- 📅 **Cuándo**: Timestamp exacto
- 🔍 **Detalles**: Información adicional

---

## 📊 Estadísticas del Sistema

```mermaid
graph LR
    subgraph "📈 MÉTRICAS CLAVE"
        M1[Entidades<br/>Principales<br/>━━━━━<br/>16 tablas]
        M2[Relaciones<br/>N:M<br/>━━━━━<br/>9 tablas<br/>intermedias]
        M3[Patrones de<br/>Diseño<br/>━━━━━<br/>5 patrones<br/>implementados]
        M4[Capas de<br/>Arquitectura<br/>━━━━━<br/>3 capas<br/>definidas]
    end
    
    style M1 fill:#fbbf24,stroke:#f59e0b,stroke-width:2px,color:#000
    style M2 fill:#60a5fa,stroke:#3b82f6,stroke-width:2px,color:#000
    style M3 fill:#34d399,stroke:#10b981,stroke-width:2px,color:#000
    style M4 fill:#a78bfa,stroke:#8b5cf6,stroke-width:2px,color:#000
```

---

## 🎨 Vista de Módulos del Sistema

```mermaid
graph TB
    subgraph "🏗️ SISTEMA DE GESTIÓN"
        M1[📋 Módulo<br/>Licitaciones<br/>━━━━━<br/>LICITACION]
        
        M2[💰 Módulo<br/>Presupuestos<br/>━━━━━<br/>PRESUPUESTO<br/>OBRA<br/>Estructura]
        
        M3[📊 Módulo<br/>Partidas<br/>━━━━━<br/>PARTIDA<br/>Relaciones<br/>con Estructura]
        
        M4[🔧 Módulo<br/>Recursos<br/>━━━━━<br/>RECURSOS<br/>PARTIDA_RECURSO<br/>Versionamiento]
        
        M5[📜 Módulo<br/>Auditoría<br/>━━━━━<br/>LOG_PRESUPUESTO<br/>Trazabilidad]
    end
    
    M1 -->|1:N| M2
    M2 -->|1:N| M3
    M3 -->|N:M| M4
    M2 -.->|audita| M5
    M3 -.->|audita| M5
    M4 -.->|audita| M5
    
    style M1 fill:#fbbf24,stroke:#f59e0b,stroke-width:2px,color:#000
    style M2 fill:#60a5fa,stroke:#3b82f6,stroke-width:2px,color:#000
    style M3 fill:#34d399,stroke:#10b981,stroke-width:2px,color:#000
    style M4 fill:#f87171,stroke:#ef4444,stroke-width:2px,color:#000
    style M5 fill:#94a3b8,stroke:#64748b,stroke-width:2px,color:#fff
```

---

## ✅ Checklist de Implementación

```mermaid
graph LR
    subgraph "🚀 FASES DE IMPLEMENTACIÓN"
        F1[✅ 1. Arquitectura<br/>Definida]
        F2[⬜ 2. Stack Tech<br/>Seleccionado]
        F3[⬜ 3. MVP<br/>Desarrollo]
        F4[⬜ 4. Testing<br/>QA]
        F5[⬜ 5. Deploy<br/>Producción]
    end
    
    F1 --> F2
    F2 --> F3
    F3 --> F4
    F4 --> F5
    
    style F1 fill:#4caf50,stroke:#2e7d32,stroke-width:3px,color:#fff
    style F2 fill:#e0e0e0,stroke:#9e9e9e,stroke-width:2px
    style F3 fill:#e0e0e0,stroke:#9e9e9e,stroke-width:2px
    style F4 fill:#e0e0e0,stroke:#9e9e9e,stroke-width:2px
    style F5 fill:#e0e0e0,stroke:#9e9e9e,stroke-width:2px
```

---

## 📚 Documentación Adicional

- 📘 [Guía de Implementación Completa](./guia_implementacion.md)
- 📗 [Diagramas PlantUML](./diagrama_c4_licitaciones.md)
- 📙 [README Principal](./README_GITHUB.md)

---

**🎯 ¿Siguiente paso?**  
Revisa la [Guía de Implementación](./guia_implementacion.md) para detalles técnicos completos.

---

*Documento generado con Modelo C4 - Noviembre 2025*

