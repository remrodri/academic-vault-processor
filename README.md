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

## Estado

El proyecto se encuentra en fase de especificación. La ruta aprobada está en [docs/PROJECT_SPEC.md](docs/PROJECT_SPEC.md) y el orden de trabajo en [docs/IMPLEMENTATION_PLAN_V1.md](docs/IMPLEMENTATION_PLAN_V1.md).

## Fuera de alcance en la V1

- Ingesta general de PDF, Excel, CSV e imágenes.
- Diarización completa de hablantes.
- RAG, chat y búsqueda semántica.
- Gestión automática de fechas límite.
- BERTopic y análisis global de múltiples materias.
