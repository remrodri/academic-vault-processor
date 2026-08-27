# IMPLEMENTATION_BLUEPRINT_ENTERPRISE.md

# Academic Vault Processor
## Enterprise Implementation Blueprint

Version: 1.0
Target: Python 3.11+
Audience: Cursor, Claude Code, Copilot, Human Developers

---

# Executive Summary

Academic Vault Processor es una plataforma local de procesamiento académico orientada a Obsidian.

Objetivo:

Transformar fuentes académicas sin estructurar en un knowledge graph navegable mediante notas Markdown, enlaces bidireccionales, metadatos YAML y trazabilidad completa hacia las fuentes originales.

El sistema debe priorizar:

- Calidad del conocimiento.
- Idempotencia.
- Extensibilidad.
- Observabilidad.
- Tipado estricto.

---

# Architectural Principles

## Single Responsibility

Cada módulo debe tener exactamente una responsabilidad.

## Composition over Inheritance

Preferir composición.

## Typed Contracts Everywhere

Toda comunicación entre capas debe realizarse mediante modelos Pydantic.

## Knowledge First

Los archivos son solo fuentes.

El producto final es conocimiento estructurado.

---

# Complete Repository Layout

```text
academic-vault-processor/
├── pyproject.toml
├── README.md
├── ARCHITECTURE.md
├── APRD.md
├── IMPLEMENTATION_BLUEPRINT.md
├── .env.example
├── .gitignore
├── .github/workflows/ci.yml
├── src/
├── tests/
└── docs/
```

---

# Dependency Strategy

## Runtime

```toml
pydantic
pydantic-settings
typer
loguru
jinja2
httpx
pymupdf
pandas
openpyxl
openai-whisper
```

## Development

```toml
pytest
pytest-cov
ruff
mypy
```

---

# Coding Standards

## Mandatory

- Python 3.11+
- mypy --strict
- ruff check
- ruff format
- type hints 100%
- public docstrings

## Maximum Line Length

```text
100
```

## Language Rules

Code:

```text
English
```

Documentation:

```text
Spanish
```

---

# Domain Model

## Core Entities

```text
Subject
Class
Concept
Activity
Quiz
Exam
Flashcard
Research
Resource
Bibliography
Attachment
```

---

# Required Models

## ProcessedContent

```python
class ProcessedContent(BaseModel):
    source_path: str
    source_type: str
    raw_text: str
    metadata: dict[str, Any]
```

## AcademicAnalysis

```python
class AcademicAnalysis(BaseModel):
    summary: list[str]
    concepts: list[Concept]
    activities: list[str]
    questions: list[Question]
    bibliography: list[str]
    resources: list[str]
    related_concepts: list[str]
    weak_topics: list[str]
    suggested_subject: str
    suggested_tags: list[str]
```

## Relation

```python
class Relation(BaseModel):
    source: str
    target: str
    relation_type: str
```

---

# Processor Architecture

## Contract

```python
class Processor(Protocol):
    def process(self, file_path: Path) -> ProcessedContent:
        ...
```

## Registry

```python
registry.register('.pdf', PdfProcessor())
registry.register('.csv', ExcelProcessor())
registry.register('.xlsx', ExcelProcessor())
registry.register('.mp4', MediaProcessor())
```

---

# Whisper Module

## Responsibilities

- Load model.
- Transcribe media.
- Export TXT.
- Export VTT.
- Return Transcript.

## Non Functional Decisions

No async.

Reason:

Whisper is CPU/GPU bound.

---

# Ollama Module

## Endpoint

```text
POST http://localhost:11434/api/generate
```

## Validation Strategy

```python
AcademicAnalysis.model_validate_json()
```

## Retry Strategy

```text
1 second
2 seconds
4 seconds
```

Maximum:

```text
3 attempts
```

---

# Prompt Strategy

System prompt must be externalized.

```text
templates/prompts/academic_agent.md
```

Business behavior must not be hardcoded.

---

# Vault Strategy

## Folder Structure

```text
00_Inbox/
01_Materias/
02_Conceptos/
03_Actividades/
04_Investigaciones/
05_Proyecto_Final/
06_Evaluaciones/
07_Flashcards/
08_Recursos/
09_Bibliografia/
90_Plantillas/
99_Adjuntos/
```

---

# Knowledge Graph Rules

Every generated note must contain:

```markdown
## Relacionado

- [[Concepto]]
- [[Materia]]
```

Relations must also be stored programmatically.

---

# Frontmatter Standard

```yaml
---
tipo:
materia:
estado:
fecha:
fecha_limite:
puntaje:
fuente:
tags:
---
```

---

# Idempotency Standard

## Index File

```text
.vault_index.json
```

## Stored Data

```json
{
  "hash": {
    "processed": true,
    "notes": []
  }
}
```

No file should be processed twice.

---

# Deduplication Standard

## Attachments

```python
hashlib.md5()
```

## Concepts

```python
SequenceMatcher().ratio() > 0.85
```

Create relation instead of duplicate.

---

# Logging Blueprint

Framework:

```text
loguru
```

Required Events:

- ingestion_start
- ingestion_completed
- whisper_start
- whisper_completed
- ollama_request
- ollama_retry
- note_created
- duplicate_detected
- error

---

# Error Handling Matrix

| Scenario | Action |
|-----------|----------|
| Corrupted PDF | Warning |
| Invalid JSON | Retry |
| Missing permissions | Abort |
| Missing file | Abort |
| Ollama unavailable | Retry |

---

# CLI Specification

```bash
academic-vault init
```

```bash
academic-vault process
```

```bash
academic-vault ingest
```

```bash
academic-vault transcribe
```

```bash
academic-vault status
```

```bash
academic-vault config
```

---

# Testing Strategy

## Target Coverage

```text
>= 70%
```

## Unit Tests

- hashing
- frontmatter
- registry
- parser
- deduplicator

## Integration Tests

- PDF -> Vault
- TXT -> Vault
- Transcript -> Vault

## Mocked Components

- Whisper
- Ollama

---

# CI Pipeline

```yaml
ruff check .
ruff format --check .
mypy src/
pytest --cov=src tests/
```

Pipeline fails on any error.

---

# Developer Guide for JS/TS Engineers

| TypeScript | Python |
|------------|---------|
| interface | Pydantic Model |
| zod | Pydantic |
| commander | Typer |
| DTO | BaseModel |
| service | service |
| dependency injection | constructor injection |

---

# Definition Of Done

Feature is complete when:

- implemented
- typed
- tested
- documented
- logged
- linted
- covered by CI

---

# Future Versions

## V2

- OCR
- DOCX
- PPTX
- Embeddings
- Semantic Search

## V3

- RAG
- Academic Copilot
- Study Recommendations
- Learning Analytics
