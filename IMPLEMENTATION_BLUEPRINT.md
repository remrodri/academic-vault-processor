# Academic Vault Processor V1 - Blueprint

## Fuente de verdad

Antes de implementar, leer en este orden:

1. `CONTEXT.md`
2. `docs/PROJECT_SPEC.md`
3. `docs/IMPLEMENTATION_PLAN_V1.md`
4. `ARCHITECTURE.md`
5. `docs/AGENTS.md`

## Estructura prevista

```text
src/academic_vault/
├── cli.py
├── config.py
├── models/
│   ├── session.py
│   ├── job.py
│   ├── transcript.py
│   ├── teaching_material.py
│   └── academic_analysis.py
├── audio/
│   └── ffmpeg_preprocessor.py
├── transcription/
│   ├── protocol.py
│   └── faster_whisper_transcriber.py
├── materials/
│   └── pdf_extractor.py
├── chunking/
│   └── session_chunker.py
├── analysis/
│   ├── protocol.py
│   ├── gemini_analyzer.py
│   └── consolidator.py
├── jobs/
│   ├── repository.py
│   └── runner.py
├── vault/
│   ├── builder.py
│   ├── naming.py
│   └── writer.py
└── templates/
    ├── prompts/academic_agent.md
    └── notes/
```

## Interfaces profundas

```python
class Transcriber(Protocol):
    def transcribe(self, audio_path: Path) -> NormalizedTranscript: ...

class AcademicAnalyzer(Protocol):
    def analyze_chunk(self, chunk: SessionChunk) -> ChunkAnalysis: ...
    def consolidate(self, analyses: list[ChunkAnalysis]) -> AcademicAnalysis: ...

class VaultBuilder(Protocol):
    def build(self, analysis: AcademicAnalysis) -> list[GeneratedNote]: ...
```

Los detalles de CUDA, Gemini y Jinja2 no deben filtrarse al orquestador.

## Configuración inicial

```env
GEMINI_API_KEY=
WHISPER_MODEL=small
WHISPER_LANGUAGE=es
WHISPER_GPU_COMPUTE_TYPE=int8_float16
WHISPER_CPU_COMPUTE_TYPE=int8
GEMINI_CHUNK_MODEL=<flash-model-id>
GEMINI_CONSOLIDATION_MODEL=<flash-model-id>
GEMINI_PRO_MODEL=<optional-pro-model-id>
USE_PRO_WHEN_AVAILABLE=false
DELETE_SOURCE_VIDEO_AFTER_SUCCESS=false
```

La aplicación carga estas variables desde `.env` con `pydantic-settings`. Los nombres actuales de modelos Gemini se configuran fuera del código.

`CONTEXT7_API_KEY` no depende de la aplicación Python: debe existir en el entorno del proceso que inicia OpenCode. `opencode.json` la referencia mediante `{env:CONTEXT7_API_KEY}`. Ninguna clave real se guarda en archivos versionados.

## Reglas de implementación

- Código y nombres técnicos en inglés; documentación y notas en español.
- Python 3.12, tipado estricto y funciones públicas documentadas.
- Sin lógica de negocio dentro de comandos Typer.
- Escrituras atómicas para manifiestos y notas.
- Cada etapa debe poder repetirse de forma segura.
- Nunca borrar el video desde el transcriptor.
- Nunca escribir Markdown directamente desde una respuesta de Gemini.
- Nunca depender de Pro para completar un trabajo.
- No añadir procesadores de formatos fuera del alcance V1.

## Artefactos por sesión

```text
work/<session-id>/
├── job.json
├── audio.normalized.wav
├── transcription.raw.json
├── transcription.json
├── transcription.txt
├── transcription.vtt
├── teaching-material.json
├── chunks/
├── analyses/
│   ├── chunks/
│   └── sections/
├── academic-analysis.json
└── generated-notes/
```

## Calidad

```bash
ruff check .
ruff format --check .
mypy src/ --strict
pytest --cov=src --cov-fail-under=70
```

Whisper, Gemini y FFmpeg deben tener adaptadores mockeables. La eliminación del video requiere pruebas específicas de todas las condiciones negativas.
