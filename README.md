🧱 Arquitectura C4 de Nufago

Este proyecto documenta y visualiza la arquitectura de Nufago utilizando el modelo C4, representando sus microservicios, capas lógicas y relaciones.
Se basa en LikeC4
, una herramienta declarativa para construir, validar y publicar diagramas de arquitectura mantenibles.

🎯 Propósito

Describir la arquitectura actual de Nufago de forma clara y versionada.

Modelar los microservicios, interfaces y dependencias técnicas.

Facilitar la comunicación entre equipos de desarrollo, infraestructura y arquitectura.

Generar diagramas automatizados y documentación técnica reproducible.

🧩 Estructura del repositorio
nufago-architecture/
├── src/                # Definiciones C4 (.c4)
├── test/               # Validaciones del modelo
├── likec4.config.ts    # Configuración del proyecto LikeC4
├── package.json        # Dependencias y scripts
└── .github/workflows/  # CI/CD y despliegue automático

⚙️ Instalación y uso
1️⃣ Instalar dependencias
npm install

2️⃣ Generar el modelo
npx likec4 build


Esto compila los archivos .c4 y genera la salida estática dentro de /dist.

3️⃣ Visualizar la arquitectura
npx likec4 preview


Abre un servidor local para explorar los diagramas generados en el navegador.

🧪 Validación del modelo

Para verificar que el modelo sea consistente:

npm run test

🚀 Despliegue

El sitio estático se puede desplegar en:

GitHub Pages (workflow incluido en .github/workflows/pages.yml)

Cualquier servicio de hosting estático compatible (Vercel, Netlify, etc.)

Construcción manual:

npx likec4 build -o ./dist

📊 Alcance del modelo C4

El modelo cubre las siguientes vistas:

Contexto – Ecosistema global de Nufago y actores externos.

Contenedores – Microservicios, bases de datos y componentes externos.

Componentes – Submódulos dentro de cada microservicio.

Código (opcional) – Enlaces entre elementos de código fuente (si aplica).

📈 Próximos pasos

Incorporar diagramas de flujos de eventos (mensajería, colas, etc.)

Añadir métricas y observabilidad a nivel de microservicio.

Integrar validación automática en CI/CD.

📄 Licencia

Distribuido bajo la MIT License
.
© Nufago — Arquitectura de Sistemas, 2025.
