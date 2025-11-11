# 📊 Documentación C4 - Sistema de Gestión de Licitaciones y Presupuestos

## 📁 Archivos Generados

Esta documentación contiene el análisis arquitectónico completo del sistema basado en el diagrama entidad-relación proporcionado.

---

## 🗂️ Contenido de la Documentación

### 1. **diagrama_c4_licitaciones.md** (13 KB)
**Diagramas C4 en formato PlantUML**

Contiene los 4 niveles del modelo C4:
- ✅ **Nivel 1 - Contexto**: Vista general del sistema, usuarios y sistemas externos
- ✅ **Nivel 2 - Contenedores**: Aplicaciones, APIs y almacenes de datos
- ✅ **Nivel 3 - Componentes (API Backend)**: Componentes internos del backend
- ✅ **Nivel 4 - Componentes Detallados**: Vista detallada del módulo de presupuestos

**Cómo usar**:
- Copiar el código PlantUML en https://www.plantuml.com/plantuml/uml/
- Usar extensión PlantUML en VS Code
- Usar herramientas como PlantText

**Incluye**:
- Descripción de capas y componentes
- Módulos principales del sistema
- Patrones de diseño implementados
- Consideraciones de arquitectura

---

### 2. **diagrama_c4_mermaid.md** (7 KB)
**Diagramas C4 en formato Mermaid**

Versión alternativa de los diagramas para visualización directa en:
- GitHub/GitLab (se renderiza automáticamente)
- Notion
- Obsidian
- VS Code con extensión Mermaid

**Contiene**:
- ✅ Diagrama de Contexto simplificado
- ✅ Diagrama de Contenedores
- ✅ Arquitectura de Componentes Backend
- ✅ Modelo de Dominio con entidades principales
- ✅ Diagrama de secuencia del flujo de datos principal

**Ventajas**:
- Visualización inmediata en GitHub
- Sintaxis más simple
- Fácil de editar y mantener

---

### 3. **guia_implementacion.md** (18 KB)
**Guía Completa de Implementación**

Documento técnico exhaustivo con:

#### 📋 Contenido:

1. **Descripción del Sistema**
   - Características principales
   - Objetivos del sistema

2. **Arquitectura por Capas**
   - Capa de Presentación (React.js)
   - Capa de API (Node.js/Express)
   - Capa de Datos (PostgreSQL/MongoDB)
   - Ejemplos de código para cada capa

3. **Módulos del Sistema** (Detallados)
   - 🏗️ Módulo de Licitaciones
   - 📊 Módulo de Presupuestos
   - 🏢 Módulo de Obras
   - 📝 Módulo de Partidas
   - 🔧 Módulo de Recursos
   - 📜 Módulo de Auditoría

4. **Patrones de Diseño**
   - Repository Pattern
   - Service Layer Pattern
   - Dependency Injection
   - Template Pattern
   - Version Control Pattern

5. **APIs y Endpoints**
   - Listado completo de endpoints
   - Estructura de respuestas
   - Paginación y filtrado

6. **Consideraciones Técnicas**
   - Performance y optimización
   - Seguridad
   - Escalabilidad
   - Mantenibilidad
   - Monitoreo

7. **Ejemplo de Flujo Completo**
   - Caso de uso end-to-end con código

---

## 🎯 Conceptos Clave del Sistema

### 1. Sistema de Plantillas (Template System)
El sistema utiliza plantillas reutilizables para la estructura presupuestaria:
- **TITULO_PRESUPUESTO** (plantilla)
- **SUBTITULO_PRESUPUESTO** (plantilla)
- **SUB_SUBTITULO_PRESUPUESTO** (plantilla)

Estas plantillas se crean una vez y se reutilizan en múltiples presupuestos y partidas mediante tablas intermedias.

### 2. Sistema de Versionamiento
Control de versiones para costos de recursos:
- **PARTIDA_RECURSO**: Mantiene el costo actual
- **PARTIDA_RECURSO_VERSION**: Almacena historial completo de cambios

Cada modificación de costo crea una nueva versión con timestamp.

### 3. Auditoría Completa
**LOG_PRESUPUESTO** registra todos los cambios:
- Usuario que realizó el cambio
- Recurso afectado
- Motivo del cambio
- Fecha y hora exacta

### 4. Arquitectura por Capas
**3 capas principales**:
1. **Controladores**: Manejan HTTP requests/responses
2. **Servicios**: Contienen lógica de negocio
3. **Repositorios**: Acceso a base de datos

---

## 🏗️ Arquitectura General

