# Arquitectura

## Flujo principal

```text
SessionInput
    ↓
AudioPreprocessor
    ↓
Transcriber
    ↓
NormalizedTranscript
    ↓
SessionChunker ← TeachingMaterial
    ↓
AcademicAnalyzer
    ↓
AcademicAnalysis
    ↓
VaultBuilder
    ↓
Obsidian Vault
```

## Decisiones

### Una sesión es la unidad de procesamiento

Cada trabajo recibe un MP4 consolidado, metadatos y material docente opcional. Un recuerdo de la sesión anterior se conserva como recapitulación dentro de la sesión actual.

### Faster-Whisper local

La transcripción se ejecuta localmente con el modelo `small`. Se intenta CUDA con `int8_float16` en la GTX 1650 y se vuelve automáticamente a CPU con `int8` si la GPU no está disponible o falla.

### Gemini Flash como base

Todo el flujo debe completarse con Gemini Flash. Gemini Pro es una mejora oportunista para consolidación y su falta de cuota activa el fallback inmediato a Flash.

### Análisis jerárquico

Una sesión de aproximadamente tres horas se divide en bloques de 10 a 15 minutos. Los bloques se analizan, se agrupan en secciones y finalmente se consolidan. Cada etapa se persiste para poder reanudar sin repetir trabajo.

### Material docente como contexto

Las diapositivas aportan vocabulario y estructura. La transcripción determina lo que realmente se explicó. El análisis distingue contenido hablado, contenido solo presente en diapositivas e inferencias.

### Contratos tipados

Pydantic valida los límites entre etapas. Gemini nunca escribe directamente en el vault; produce `AcademicAnalysis`, y el `VaultBuilder` genera Markdown mediante plantillas.

### Escritura y eliminación seguras

La primera validación se genera fuera del vault. El video solo puede eliminarse cuando transcripción, análisis, notas e índice estén validados y el trabajo alcance `COMPLETED`.

## Estados persistentes

```text
PENDING
AUDIO_READY
TRANSCRIBING
TRANSCRIBED
ANALYZING_CHUNKS
CONSOLIDATING
GENERATING_VAULT
WAITING_FOR_QUOTA
COMPLETED
FAILED
```

## Dependencias principales

- Python 3.12 y uv.
- FFmpeg.
- faster-whisper y CTranslate2.
- google-genai.
- pydantic y pydantic-settings.
- semantic-text-splitter.
- PyMuPDF y, posteriormente, python-pptx.
- Jinja2, PyYAML, Typer, loguru, tenacity y orjson.
