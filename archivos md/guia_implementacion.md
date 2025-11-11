# Guía de Implementación - Sistema de Gestión de Licitaciones y Presupuestos

## Índice
1. [Descripción del Sistema](#descripción-del-sistema)
2. [Arquitectura por Capas](#arquitectura-por-capas)
3. [Módulos del Sistema](#módulos-del-sistema)
4. [Patrones de Diseño](#patrones-de-diseño)
5. [Modelo de Datos](#modelo-de-datos)
6. [APIs y Endpoints](#apis-y-endpoints)
7. [Consideraciones Técnicas](#consideraciones-técnicas)

---

## Descripción del Sistema

El **Sistema de Gestión de Licitaciones y Presupuestos** es una aplicación empresarial diseñada para gestionar el ciclo completo de licitaciones de construcción, desde la creación de presupuestos hasta el control detallado de partidas y recursos.

### Características Principales

1. **Gestión de Licitaciones**: Control del estado y seguimiento de licitaciones
2. **Presupuestos Estructurados**: Sistema de plantillas jerárquicas reutilizables
3. **Partidas Presupuestarias**: Composición detallada de recursos por partida
4. **Control de Costos**: Versionamiento de costos de recursos
5. **Auditoría Completa**: Trazabilidad de todos los cambios
6. **Gestión de Obras**: Asociación de obras con presupuestos

---

## Arquitectura por Capas

### 1. Capa de Presentación (Frontend)
**Tecnología**: React.js

**Responsabilidades**:
- Interfaz de usuario responsiva
- Validación de formularios del lado del cliente
- Gestión de estado de la aplicación (Redux/Context API)
- Comunicación con API mediante HTTP

**Módulos principales**:
```
src/
├── modules/
│   ├── licitaciones/
│   │   ├── components/
│   │   ├── pages/
│   │   └── services/
│   ├── presupuestos/
│   │   ├── components/
│   │   ├── pages/
│   │   └── services/
│   ├── partidas/
│   ├── recursos/
│   └── auditoria/
├── shared/
│   ├── components/
│   ├── hooks/
│   └── utils/
└── store/
```

### 2. Capa de API (Backend)
**Tecnología**: Node.js con Express

**Estructura de 3 subcapas**:

#### 2.1 Subcapa de Controladores
Maneja las peticiones HTTP y valida entradas

```javascript
// Ejemplo: controllers/PresupuestoController.js
class PresupuestoController {
    constructor(presupuestoService) {
        this.presupuestoService = presupuestoService;
    }

    async crear(req, res) {
        try {
            const presupuesto = await this.presupuestoService.crear(req.body);
            res.status(201).json(presupuesto);
        } catch (error) {
            res.status(400).json({ error: error.message });
        }
    }

    async obtenerPorId(req, res) {
        try {
            const presupuesto = await this.presupuestoService.obtenerPorId(req.params.id);
            res.json(presupuesto);
        } catch (error) {
            res.status(404).json({ error: error.message });
        }
    }
}
```

#### 2.2 Subcapa de Servicios
Contiene la lógica de negocio

```javascript
// Ejemplo: services/PresupuestoService.js
class PresupuestoService {
    constructor(presupuestoRepo, licitacionService, auditService) {
        this.presupuestoRepo = presupuestoRepo;
        this.licitacionService = licitacionService;
        this.auditService = auditService;
    }

    async crear(datos) {
        // Validar que la licitación existe y está activa
        const licitacion = await this.licitacionService.obtenerPorId(datos.licitacion_id);
        if (!licitacion.estado) {
            throw new Error('La licitación no está activa');
        }

        // Crear presupuesto
        const presupuesto = await this.presupuestoRepo.crear(datos);

        // Registrar en auditoría
        await this.auditService.registrar({
            entidad: 'PRESUPUESTO',
            accion: 'CREAR',
            entidad_id: presupuesto.id,
            usuario_id: datos.usuario_id
        });

        return presupuesto;
    }

    async asociarEstructura(presupuestoId, estructuraIds) {
        // Lógica para asociar títulos, subtítulos y sub-subtítulos
        const relaciones = await this.presupuestoRepo.crearRelacionesEstructura(
            presupuestoId, 
            estructuraIds
        );
        
        return relaciones;
    }
}
```

#### 2.3 Subcapa de Repositorios
Acceso a la base de datos

```javascript
// Ejemplo: repositories/PresupuestoRepository.js
class PresupuestoRepository {
    constructor(db) {
        this.db = db;
    }

    async crear(datos) {
        const query = `
            INSERT INTO PRESUPUESTO (titulo, licitacion_id, fecha)
            VALUES ($1, $2, $3)
            RETURNING *
        `;
        const result = await this.db.query(query, [
            datos.titulo,
            datos.licitacion_id,
            new Date()
        ]);
        return result.rows[0];
    }

    async obtenerPorId(id) {
        const query = `
            SELECT p.*, l.titulo as licitacion_titulo
            FROM PRESUPUESTO p
            JOIN LICITACION l ON p.licitacion_id = l.id
            WHERE p.id = $1
        `;
        const result = await this.db.query(query, [id]);
        return result.rows[0];
    }

    async obtenerConEstructura(id) {
        // Query compleja con JOINs a todas las tablas de estructura
        const query = `
            SELECT 
                p.*,
                json_agg(DISTINCT jsonb_build_object(
                    'id', t.id,
                    'titulo', t.titulo,
                    'descripcion', t.descripcion
                )) as titulos,
                json_agg(DISTINCT jsonb_build_object(
                    'id', s.id,
                    'subtitulo', s.subtitulo,
                    'titulo_id', s.titulo_presupuesto_id
                )) as subtitulos
            FROM PRESUPUESTO p
            LEFT JOIN PRESUPUESTO_TITULO pt ON p.id = pt.presupuesto_id
            LEFT JOIN TITULO_PRESUPUESTO t ON pt.titulo_presupueto_id = t.id
            LEFT JOIN PRESUPUESTO_SUBTITULO ps ON p.id = ps.presupuesto_id
            LEFT JOIN SUBTITULO_PRESUPUESTO s ON ps.subtitulo_presupuesto_id = s.id
            WHERE p.id = $1
            GROUP BY p.id
        `;
        const result = await this.db.query(query, [id]);
        return result.rows[0];
    }
}
```

### 3. Capa de Datos
**Tecnología**: PostgreSQL o MongoDB

---

## Módulos del Sistema

### Módulo 1: Licitaciones

**Entidades**: `LICITACION`

**Funcionalidades**:
- Crear licitaciones
- Actualizar estado de licitaciones
- Consultar licitaciones activas/inactivas
- Asociar múltiples presupuestos a una licitación

**Endpoints**:
```
POST   /api/licitaciones
GET    /api/licitaciones
GET    /api/licitaciones/:id
PUT    /api/licitaciones/:id
DELETE /api/licitaciones/:id
GET    /api/licitaciones/:id/presupuestos
PATCH  /api/licitaciones/:id/estado
```

---

### Módulo 2: Presupuestos

**Entidades**: 
- `PRESUPUESTO`
- `TITULO_PRESUPUESTO` (plantilla)
- `SUBTITULO_PRESUPUESTO` (plantilla)
- `SUB_SUBTITULO_PRESUPUESTO` (plantilla)
- `PRESUPUESTO_TITULO` (relación)
- `PRESUPUESTO_SUBTITULO` (relación)
- `PRESUPUESTO_SUB_SUBTITULO` (relación)

**Concepto Clave: Sistema de Plantillas**

El sistema utiliza un modelo de plantillas donde la estructura presupuestaria (títulos, subtítulos, sub-subtítulos) se define una sola vez y luego se reutiliza en múltiples presupuestos.

```
TITULO_PRESUPUESTO (Plantilla)
    ├── SUBTITULO_PRESUPUESTO (Plantilla)
    │   └── SUB_SUBTITULO_PRESUPUESTO (Plantilla)
    
PRESUPUESTO (Instancia)
    ├── usa → TITULO_PRESUPUESTO (referencia a plantilla)
    ├── usa → SUBTITULO_PRESUPUESTO (referencia a plantilla)
    └── usa → SUB_SUBTITULO_PRESUPUESTO (referencia a plantilla)
```

**Flujo de Trabajo**:
1. Se crean plantillas de estructura (títulos, subtítulos)
2. Al crear un presupuesto, se seleccionan las plantillas a usar
3. Se crean registros en las tablas intermedias para asociar el presupuesto con las plantillas

**Endpoints**:
```
POST   /api/presupuestos
GET    /api/presupuestos
GET    /api/presupuestos/:id
GET    /api/presupuestos/:id/estructura
POST   /api/presupuestos/:id/asociar-estructura
GET    /api/presupuestos/:id/partidas

# Gestión de plantillas
POST   /api/estructura/titulos
POST   /api/estructura/subtitulos
POST   /api/estructura/sub-subtitulos
GET    /api/estructura/titulos
GET    /api/estructura/jerarquia
```

---

### Módulo 3: Obras

**Entidades**: `OBRA`

**Funcionalidades**:
- Asociar obras a presupuestos
- Consultar obras financiadas por presupuesto
- Seguimiento de obras

**Endpoints**:
```
POST   /api/obras
GET    /api/obras
GET    /api/obras/:id
GET    /api/presupuestos/:id/obras
```

---

### Módulo 4: Partidas

**Entidades**:
- `PARTIDA`
- `PARTIDA_TITULO` (relación con plantillas)
- `PARTIDA_SUBTITULO` (relación con plantillas)
- `PARTIDA_SUB_SUBTITULO` (relación con plantillas)

**Concepto Clave**: Las partidas también usan el sistema de plantillas de estructura

**Funcionalidades**:
- Crear partidas asociadas a presupuestos
- Asociar partidas con estructura (títulos, subtítulos)
- Gestionar recursos de partidas
- Calcular costos de partidas

**Estructura de una Partida**:
```
PARTIDA
├── Pertenece a → PRESUPUESTO
├── Usa plantilla → TITULO_PRESUPUESTO
├── Usa plantilla → SUBTITULO_PRESUPUESTO
├── Usa plantilla → SUB_SUBTITULO_PRESUPUESTO
└── Contiene → PARTIDA_RECURSO[]
    ├── RECURSO
    └── PARTIDA_RECURSO_VERSION[] (historial de costos)
```

**Endpoints**:
```
POST   /api/partidas
GET    /api/partidas
GET    /api/partidas/:id
GET    /api/partidas/:id/recursos
POST   /api/partidas/:id/recursos
GET    /api/partidas/:id/estructura
POST   /api/partidas/:id/asociar-estructura
GET    /api/partidas/:id/costo-total
```

---

### Módulo 5: Recursos

**Entidades**:
- `RECURSOS`
- `PARTIDA_RECURSO`
- `PARTIDA_RECURSO_VERSION`
- `RECURSO_ESTIMADO_PARTIDA`

**Concepto Clave: Sistema de Versionamiento**

Cada vez que se modifica el costo de un recurso en una partida, se crea una nueva versión en `PARTIDA_RECURSO_VERSION`, manteniendo un historial completo de cambios.

```
PARTIDA_RECURSO
├── costo: 100.00 (costo actual)
├── costo_meta: 95.00 (costo objetivo)
└── Versiones:
    ├── Version 1: 100.00 (2024-01-01)
    ├── Version 2: 105.00 (2024-02-15)
    └── Version 3: 100.00 (2024-03-20) ← actual
```

**Funcionalidades**:
- Catálogo centralizado de recursos
- Asignación de recursos a partidas
- Control de costos actuales y meta
- Historial de versiones de costos
- Recursos estimados por partida

**Endpoints**:
```
# Gestión de recursos
POST   /api/recursos
GET    /api/recursos
GET    /api/recursos/:id
GET    /api/recursos/:codigo

# Recursos en partidas
POST   /api/partidas/:partidaId/recursos
PUT    /api/partidas/:partidaId/recursos/:recursoId
GET    /api/partidas/:partidaId/recursos/:recursoId/versiones
POST   /api/partidas/:partidaId/recursos/:recursoId/actualizar-costo

# Recursos estimados
POST   /api/partidas/:partidaId/recursos-estimados
GET    /api/partidas/:partidaId/recursos-estimados
```

---

### Módulo 6: Auditoría

**Entidades**: `LOG_PRESUPUESTO`

**Funcionalidades**:
- Registro automático de todos los cambios
- Trazabilidad completa del sistema
- Consultas de auditoría
- Análisis de cambios históricos

**Información Registrada**:
- Usuario que realizó el cambio
- Recurso afectado
- Motivo del cambio
- Fecha y hora exacta

**Endpoints**:
```
GET    /api/auditoria/logs
GET    /api/auditoria/logs/:id
GET    /api/auditoria/recursos/:recursoId/logs
GET    /api/auditoria/usuarios/:usuarioId/logs
GET    /api/auditoria/presupuestos/:presupuestoId/logs
POST   /api/auditoria/logs (uso interno)
```

---

## Patrones de Diseño

### 1. Repository Pattern
Abstrae el acceso a datos

```javascript
// Interface implícita
class IRepository {
    crear(datos) {}
    obtenerPorId(id) {}
    actualizar(id, datos) {}
    eliminar(id) {}
    obtenerTodos(filtros) {}
}

// Implementación concreta
class PresupuestoRepository extends IRepository {
    // Implementación específica para PostgreSQL
}
```

### 2. Service Layer Pattern
Encapsula la lógica de negocio

```javascript
class PresupuestoService {
    // Orquesta múltiples repositorios y servicios
    async crearPresupuestoCompleto(datos) {
        // 1. Validar licitación
        // 2. Crear presupuesto
        // 3. Asociar estructura
        // 4. Registrar auditoría
        // 5. Enviar notificaciones
    }
}
```

### 3. Dependency Injection
Facilita testing y mantenibilidad

```javascript
// Inyección de dependencias en constructor
class PresupuestoService {
    constructor(presupuestoRepo, licitacionService, auditService, notificationService) {
        this.presupuestoRepo = presupuestoRepo;
        this.licitacionService = licitacionService;
        this.auditService = auditService;
        this.notificationService = notificationService;
    }
}
```

### 4. Template Pattern
Sistema de plantillas reutilizables para estructura presupuestaria

### 5. Version Control Pattern
Control de versiones en costos de recursos

---

## Modelo de Datos - Relaciones Clave

### Relación Licitación → Presupuesto (1:N)
Una licitación puede tener múltiples presupuestos

### Relación Presupuesto → Obra (1:N)
Un presupuesto puede financiar múltiples obras

### Relación Presupuesto ↔ Estructura (N:M)
Un presupuesto puede usar múltiples títulos/subtítulos
Un título/subtítulo puede ser usado por múltiples presupuestos

### Relación Partida → Recursos (N:M con atributos)
Una partida contiene múltiples recursos
Un recurso puede estar en múltiples partidas
La relación tiene atributos: costo, costo_meta

### Relación Partida_Recurso → Versiones (1:N)
Cada asignación de recurso a partida tiene múltiples versiones

---

## APIs y Endpoints

### Estructura de Respuestas

**Éxito (200-299)**:
```json
{
  "success": true,
  "data": { ... },
  "message": "Operación exitosa"
}
```

**Error (400-599)**:
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Descripción del error",
    "details": [ ... ]
  }
}
```

### Paginación
```
GET /api/presupuestos?page=1&limit=20&sort=fecha&order=desc
```

### Filtrado
```
GET /api/licitaciones?estado=true&fecha_desde=2024-01-01&fecha_hasta=2024-12-31
```

---

## Consideraciones Técnicas

### 1. Performance

**Optimizaciones recomendadas**:
- Índices en claves foráneas
- Índices compuestos para consultas frecuentes
- Cache de plantillas de estructura (Redis)
- Paginación en todas las listas
- Queries optimizadas con JOINs estratégicos

```sql
-- Índices recomendados
CREATE INDEX idx_presupuesto_licitacion ON PRESUPUESTO(licitacion_id);
CREATE INDEX idx_partida_presupuesto ON PARTIDA(presupuesto_id);
CREATE INDEX idx_partida_recurso_partida ON PARTIDA_RECURSO(partida_id);
CREATE INDEX idx_partida_recurso_recurso ON PARTIDA_RECURSO(recurso_id);
CREATE INDEX idx_log_fecha ON LOG_PRESUPUETO(fecha);
CREATE INDEX idx_log_usuario ON LOG_PRESUPUETO(usario_id);
```

### 2. Seguridad

**Medidas de seguridad**:
- Autenticación JWT
- Autorización basada en roles (RBAC)
- Validación de entradas
- Sanitización de datos
- Rate limiting en APIs
- Logs de acceso

### 3. Escalabilidad

**Estrategias**:
- Arquitectura modular permite escalar servicios independientemente
- Separación de lectura/escritura (CQRS opcional)
- Cache distribuido (Redis)
- Load balancing
- Database sharding (si es necesario)

### 4. Mantenibilidad

**Buenas prácticas**:
- Código organizado por dominio
- Documentación inline
- Tests unitarios y de integración
- Versionamiento semántico de API
- Logs estructurados

### 5. Monitoreo

**Métricas clave**:
- Tiempo de respuesta de APIs
- Tasa de errores
- Uso de recursos (CPU, memoria)
- Queries lentas de BD
- Logs de auditoría

---

## Ejemplo de Flujo Completo

### Caso de Uso: Crear Presupuesto con Partidas y Recursos

```javascript
// 1. Usuario crea una licitación
POST /api/licitaciones
{
  "titulo": "Construcción de Puente",
  "estado": true,
  "fecha": "2024-01-15"
}
// → Retorna: { id: 1, titulo: "Construcción de Puente", ... }

// 2. Usuario crea un presupuesto para la licitación
POST /api/presupuestos
{
  "titulo": "Presupuesto Principal",
  "licitacion_id": 1,
  "fecha": "2024-01-20"
}
// → Retorna: { id: 1, titulo: "Presupuesto Principal", ... }

// 3. Usuario asocia estructura al presupuesto
POST /api/presupuestos/1/asociar-estructura
{
  "titulos": [1, 2, 3],
  "subtitulos": [1, 2, 3, 4],
  "sub_subtitulos": [1, 2]
}
// → Crea relaciones en PRESUPUESTO_TITULO, PRESUPUESTO_SUBTITULO, etc.

// 4. Usuario crea una partida
POST /api/partidas
{
  "titulo": "Excavación y Movimiento de Tierras",
  "presupuesto_id": 1
}
// → Retorna: { id: 1, titulo: "Excavación...", ... }

// 5. Usuario asocia estructura a la partida
POST /api/partidas/1/asociar-estructura
{
  "titulo_id": 1,
  "subtitulo_id": 1,
  "sub_subtitulo_id": 1
}

// 6. Usuario agrega recursos a la partida
POST /api/partidas/1/recursos
{
  "recurso_id": 101,
  "costo": 1500.00,
  "costo_meta": 1400.00
}
// → Crea PARTIDA_RECURSO y primera version en PARTIDA_RECURSO_VERSION
// → Registra en LOG_PRESUPUESTO

// 7. Usuario actualiza costo del recurso
POST /api/partidas/1/recursos/101/actualizar-costo
{
  "nuevo_costo": 1450.00,
  "motivo": "Ajuste por variación en precios de mercado"
}
// → Actualiza PARTIDA_RECURSO.costo
// → Crea nueva version en PARTIDA_RECURSO_VERSION
// → Registra en LOG_PRESUPUESTO

// 8. Usuario consulta historial de versiones
GET /api/partidas/1/recursos/101/versiones
// → Retorna array con todas las versiones y sus fechas
```

---

## Conclusión

Esta arquitectura proporciona:
- ✅ Separación clara de responsabilidades
- ✅ Escalabilidad horizontal y vertical
- ✅ Trazabilidad completa
- ✅ Flexibilidad mediante plantillas
- ✅ Control de versiones de costos
- ✅ Mantenibilidad a largo plazo