```
┌─────────────────────────────────────────────────┐
│           USUARIOS DEL SISTEMA                   │
│  Gestores │ Ingenieros │ Auditores │ Directores │
└─────────────────┬───────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────┐
│         CAPA DE PRESENTACIÓN                     │
│         Aplicación Web (React.js)                │
└─────────────────┬───────────────────────────────┘
                  │ HTTPS/JSON
                  ▼
┌─────────────────────────────────────────────────┐
│         CAPA DE API (Node.js/Express)            │
│  ┌──────────────────────────────────────────┐   │
│  │  Subcapa de Controladores                │   │
│  │  - Licitaciones - Presupuestos           │   │
│  │  - Partidas - Recursos - Auditoría       │   │
│  └────────────────┬─────────────────────────┘   │
│                   │                              │
│  ┌────────────────▼─────────────────────────┐   │
│  │  Subcapa de Servicios de Negocio         │   │
│  │  - Validaciones - Orquestación           │   │
│  │  - Cálculos - Versionamiento             │   │
│  └────────────────┬─────────────────────────┘   │
│                   │                              │
│  ┌────────────────▼─────────────────────────┐   │
│  │  Subcapa de Repositorios                 │   │
│  │  - Acceso a Datos - Queries              │   │
│  └────────────────┬─────────────────────────┘   │
└───────────────────┼──────────────────────────────┘
                    │
                    ▼
┌─────────────────────────────────────────────────┐
│         CAPA DE DATOS                            │
│    Base de Datos (PostgreSQL/MongoDB)            │
│  - Licitaciones - Presupuestos - Partidas        │
│  - Recursos - Estructura - Logs                  │
└─────────────────────────────────────────────────┘
```

---

## 📊 Entidades Principales

### Jerarquía de Datos

```
LICITACION
├── PRESUPUESTO (1:N)
│   ├── OBRA (1:N)
│   ├── Usa → TITULO_PRESUPUESTO (N:M)
│   ├── Usa → SUBTITULO_PRESUPUESTO (N:M)
│   ├── Usa → SUB_SUBTITULO_PRESUPUESTO (N:M)
│   └── PARTIDA (1:N)
│       ├── Usa → TITULO_PRESUPUESTO (N:M)
│       ├── Usa → SUBTITULO_PRESUPUESTO (N:M)
│       ├── Usa → SUB_SUBTITULO_PRESUPUESTO (N:M)
│       └── PARTIDA_RECURSO (N:M)
│           ├── RECURSOS
│           ├── PARTIDA_RECURSO_VERSION (1:N)
│           └── RECURSO_ESTIMADO_PARTIDA
│
└── LOG_PRESUPUESTO (Auditoría de todo)
```

---

## 🚀 Cómo Empezar

### 1. Revisar Diagramas
Comenzar con **diagrama_c4_mermaid.md** para obtener una vista visual rápida del sistema.

### 2. Entender la Arquitectura
Leer **diagrama_c4_licitaciones.md** para comprender los niveles de la arquitectura C4.

### 3. Implementar
Usar **guia_implementacion.md** como referencia técnica para el desarrollo.

---

## 💡 Stack Tecnológico Recomendado

### Frontend
- **Framework**: React.js 18+
- **State Management**: Redux Toolkit o Zustand
- **UI**: Material-UI o Ant Design
- **Forms**: React Hook Form + Yup

### Backend
- **Runtime**: Node.js 18+ LTS
- **Framework**: Express.js
- **ORM**: TypeORM o Sequelize (SQL) / Mongoose (MongoDB)
- **Validation**: Joi o Zod
- **Authentication**: JWT + Passport.js

### Base de Datos
- **Opción 1**: PostgreSQL 15+ (Recomendado para relaciones complejas)
- **Opción 2**: MongoDB 6+ (Para mayor flexibilidad)
- **Cache**: Redis

### DevOps
- **Containerización**: Docker + Docker Compose
- **CI/CD**: GitHub Actions o GitLab CI
- **Monitoreo**: PM2 + New Relic o DataDog

---

## 📈 Próximos Pasos

1. ✅ Revisar y validar la arquitectura propuesta
2. ⬜ Definir tecnologías específicas del stack
3. ⬜ Crear prototipos de interfaces
4. ⬜ Desarrollar MVP con módulo de licitaciones y presupuestos
5. ⬜ Implementar sistema de autenticación y autorización
6. ⬜ Desarrollar módulos de partidas y recursos
7. ⬜ Implementar sistema de auditoría
8. ⬜ Realizar pruebas de integración
9. ⬜ Deployment en ambiente de staging
10. ⬜ Capacitación de usuarios y go-live

---

## 📞 Soporte

Para cualquier duda o aclaración sobre esta arquitectura:
- Revisar los 3 documentos proporcionados
- Consultar ejemplos de código en **guia_implementacion.md**
- Validar patrones de diseño según caso de uso específico

---

## 📄 Licencia y Uso

Esta documentación ha sido generada para el proyecto de **Sistema de Gestión de Licitaciones y Presupuestos** y puede ser adaptada según las necesidades específicas del proyecto.

**Fecha de Generación**: Noviembre 2025
**Versión**: 1.0
**Formato**: Markdown con PlantUML y Mermaid

---

## 🎓 Referencias

- **Modelo C4**: https://c4model.com/
- **PlantUML**: https://plantuml.com/
- **Mermaid**: https://mermaid.js.org/
- **Clean Architecture**: Robert C. Martin
- **Domain-Driven Design**: Eric Evans

