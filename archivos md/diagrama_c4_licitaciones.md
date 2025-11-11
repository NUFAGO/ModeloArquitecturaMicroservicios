# Diagrama C4 - Sistema de Gestión de Licitaciones y Presupuestos

## Nivel 1: Diagrama de Contexto

```plantuml
@startuml C4_Context
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Context.puml

LAYOUT_WITH_LEGEND()

title Diagrama de Contexto - Sistema de Gestión de Licitaciones y Presupuestos

Person(gestor, "Gestor de Licitaciones", "Administra licitaciones y crea presupuestos iniciales")
Person(ingeniero, "Ingeniero de Costos", "Gestiona partidas, recursos y versiones de presupuestos")
Person(auditor, "Auditor", "Supervisa cambios y revisa logs del sistema")
Person(director, "Director de Proyecto", "Consulta obras y estado de presupuestos")

System(sistema, "Sistema de Gestión de Licitaciones y Presupuestos", "Gestiona el ciclo completo de licitaciones, presupuestos, partidas y recursos para proyectos de construcción")

System_Ext(erp, "Sistema ERP", "Sistema empresarial para contabilidad y finanzas")
System_Ext(docs, "Sistema de Documentos", "Almacenamiento de documentación técnica")

Rel(gestor, sistema, "Crea y gestiona licitaciones y presupuestos", "HTTPS")
Rel(ingeniero, sistema, "Administra partidas, recursos y costos", "HTTPS")
Rel(auditor, sistema, "Consulta logs y auditoría", "HTTPS")
Rel(director, sistema, "Consulta obras y presupuestos", "HTTPS")

Rel(sistema, erp, "Exporta datos financieros", "API REST")
Rel(sistema, docs, "Almacena documentación", "API REST")

@enduml
```

## Nivel 2: Diagrama de Contenedores

```plantuml
@startuml C4_Container
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

LAYOUT_WITH_LEGEND()

title Diagrama de Contenedores - Sistema de Gestión de Licitaciones y Presupuestos

Person(usuario, "Usuario del Sistema", "Gestores, Ingenieros, Auditores")

System_Boundary(sistema, "Sistema de Gestión de Licitaciones y Presupuestos") {
    Container(webapp, "Aplicación Web", "React.js", "Interfaz de usuario para gestión de licitaciones, presupuestos y recursos")
    
    Container(api, "API REST", "Node.js, Express", "Proporciona funcionalidad de negocio mediante API REST")
    
    Container(auth, "Servicio de Autenticación", "Node.js, JWT", "Gestiona autenticación y autorización de usuarios")
    
    ContainerDb(db, "Base de Datos Principal", "MongoDB/PostgreSQL", "Almacena licitaciones, presupuestos, partidas, recursos y sus relaciones")
    
    ContainerDb(cache, "Cache", "Redis", "Cache de consultas frecuentes y sesiones")
    
    Container(notif, "Servicio de Notificaciones", "Node.js", "Envía notificaciones sobre cambios en presupuestos")
}

System_Ext(erp, "Sistema ERP", "Sistema empresarial")
System_Ext(email, "Servicio de Email", "Notificaciones por correo")

Rel(usuario, webapp, "Usa", "HTTPS")
Rel(webapp, api, "Realiza llamadas API", "JSON/HTTPS")
Rel(webapp, auth, "Autentica", "JWT/HTTPS")

Rel(api, db, "Lee y escribe datos", "SQL/NoSQL")
Rel(api, cache, "Lee/Escribe cache", "Redis Protocol")
Rel(api, notif, "Envía eventos", "Message Queue")
Rel(auth, db, "Valida credenciales", "SQL/NoSQL")

Rel(notif, email, "Envía emails", "SMTP")
Rel(api, erp, "Exporta datos", "REST API")

@enduml
```

## Nivel 3: Diagrama de Componentes (API Backend)

