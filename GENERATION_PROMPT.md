# GENERATION_PROMPT.md

# Academic Vault Processor - AI Generation Prompt

## Purpose

Use this document as the PRIMARY instruction set when generating code.

Read the following documents before generating any file:

1. IMPLEMENTATION_BLUEPRINT_ENTERPRISE_Academic_Vault.md
2. APRD_Academic_Vault_Full.md
3. AGENTS.md
4. PROJECT_SPEC_Academic_Vault_v2.md
5. ARCHITECTURE_Academic_Vault.md

These documents together define the source of truth.

---

# Mission

Generate a production-oriented Python project named:

```text
academic-vault-processor
```

The repository must be executable immediately after:

```bash
uv sync
pytest
```

No placeholder implementations.

No empty files.

No TODO-only modules.

---

# Architecture Rules

## Mandatory Patterns

- Strategy Pattern
- Registry Pattern
- Service Layer
- Builder Pattern
- Typed DTOs via Pydantic

Avoid large procedural scripts.

Avoid business logic in CLI commands.

---

# Quality Requirements

Every public function must include:

- Type hints
- Google-style docstrings
- Error handling
- Logging

All code must pass:

```bash
ruff check .
ruff format --check .
mypy src/ --strict
pytest --cov=src tests/
```

---

# Python Guidelines for TypeScript Developers

Use:

```python
Pydantic BaseModel
```

instead of:

```ts
interface
```

Use:

```python
Enum
```

instead of string literals spread throughout code.

Use constructor injection.

Avoid global mutable state.

---

# File Creation Order

Generate files in the following order:

1. pyproject.toml
2. config.py
3. models/
4. processors/
5. services/
6. builders/
7. pipelines/
8. CLI
9. templates
10. tests
11. CI/CD
12. documentation

---

# Mandatory Models

Generate:

```text
Concept
Transcript
Relation
Note
ProcessedContent
AcademicAnalysis
VaultStats
```

Use Pydantic.

---

# Processor Requirements

Implement:

```text
PdfProcessor
ExcelProcessor
TextProcessor
MediaProcessor
ImageProcessor
```

Each processor must implement:

```python
def process(path: Path) -> ProcessedContent
```

---

# Whisper Requirements

Must use local:

```text
openai-whisper
```

Generate:

```text
.txt
.vtt
```

Document why execution is synchronous.

---

# Ollama Requirements

Endpoint:

```text
http://localhost:11434/api/generate
```

Model:

```text
llama3:8b
```

Validation:

```python
AcademicAnalysis.model_validate_json()
```

Retry policy:

```text
1s
2s
4s
```

---

# Obsidian Rules

Must create:

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

# Frontmatter Rules

Every generated note must contain valid YAML.

Required fields:

```yaml
---
tipo:
materia:
estado:
fecha:
fuente:
tags:
---
```

---

# Idempotency Rules

Create:

```text
.vault_index.json
```

Track:

- hashes
- generated notes
- source file

Never process the same content twice.

---

# Deduplication Rules

Attachments:

```python
hashlib.md5()
```

Concepts:

```python
SequenceMatcher().ratio() > 0.85
```

If similar concept exists:

- create relation
- avoid duplicate note

---

# Testing Requirements

Minimum coverage:

```text
70%
```

Required test suites:

- test_whisper_service.py
- test_ollama_service.py
- test_pdf_processor.py
- test_vault_builder.py
- test_deduplicator.py

Mock external dependencies.

---

# CI Requirements

GitHub Actions must execute:

```yaml
ruff check .
ruff format --check .
mypy src/
pytest --cov=src tests/
```

Build fails on any violation.

---

# Definition of Done

A feature is complete only if:

- implemented
- documented
- tested
- typed
- linted
- logged
- covered by CI

---

# Final Instruction

Do not simplify the architecture.

Do not replace models with dictionaries.

Do not remove typing.

Do not bypass validation.

Prefer maintainability, correctness and extensibility over short code.
