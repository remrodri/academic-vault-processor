# Academic Vault Processor V1

## Objetivo

Construir una herramienta CLI en Python 3.12 que transforme una sesión académica grabada en MP4 en conocimiento organizado para un vault de Obsidian existente.

La V1 procesa exclusivamente sesiones en español, con material docente opcional, y prioriza un equilibrio entre precisión y velocidad en un equipo con 16 GB de RAM, CPU Intel i5 de 12.ª generación y GPU GTX 1650 de 4 GB.

## Flujo obligatorio

```text
MP4 + session.yaml + diapositivas opcionales
                    ↓
          preparación de audio
                    ↓
       Faster-Whisper local small
                    ↓
         transcripción normalizada
                    ↓
       bloques de 10 a 15 minutos
                    ↓
       análisis con Gemini Flash
                    ↓
       consolidación jerárquica
                    ↓
         AcademicAnalysis válido
                    ↓
       notas Markdown para Obsidian
```

## Alcance funcional

La V1 debe:

- Aceptar un único MP4 consolidado por sesión.
- Leer metadatos desde `session.yaml`.
- Aceptar diapositivas PDF opcionales.
- Extraer audio mono a 16 kHz con FFmpeg.
- Normalizar volumen sin aplicar reducción agresiva de ruido por defecto.
- Transcribir localmente con Faster-Whisper.
- Intentar GPU y continuar automáticamente en CPU si CUDA falla.
- Conservar JSON crudo, JSON normalizado, TXT y VTT.
- Dividir transcripciones de aproximadamente tres horas en bloques reanudables.
- Analizar bloques y consolidarlos con Gemini.
- Funcionar completamente con Gemini Flash.
- Utilizar Gemini Pro solo como mejora opcional.
- Validar respuestas mediante modelos Pydantic.
- Generar una nota de sesión y conceptos independientes justificados.
- Actualizar el índice de la materia sin sobrescribir contenido manual.
- Reanudar trabajos interrumpidos o detenidos por cuota.
- Eliminar el video solo después de completar y validar todo el trabajo.

## Fuera de alcance

- Procesamiento general de PDF, Excel, CSV, imágenes, Word o EPUB.
- Diarización completa de hablantes.
- Extracción automática de fechas límite.
- RAG, chat, embeddings y búsqueda semántica.
- BERTopic.
- Planificador de estudio.
- Modificación destructiva de notas existentes.

## Entrada

```text
input/<materia>/<sesion>/
├── session.yaml
├── clase.mp4
└── diapositivas.pdf       # opcional
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

Campos obligatorios: `course`, `session_number`, `date`, `language` y `video`.

## Tratamiento del material docente

Las diapositivas:

- Aportan títulos, estructura, siglas y vocabulario.
- Ayudan a corregir errores probables de transcripción.
- No demuestran por sí solas que un tema fue explicado.
- Deben distinguirse del contenido hablado.

La prioridad de evidencia es:

1. Explicación verbal del docente.
2. Material docente utilizado en la sesión.
3. Inferencia del modelo marcada explícitamente.

No se incorpora conocimiento general externo en la V1.

## Transcripción

Configuración inicial:

```text
model: small
language: es
vad_filter: true
GPU compute_type: int8_float16
CPU compute_type: int8
```

Salidas:

```text
transcription.raw.json
transcription.json
transcription.txt
transcription.vtt
```

Contrato normalizado:

```python
class TranscriptSegment(BaseModel):
    id: int
    start: float
    end: float
    text: str

class NormalizedTranscript(BaseModel):
    source: str
    language: str
    duration_seconds: float
    segments: list[TranscriptSegment]
```

## Segmentación

La transcripción se agrupa en bloques consecutivos de 10 a 15 minutos, respetando oraciones, silencios y cambios claros de tema. Los timestamps se conservan internamente, pero no se muestran de forma general en las notas.

```python
class SessionChunk(BaseModel):
    id: int
    start: float
    end: float
    text: str
    source_segment_ids: list[int]
    teaching_material: list[str]