```plantuml
@startuml C4_Component
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

LAYOUT_WITH_LEGEND()

title Diagrama de Componentes - API Backend

Container(webapp, "Aplicación Web", "React.js", "Interfaz de usuario")

Container_Boundary(api, "API REST - Node.js") {
    Component(licitacionCtrl, "Controlador de Licitaciones", "Express Controller", "Gestiona endpoints de licitaciones")
    
    Component(presupuestoCtrl, "Controlador de Presupuestos", "Express Controller", "Gestiona endpoints de presupuestos y obras")
    
    Component(partidaCtrl, "Controlador de Partidas", "Express Controller", "Gestiona endpoints de partidas")
    
    Component(recursoCtrl, "Controlador de Recursos", "Express Controller", "Gestiona endpoints de recursos")
    
    Component(estructuraCtrl, "Controlador de Estructura", "Express Controller", "Gestiona títulos, subtítulos y sub-subtítulos")
    
    Component(auditCtrl, "Controlador de Auditoría", "Express Controller", "Gestiona logs y auditoría")
    
    Component(licitacionSvc, "Servicio de Licitaciones", "Business Logic", "Lógica de negocio para licitaciones")
    
    Component(presupuestoSvc, "Servicio de Presupuestos", "Business Logic", "Lógica de negocio para presupuestos, plantillas y relaciones")
    
    Component(partidaSvc, "Servicio de Partidas", "Business Logic", "Gestión de partidas y relaciones con estructura")
    
    Component(recursoSvc, "Servicio de Recursos", "Business Logic", "Gestión de recursos y costos")
    
    Component(versionSvc, "Servicio de Versionamiento", "Business Logic", "Control de versiones de recursos en partidas")
    
    Component(auditSvc, "Servicio de Auditoría", "Business Logic", "Registro de cambios y trazabilidad")
    
    Component(licitacionRepo, "Repositorio Licitaciones", "Data Access", "Acceso a datos de licitaciones")
    
    Component(presupuestoRepo, "Repositorio Presupuestos", "Data Access", "Acceso a datos de presupuestos y estructura")
    
    Component(partidaRepo, "Repositorio Partidas", "Data Access", "Acceso a datos de partidas y relaciones")
    
    Component(recursoRepo, "Repositorio Recursos", "Data Access", "Acceso a datos de recursos y versiones")
    
    Component(logRepo, "Repositorio Logs", "Data Access", "Acceso a datos de auditoría")
}

ContainerDb(db, "Base de Datos", "MongoDB/PostgreSQL", "Almacenamiento persistente")

' Relaciones Web -> Controllers
Rel(webapp, licitacionCtrl, "Gestiona licitaciones", "JSON/HTTPS")
Rel(webapp, presupuestoCtrl, "Gestiona presupuestos", "JSON/HTTPS")
Rel(webapp, partidaCtrl, "Gestiona partidas", "JSON/HTTPS")
Rel(webapp, recursoCtrl, "Gestiona recursos", "JSON/HTTPS")
Rel(webapp, estructuraCtrl, "Gestiona estructura", "JSON/HTTPS")
Rel(webapp, auditCtrl, "Consulta logs", "JSON/HTTPS")

' Relaciones Controllers -> Services
Rel(licitacionCtrl, licitacionSvc, "Usa")
Rel(presupuestoCtrl, presupuestoSvc, "Usa")
Rel(partidaCtrl, partidaSvc, "Usa")
Rel(recursoCtrl, recursoSvc, "Usa")
Rel(estructuraCtrl, presupuestoSvc, "Usa")
Rel(auditCtrl, auditSvc, "Usa")

' Relaciones Services -> Services
Rel(presupuestoSvc, licitacionSvc, "Valida licitación")
Rel(partidaSvc, presupuestoSvc, "Valida presupuesto")
Rel(recursoSvc, versionSvc, "Gestiona versiones")
Rel(recursoSvc, auditSvc, "Registra cambios")
Rel(presupuestoSvc, auditSvc, "Registra cambios")

' Relaciones Services -> Repositories
Rel(licitacionSvc, licitacionRepo, "Usa")
Rel(presupuestoSvc, presupuestoRepo, "Usa")
Rel(partidaSvc, partidaRepo, "Usa")
Rel(recursoSvc, recursoRepo, "Usa")
Rel(versionSvc, recursoRepo, "Usa")
Rel(auditSvc, logRepo, "Usa")

' Relaciones Repositories -> DB
Rel(licitacionRepo, db, "Lee/Escribe", "SQL/NoSQL")
Rel(presupuestoRepo, db, "Lee/Escribe", "SQL/NoSQL")
Rel(partidaRepo, db, "Lee/Escribe", "SQL/NoSQL")
Rel(recursoRepo, db, "Lee/Escribe", "SQL/NoSQL")
Rel(logRepo, db, "Lee/Escribe", "SQL/NoSQL")

@enduml
```

## Nivel 4: Diagrama de Componentes Detallado (Módulo de Presupuestos)

