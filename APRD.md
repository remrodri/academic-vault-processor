# APRD - Academic Vault Processor

# 1. Product Vision

Academic Vault Processor es una herramienta CLI local orientada a transformar material académico sin procesar en una base de conocimiento estructurada para Obsidian.

El objetivo principal no es almacenar archivos sino convertir información dispersa en conocimiento reutilizable, enlazado y orientado al estudio.

---

# 2. Objetivos del Producto

## Objetivos Funcionales

- Procesar videos, audios, PDFs, Excel, CSV, imágenes y texto.
- Generar transcripciones usando Whisper local.
- Analizar contenido utilizando Ollama y llama3:8b.
- Detectar conceptos clave.
- Detectar actividades y evaluaciones.
- Generar notas Markdown.
- Generar enlaces bidireccionales Obsidian.
- Crear índices por materia.
- Evitar duplicados.
- Garantizar idempotencia.

## Objetivos No Funcionales

- Python 3.11+
- Mypy strict
- Ruff
- Cobertura >= 70%
- Arquitectura extensible
- Logging centralizado

---

# 3. Arquitectura General

```text
Input File
    |
    v
Processor Registry
    |
    v
Processor
    |
    v
ProcessedContent
    |
    v
Ollama Analysis
    |
    v
AcademicAnalysis
    |
    v
Vault Builder
    |
    v
Obsidian Vault
```

---

# 4. ADRs

## ADR-001 Typer

Decisión:
Utilizar Typer como framework principal para CLI.

Motivación:
- Type hints nativos.
- Excelente DX.
- Integración natural con Python moderno.

## ADR-002 Pydantic

Decisión:
Utilizar Pydantic para todos los contratos.

Equivalencia TypeScript:

```ts
interface User {}
```

Python:

```python
class User(BaseModel):
    ...
```

## ADR-003 Registry Pattern

No utilizar múltiples bloques if/elif.

Implementar ProcessorRegistry.

Beneficio:
Soporte para nuevos formatos sin modificar código existente.

## ADR-004 Whisper Sync

Whisper será síncrono.

Justificación:
La inferencia es CPU/GPU bound.

Async no aporta beneficios relevantes.

---

# 5. Estructura del Proyecto

```text
src/
├── models/
├── processors/
├── services/
├── builders/
├── pipelines/
├── templates/
└── utils/
```

---

# 6. Modelos de Dominio

## NoteType

- materia
- clase
- concepto
- actividad
- cuestionario
- examen
- flashcard
- recurso
- bibliografia
- investigacion

## NoteStatus

- pendiente
- en_progreso
- revisado
- listo_para_estudiar
- debil
- consolidado
- evaluado
- entregado
- archivado

---

# 7. Contratos

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

---

# 8. Processor Registry

```python
registry.register('.pdf', PdfProcessor())
registry.register('.csv', ExcelProcessor())
registry.register('.xlsx', ExcelProcessor())
registry.register('.mp4', MediaProcessor())
```

---

# 9. Flujo Whisper

Entrada:

```text
video.mp4
```

Salida:

```text
transcript.txt
transcript.vtt
```

Metadatos:

- idioma
- duración
- archivo original

---

# 10. Flujo Ollama

Endpoint:

```text
POST http://localhost:11434/api/generate
```

Modelo:

```text
llama3:8b
```

Validación:

```python
AcademicAnalysis.model_validate_json()
```

Backoff:

- 1 segundo
- 2 segundos
- 4 segundos

---

# 11. Obsidian Vault

## Estructura

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

# 12. Frontmatter

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

# 13. Knowledge Graph

Relaciones esperadas:

```text
Materia -> Clase
Materia -> Actividad
Clase -> Concepto
Actividad -> Concepto
Cuestionario -> Concepto
Bibliografia -> Concepto
```

Links:

```markdown
[[Concepto]]
[[Materia]]
```

---

# 14. Idempotencia

Archivo:

```text
.vault_index.json
```

Responsabilidades:

- tracking de hashes
- tracking de notas generadas
- evitar reprocesamiento

---

# 15. Deduplicación

Hash archivo:

```python
hashlib.md5()
```

Conceptos:

```python
SequenceMatcher().ratio() > 0.85
```

---

# 16. Casos de Uso

## CU-001 Procesar PDF

Entrada:

```text
archivo.pdf
```

Salida:

- notas de clase
- conceptos
- materia

## CU-002 Procesar Video

Entrada:

```text
clase.mp4
```

Salida:

- transcripción
- conceptos
- actividades

## CU-003 Reprocesar

Resultado:

No duplicar información.

---

# 17. Edge Cases

## PDF escaneado

Acción:

Registrar warning.

## Ollama caído

Acción:

Aplicar retry policy.

## JSON inválido

Acción:

Reintentar.

## Sin permisos de escritura

Acción:

Abortar con error controlado.

---

# 18. Testing Strategy

Unit Tests:

- hashing
- registry
- deduplicación
- parsing json
- frontmatter

Integration Tests:

- PDF -> Vault
- TXT -> Vault

Mockear:

- Whisper
- Ollama

---

# 19. CI/CD

```yaml
ruff check .
mypy src/
pytest --cov=src tests/
```

---

# 20. Roadmap

## V1

- Whisper
- Ollama
- Vault Builder

## V1.5

- OCR
- DOCX
- PPTX

## V2

- Embeddings locales
- Semantic Search
- RAG local

## V3

- Academic Chat
- Study Planner
- Recommendations

---

# 21. Success Criteria

El éxito del proyecto se medirá por:

- Calidad de las notas generadas.
- Reutilización del conocimiento.
- Ausencia de duplicados.
- Correcta generación de enlaces.
- Facilidad de estudio dentro de Obsidian.
