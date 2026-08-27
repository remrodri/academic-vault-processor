# Academic Vault Processor v2

## Objetivo

Desarrollar un CLI Tool en Python 3.11+ llamado **academic-vault-processor** capaz de transformar material académico sin procesar (videos, audios, PDFs, imágenes, Excel, CSV y texto) en una base de conocimiento estructurada para Obsidian.

### Objetivos del sistema

- Procesar material académico.
- Extraer conocimiento.
- Detectar conceptos.
- Evitar duplicados.
- Construir una red de conocimiento enlazada.
- Generar notas Markdown con YAML frontmatter.
- Mantener trazabilidad hacia las fuentes originales.
- Garantizar idempotencia.

---

# Principios Arquitectónicos

## 1. Separación de Responsabilidades

```text
Fuente
 ↓
Processor
 ↓
ProcessedContent
 ↓
AI Analysis
 ↓
Knowledge Graph
 ↓
Vault Builder
 ↓
Obsidian Vault
```

Cada capa debe tener una única responsabilidad.

## 2. Extensibilidad

Agregar nuevos formatos no debe requerir modificar lógica existente.

Formatos futuros previstos:

- YouTube
- HTML
- Notion Export
- EPUB
- Word
- PowerPoint

Patrones utilizados:

- Strategy
- Registry

## 3. Knowledge First

El objetivo **no es almacenar archivos**.

El objetivo es generar conocimiento reutilizable.

Todo archivo debe transformarse en:

- Materias
- Clases
- Conceptos
- Actividades
- Evaluaciones
- Flashcards
- Bibliografía
- Recursos

---

# Stack Obligatorio

```text
Python 3.11+
uv
Typer
pydantic-settings
loguru
jinja2
openai-whisper
ollama
pymupdf
pandas
openpyxl
pytest
pytest-cov
ruff
mypy --strict
```

---

# Estructura del Proyecto

```text
academic-vault-processor/
├── pyproject.toml
├── README.md
├── ARCHITECTURE.md
├── .env.example
├── .gitignore
├── src/
│   ├── main.py
│   ├── config.py
│   ├── models/
│   ├── processors/
│   ├── services/
│   ├── builders/
│   ├── pipelines/
│   ├── templates/
│   └── utils/
└── tests/
```

---

# Dominio Académico

## Objetos soportados

- Materia
- Clase
- Concepto
- Actividad
- Tarea
- Caso
- Ejercicio
- Cuestionario
- Examen
- Flashcard
- Recurso
- Bibliografía
- Investigación
- Proyecto Final
- Fuente

---

# Modelos

## NoteType

```text
materia
clase
concepto
actividad
cuestionario
examen
flashcard
recurso
bibliografia
investigacion
proyecto_final
```

## NoteStatus

```text
pendiente
en_progreso
revisado
listo_para_estudiar
debil
consolidado
evaluado
entregado
archivado
```

## ProcessedContent

```python
source_path: str
source_type: str
raw_text: str
metadata: dict[str, Any]
```

## AcademicAnalysis

```python
summary
concepts
activities
questions
bibliography
resources
related_concepts
weak_topics
suggested_subject
suggested_tags
```

---

# Processor Registry

No utilizar múltiples bloques `if/elif` para determinar el procesador.

Implementar un `ProcessorRegistry` centralizado.

---

# Whisper

## Configuración

```env
WHISPER_MODEL=small
```

## Salidas

- transcript.txt
- transcript.vtt

## Metadatos

- Duración
- Idioma
- Archivo fuente

## Justificación de no usar async

Whisper es una tarea CPU/GPU-bound.

La mayor parte del tiempo se consume en inferencia local y no en I/O.

---

# Ollama

```env
OLLAMA_URL=http://localhost:11434
OLLAMA_MODEL=llama3:8b
```

Endpoint:

```text
POST /api/generate
```

---

# Academic System Prompt

Archivo:

```text
templates/prompts/academic_agent.md
```

Las reglas académicas derivadas de AGENTS.md deben vivir aquí y no dentro del código Python.

---

# Respuesta Esperada de Ollama

```json
{
  "summary": [],
  "concepts": [],
  "activities": [],
  "questions": [],
  "bibliography": [],
  "resources": [],
  "related_concepts": [],
  "weak_topics": [],
  "suggested_subject": "",
  "suggested_tags": []
}
```

---

# Reintentos

Backoff exponencial:

1. 1 segundo
2. 2 segundos
3. 4 segundos

Máximo: 3 intentos.

---

# Deduplicación

## Adjuntos

```text
MD5(content)
```

## Conceptos

```python
SequenceMatcher ratio > 0.85
```

Si existe una nota similar:

- Crear enlace cruzado.
- No crear duplicado.

---

# Índice de Procesamiento

Archivo:

```text
.vault_index.json
```

Objetivos:

- Idempotencia.
- Trazabilidad.

---

# Knowledge Graph

Todas las notas deben generar enlaces Obsidian.

```markdown
[[Gestion de Riesgos]]
[[ISO 31010]]
[[Matriz de Riesgos]]
```

Mantener también relaciones explícitas mediante un modelo `Relation`.

---

# CLI

```bash
academic-vault init
```

```bash
academic-vault process --input ./material --output ./vault --materia "Gestion de Riesgos"
```

```bash
academic-vault transcribe --video clase1.mp4
```

```bash
academic-vault ingest --file documento.pdf --tipo pdf --materia "Gestion de Riesgos"
```

```bash
academic-vault status
```

```bash
academic-vault config --set whisper_model small
```

---

# Testing

Cobertura mínima:

```text
70%
```

Pruebas obligatorias:

- Hashing
- Deduplicación
- Frontmatter
- Parsing de Ollama
- Extracción PDF
- Registry
- Vault Builder

Whisper y Ollama deben mockearse.

---

# Filosofía del Producto

Este proyecto no es un convertidor de archivos.

Es un sistema de construcción de conocimiento académico para Obsidian.

## Métrica de éxito

No:

```text
Cantidad de PDFs procesados
```

Sí:

```text
Capacidad de transformar material académico en conocimiento estructurado, conectado y reutilizable.
```

## Roadmap futuro

- RAG local
- Búsqueda semántica
- Chat sobre el Vault
- Knowledge Graph enriquecido
- Recomendaciones de estudio