```plantuml
@startuml C4_Component_Detail
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

LAYOUT_TOP_DOWN()

title Diagrama de Componentes Detallado - Módulo de Presupuestos

Component(presupuestoCtrl, "Controlador de Presupuestos", "Express Controller")

Container_Boundary(presupuestoModule, "Módulo de Presupuestos") {
    
    Component(presupuestoSvc, "Servicio Principal", "Service", "Orquesta operaciones de presupuestos")
    
    Component(estructuraSvc, "Servicio de Estructura", "Service", "Gestiona títulos, subtítulos y sub-subtítulos como plantillas")
    
    Component(relacionSvc, "Servicio de Relaciones", "Service", "Gestiona relaciones entre presupuesto y estructura")
    
    Component(validacionSvc, "Servicio de Validación", "Service", "Valida integridad de presupuestos")
    
    Component(obraSvc, "Servicio de Obras", "Service", "Gestiona obras asociadas a presupuestos")
    
    Component(calculoSvc, "Servicio de Cálculo", "Service", "Calcula totales y subtotales")
    
    Component(presupuestoRepo, "Repositorio Presupuesto", "Repository", "CRUD de presupuestos")
    
    Component(tituloRepo, "Repositorio Títulos", "Repository", "CRUD de títulos presupuestarios")
    
    Component(subtituloRepo, "Repositorio Subtítulos", "Repository", "CRUD de subtítulos")
    
    Component(subSubtituloRepo, "Repositorio Sub-Subtítulos", "Repository", "CRUD de sub-subtítulos")
    
    Component(relacionRepo, "Repositorio Relaciones", "Repository", "Gestiona tablas intermedias")
    
    Component(obraRepo, "Repositorio Obras", "Repository", "CRUD de obras")
}

ContainerDb(db, "Base de Datos", "Relacional", "Almacena datos")

Component(licitacionSvc, "Servicio de Licitaciones", "External Service")
Component(partidaSvc, "Servicio de Partidas", "External Service")
Component(auditSvc, "Servicio de Auditoría", "External Service")

' Relaciones Controller -> Service
Rel(presupuestoCtrl, presupuestoSvc, "Orquesta operaciones")

' Relaciones Service Principal
Rel(presupuestoSvc, estructuraSvc, "Obtiene plantillas")
Rel(presupuestoSvc, relacionSvc, "Asocia estructura")
Rel(presupuestoSvc, validacionSvc, "Valida")
Rel(presupuestoSvc, obraSvc, "Gestiona obras")
Rel(presupuestoSvc, calculoSvc, "Calcula totales")
Rel(presupuestoSvc, licitacionSvc, "Valida licitación")
Rel(presupuestoSvc, auditSvc, "Registra cambios")

' Relaciones Estructura
Rel(estructuraSvc, tituloRepo, "Usa")
Rel(estructuraSvc, subtituloRepo, "Usa")
Rel(estructuraSvc, subSubtituloRepo, "Usa")

' Relaciones Relaciones
Rel(relacionSvc, relacionRepo, "Usa")
Rel(relacionSvc, validacionSvc, "Valida consistencia")

' Relaciones Validación
Rel(validacionSvc, presupuestoRepo, "Consulta datos")
Rel(validacionSvc, partidaSvc, "Valida partidas")

' Relaciones Obra
Rel(obraSvc, obraRepo, "Usa")
Rel(obraSvc, presupuestoRepo, "Consulta presupuesto")

' Relaciones Cálculo
Rel(calculoSvc, presupuestoRepo, "Consulta datos")
Rel(calculoSvc, partidaSvc, "Obtiene costos de partidas")

' Relaciones a BD
Rel(presupuestoRepo, db, "CRUD")
Rel(tituloRepo, db, "CRUD")
Rel(subtituloRepo, db, "CRUD")
Rel(subSubtituloRepo, db, "CRUD")
Rel(relacionRepo, db, "CRUD")
Rel(obraRepo, db, "CRUD")

@enduml
```

## Descripción de Capas y Componentes

### Capa de Presentación
- **Aplicación Web (React.js)**: Interfaz de usuario responsiva para todos los actores del sistema

### Capa de API
- **Controladores**: Manejan las peticiones HTTP y respuestas
- **Servicios de Negocio**: Contienen la lógica de negocio principal
- **Repositorios**: Capa de acceso a datos (patrón Repository)

### Módulos Principales

#### 1. Módulo de Licitaciones
- Gestión del ciclo de vida de licitaciones
- Control de estado de licitaciones

#### 2. Módulo de Presupuestos
- Creación y gestión de presupuestos
- Asociación con licitaciones
- Gestión de estructura jerárquica (títulos, subtítulos, sub-subtítulos)
- Sistema de plantillas reutilizables

#### 3. Módulo de Obras
- Asociación de obras con presupuestos
- Seguimiento de obras financiadas

#### 4. Módulo de Partidas
- Gestión de partidas presupuestarias
- Asociación con estructura (títulos, subtítulos)
- Composición de recursos

#### 5. Módulo de Recursos
- Catálogo de recursos
- Gestión de costos
- Sistema de versionamiento de costos
- Recursos estimados por partida

#### 6. Módulo de Auditoría
- Registro de cambios (LOG_PRESUPUESTO)
- Trazabilidad completa
- Consulta de históricos

### Patrones de Diseño Implementados

1. **Repository Pattern**: Abstracción de acceso a datos
2. **Service Layer**: Lógica de negocio centralizada
3. **Controller Pattern**: Manejo de peticiones HTTP
4. **Template Pattern**: Sistema de plantillas para estructura presupuestaria
5. **Version Control Pattern**: Control de versiones en recursos de partidas

### Consideraciones de Arquitectura

- **Separación de Responsabilidades**: Clara división entre capas
- **Escalabilidad**: Arquitectura modular permite escalar componentes independientemente
- **Mantenibilidad**: Código organizado por dominio de negocio
- **Trazabilidad**: Sistema de auditoría completo
- **Reutilización**: Plantillas de estructura presupuestaria
- **Versionamiento**: Control de cambios en costos de recursos

