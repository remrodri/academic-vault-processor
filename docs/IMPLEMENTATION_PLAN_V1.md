# Academic Vault Processor V1 - Final Implementation Plan

## Objective

Primary workflow:

```text
Video
↓
Whisper
↓
Transcript
↓
Ollama
↓
Concept Extraction
↓
Knowledge Graph
↓
Obsidian Vault
```

The system must transform raw academic recordings into structured and linked knowledge stored inside an Obsidian Vault.

---

# Repository Structure

```text
academic-vault-processor/

├── README.md
├── ARCHITECTURE.md
├── APRD.md
├── IMPLEMENTATION_BLUEPRINT.md
├── GENERATION_PROMPT.md

├── pyproject.toml
├── .env.example
├── .gitignore
├── .pre-commit-config.yaml

├── docs/
│   ├── AGENTS.md
│   ├── PROJECT_SPEC.md
│   └── IMPLEMENTATION_PLAN_V1.md

├── .github/
│   └── workflows/
│       └── ci.yml

├── src/
│   ├── __init__.py
│   ├── main.py
│   ├── config.py
│
│   ├── models/
│   │   ├── __init__.py
│   │   ├── enums.py
│   │   ├── processed_content.py
│   │   ├── transcript.py
│   │   ├── concept.py
│   │   ├── relation.py
│   │   ├── note.py
│   │   └── academic_analysis.py
│
│   ├── protocols/
│   │   ├── __init__.py
│   │   └── processor.py
│
│   ├── registry/
│   │   ├── __init__.py
│   │   └── processor_registry.py
│
│   ├── processors/
│   │   ├── __init__.py
│   │   ├── base.py
│   │   ├── media_processor.py
│   │   ├── pdf_processor.py
│   │   ├── text_processor.py
│   │   └── excel_processor.py
│
│   ├── services/
│   │   ├── __init__.py
│   │   ├── whisper_service.py
│   │   ├── ollama_service.py
│   │   └── deduplication_service.py
│
│   ├── builders/
│   │   ├── __init__.py
│   │   ├── markdown_builder.py
│   │   └── vault_builder.py
│
│   ├── pipelines/
│   │   ├── __init__.py
│   │   └── ingestion_pipeline.py
│
│   ├── templates/
│   │   ├── prompts/
│   │   │   └── academic_agent.md
│   │   └── notes/
│   │       ├── clase.tpl
│   │       ├── concepto.tpl
│   │       └── materia.tpl
│
│   └── utils/
│       ├── __init__.py
│       ├── hashing.py
│       ├── similarity.py
│       ├── frontmatter.py
│       └── vault_initializer.py
│
├── tests/
│   ├── conftest.py
│   ├── models/
│   ├── processors/
│   ├── services/
│   ├── builders/
│   ├── pipelines/
│   └── integration/
│
└── 99_Adjuntos/
    └── .vault_index.json
```

---

# Milestone 1

## Goal

Project foundation.

## Deliverables

- pyproject.toml
- .gitignore
- .env.example
- CI pipeline
- pre-commit hooks
- project structure

## Definition of Done

- uv sync works
- pytest runs
- ruff passes
- mypy passes

---

# Milestone 2

## Goal

Domain model layer.

## Deliverables

- ProcessedContent
- Transcript
- Concept
- Relation
- Note
- AcademicAnalysis

## Definition of Done

- Pydantic models
- Validation tests
- Type hints
- 70% coverage

---

# Milestone 3

## Goal

Processor protocol and registry.

## Deliverables

- Processor Protocol
- ProcessorRegistry

Supported types:

- mp4
- mov
- wav
- mp3
- pdf
- txt
- md
- csv
- xlsx

---

# Milestone 4

## Goal

Services layer.

## Deliverables

### Whisper Service

Input:

```text
video.mp4
```

Output:

```text
transcript.txt
transcript.vtt
```

### Ollama Service

Input:

```text
transcript
```

Output:

```text
AcademicAnalysis
```

### Deduplication Service

- MD5
- SequenceMatcher

---

# Milestone 5

## Goal

Processors.

## Deliverables

- MediaProcessor
- PdfProcessor
- TextProcessor
- ExcelProcessor

All processors must return:

```python
ProcessedContent
```

---

# Milestone 6

## Goal

Markdown and Vault generation.

## Deliverables

### Matter Note

```markdown
# Gestion de Riesgos
```

### Concept Notes

```markdown
# ISO 31010
```

### Class Notes

```markdown
# Clase 1
```

---

# Milestone 7

## Goal

Knowledge Graph.

## Requirements

Every concept note must generate:

```markdown
## Relacionado

- [[Concepto A]]
- [[Concepto B]]
```

Also store:

```python
Relation
```

objects.

---

# Milestone 8

## Goal

Ingestion Pipeline.

Flow:

```text
Video
↓
Whisper
↓
Transcript
↓
Ollama
↓
Concepts
↓
Markdown
↓
Vault
```

---

# Milestone 9

## Goal

CLI.

Commands:

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

# Milestone 10

## Goal

Documentation and finalization.

Requirements:

- README complete
- Architecture docs updated
- CI passing
- Coverage >= 70%

---

# V1 Definition of Done

The project is considered complete when:

✅ Video transcription works

✅ Ollama analysis works

✅ Concepts are extracted

✅ Concept notes are generated

✅ Obsidian links are generated

✅ Vault structure is generated

✅ Deduplication works

✅ Tests pass

✅ Ruff passes

✅ Mypy strict passes

✅ CI pipeline passes

✅ Documentation is updated