```

## Análisis con Gemini

### Política de modelos

- Gemini Flash es el modelo base para bloques y consolidación.
- Gemini Pro puede utilizarse para la consolidación final si está habilitado y tiene cuota.
- La indisponibilidad de Pro activa fallback inmediato a Flash.
- La indisponibilidad temporal de Flash guarda el trabajo como `WAITING_FOR_QUOTA`.
- Los identificadores de modelos son configuración, no constantes de dominio.

### Estrategia jerárquica

```text
bloques → secciones → análisis final
```

Cada resultado se persiste antes de iniciar la siguiente solicitud.

### Contenido extraído

- Resumen ejecutivo.
- Temas tratados.
- Explicaciones y definiciones.
- Ejemplos y experiencias del docente.
- Posibles preguntas de examen.
- Actividades mencionadas, sin interpretar fechas relativas.
- Bibliografía y recursos mencionados.
- Recapitulación de sesiones anteriores.
- Dudas, contradicciones e incertidumbres.
- Conceptos candidatos y relaciones.

### Confiabilidad

El análisis debe distinguir:

- Contenido respaldado por la transcripción.
- Contenido presente solo en material docente.
- Inferencia probable.
- Información incierta.

## Granularidad de notas

Siempre se genera una nota de sesión.

Un concepto obtiene nota propia solo cuando cumple al menos dos criterios:

- Fue definido o explicado.
- Ocupa una parte sustancial de la sesión.
- Es un tema formal de las diapositivas.
- Aparece en más de una sesión.
- Tiene ejemplos o aplicaciones.
- Es necesario para comprender otros conceptos.
- Es relevante para estudiar la materia.

Una mención aislada permanece en la nota de sesión. Las experiencias del docente permanecen en la sesión, salvo que formen un caso de estudio desarrollado.

## Reglas de Obsidian

La política académica procede de `docs/AGENTS.md` y se materializa en `src/templates/prompts/academic_agent.md`.

Salidas mínimas:

```text
01_Materias/<Materia>/<Materia>.md
01_Materias/<Materia>/Clases/Sesion NN - <Tema>.md
02_Conceptos/<Concepto>.md
99_Adjuntos/Transcripciones/<Sesion>/transcription.json
99_Adjuntos/Transcripciones/<Sesion>/transcription.vtt
```

La nota de sesión contiene:

- Resumen ejecutivo.
- Temas tratados.
- Conceptos.
- Ejemplos y experiencias del docente.
- Posibles preguntas de examen.
- Actividades mencionadas.
- Recapitulación anterior cuando corresponda.
- Dudas.
- Enlaces relacionados.

Gemini produce datos; `VaultBuilder` produce Markdown mediante plantillas.

## Estados y reanudación

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

Los errores `429` respetan `Retry-After`. Otros errores temporales usan backoff con jitter. Un JSON inválido admite una solicitud de reparación; si continúa inválido, el bloque falla sin perder los anteriores.

## Eliminación segura

Con `DELETE_SOURCE_VIDEO_AFTER_SUCCESS=true`, el video solo se elimina cuando:

- Todas las salidas de transcripción son válidas.
- Todos los bloques requeridos fueron analizados.
- Existe un `AcademicAnalysis` final válido.
- Las notas fueron escritas correctamente.
- El índice fue actualizado.
- El estado es `COMPLETED`.

El audio temporal se elimina después del mismo control. Ante cualquier fallo, el video permanece intacto.

## CLI

```bash
academic-vault process <session-directory>
academic-vault resume <session-id>
academic-vault status [session-id]
```

## Calidad y pruebas

Objetivo de cobertura: 70 % como mínimo.

Pruebas obligatorias:

- Validación de `session.yaml`.
- Preparación de audio.
- Selección GPU y fallback CPU.
- Normalización de transcripción.
- Segmentación sin pérdida ni reordenamiento.
- Extracción de diapositivas.
- Parsing y reparación de respuestas Gemini.
- Fallback Pro a Flash.
- Pausa y reanudación por cuota.
- Granularidad de conceptos.
- Generación de frontmatter y enlaces.
- Protección contra sobrescritura.
- Condiciones para eliminar el video.

Whisper, Gemini y FFmpeg se mockean en pruebas unitarias. La prueba de aceptación utiliza una clase real de 20 a 30 minutos antes de procesar una sesión completa.

## Criterio de éxito

La V1 está terminada cuando una sesión real de aproximadamente tres horas puede procesarse de extremo a extremo, reanudarse después de una interrupción y generar una nota de sesión útil, conceptos no excesivos y enlaces coherentes sin inventar contenido.
