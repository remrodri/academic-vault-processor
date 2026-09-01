# Academic Vault Processor

Herramienta CLI local para convertir sesiones académicas grabadas en notas de Obsidian estructuradas y enlazadas.

## Alcance de la V1

```text
MP4 + metadatos + diapositivas opcionales
                  ↓
        Faster-Whisper local
                  ↓
       transcripción normalizada
                  ↓
       análisis con Gemini Flash
                  ↓
       consolidación académica
                  ↓
       notas Markdown para Obsidian
```

Gemini Pro puede mejorar la consolidación cuando tenga cuota gratuita, pero nunca es necesario para completar el flujo.

## Entrada esperada

```text
input/
└── gestion-de-riesgos/
    └── sesion-04/
        ├── session.yaml
        ├── clase.mp4
        └── diapositivas.pdf
```

```yaml
course: Gestion de Riesgos
session_number: 4
date: 2026-08-28
title: null
language: es
video: clase.mp4
slides: diapositivas.pdf
```

## Comandos previstos

```bash
academic-vault process ./input/gestion-de-riesgos/sesion-04
academic-vault resume <session-id>
academic-vault status
```

## Configuración local

Crear el archivo local de configuración a partir de la plantilla:

```powershell
Copy-Item .env.example .env
```

Completar en `.env` la clave de Gemini y las opciones de la aplicación. `.env` está excluido de Git y nunca debe contenerse en un commit.

La aplicación Python cargará `.env` mediante `pydantic-settings`. Context7 es utilizado por OpenCode antes de iniciar la aplicación, por lo que su clave debe configurarse como variable de usuario de Windows:

```powershell
[Environment]::SetEnvironmentVariable(
    "CONTEXT7_API_KEY",
    "TU_NUEVA_CLAVE",
    "User"
)
```

Para utilizarla también en la terminal actual:

```powershell
$env:CONTEXT7_API_KEY = "TU_NUEVA_CLAVE"
```

Después se debe reiniciar OpenCode. `opencode.json` solo referencia `{env:CONTEXT7_API_KEY}` y no almacena la clave.

## Estado

El proyecto se encuentra en fase de especificación. La ruta aprobada está en [docs/PROJECT_SPEC.md](docs/PROJECT_SPEC.md) y el orden de trabajo en [docs/IMPLEMENTATION_PLAN_V1.md](docs/IMPLEMENTATION_PLAN_V1.md).

## Fuera de alcance en la V1

- Ingesta general de PDF, Excel, CSV e imágenes.
- Diarización completa de hablantes.
- RAG, chat y búsqueda semántica.
- Gestión automática de fechas límite.
- BERTopic y análisis global de múltiples materias.
