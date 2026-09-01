# Academic Vault Processor V1 - Plan de implementación

## Objetivo

Entregar primero un flujo vertical para una sesión MP4 real. Cada hito debe dejar una capacidad ejecutable y probada; los formatos no relacionados con sesiones quedan pospuestos.

## Hito 1: entorno y esqueleto

Entregables:

- `pyproject.toml` para Python 3.12 y uv.
- Estructura `src/academic_vault/` y `tests/`.
- Ruff, mypy estricto, pytest y cobertura.
- Configuración por variables de entorno.
- CLI Typer con `process`, `resume` y `status`.

Finalizado cuando `uv sync`, lint, tipos y pruebas funcionan.

## Hito 2: contratos y trabajo persistente

Entregables:

- `SessionInput`, `JobManifest` y estados.
- `NormalizedTranscript`, `SessionChunk` y material docente.
- Contratos parciales y finales de `AcademicAnalysis`.
- Validación de `session.yaml`.
- Persistencia atómica del manifiesto.

Finalizado cuando un trabajo puede crearse, validarse y reabrirse.

## Hito 3: preparación de audio

Entregables:

- Integración con FFmpeg.
- Conversión de MP4 a mono, 16 kHz.
- Normalización de volumen.
- Manejo de rutas con espacios y errores de FFmpeg.

Finalizado cuando una muestra real produce audio transcribible sin alterar el video.

## Hito 4: Faster-Whisper

Entregables:

- Transcriptor `small` en español.
- CUDA `int8_float16` como primera opción.
- Fallback automático a CPU `int8`.
- VAD.
- JSON crudo, JSON normalizado, TXT y VTT.
- Progreso y estado reanudable.

Finalizado cuando una muestra de 20 a 30 minutos se transcribe en GPU y también mediante el fallback simulado.

## Hito 5: material docente y bloques

Entregables:

- Extracción de texto de PDF con PyMuPDF.
- Asociación aproximada de páginas con bloques.
- Segmentación de 10 a 15 minutos.
- Preservación de orden y segmentos fuente.
- Contexto limitado entre bloques consecutivos.

Finalizado cuando una transcripción larga se divide sin pérdida de texto y conserva contexto de diapositivas.

## Hito 6: analizador Gemini

Entregables:

- Cliente Gemini detrás de un protocolo `AcademicAnalyzer`.
- Flash como modelo base configurable.
- Structured output validado con Pydantic.
- Backoff, jitter, `Retry-After` y reparación de JSON.
- Persistencia de cada análisis parcial.
- Estado `WAITING_FOR_QUOTA`.

Finalizado cuando puede analizarse y reanudarse un conjunto de bloques mockeado y uno real.

## Hito 7: consolidación jerárquica

Entregables:

- Consolidación de bloques en secciones.
- Consolidación de secciones en análisis final.
- Pro opcional con fallback inmediato a Flash.
- Deduplicación de conceptos dentro de la sesión.
- Clasificación de evidencia e incertidumbre.
- Reglas de recapitulación y granularidad.

Finalizado cuando Flash por sí solo produce un `AcademicAnalysis` válido y coherente.

## Hito 8: generación de Obsidian

Entregables:

- Prompt académico derivado de `docs/AGENTS.md`.
- Plantillas de materia, sesión y concepto.
- Frontmatter YAML válido.
- Enlaces Obsidian.
- Escritura atómica en una carpeta de revisión.
- Protección contra sobrescritura manual.

Finalizado cuando el mismo análisis puede regenerar Markdown sin volver a llamar a Gemini.

## Hito 9: finalización segura

Entregables:

- Verificación integral de artefactos.
- Índice de sesiones procesadas.
- Eliminación opcional del MP4 solo en `COMPLETED`.
- Eliminación de audio temporal.
- Resumen de ejecución y errores accionables.

Finalizado cuando ninguna ruta de fallo puede eliminar el video original.

## Hito 10: prueba de aceptación

Secuencia:

1. Procesar una muestra real de 20 a 30 minutos con diapositivas.
2. Revisar precisión, terminología y estructura académica.
3. Ajustar prompt y criterios de conceptos.
4. Procesar una sesión completa de aproximadamente tres horas.
5. Medir tiempo, uso de GPU, número de llamadas y capacidad de reanudación.
6. Aprobar escritura directa al vault.

## Definición de terminado

- La sesión completa se procesa con Flash aunque Pro no tenga cuota.
- La GTX 1650 se utiliza cuando es compatible y CPU funciona como fallback.
- Una interrupción no repite bloques terminados.
- Las diapositivas mejoran vocabulario sin convertirse en contenido hablado.
- Se genera una nota útil por sesión.
- Solo se crean conceptos independientes justificados.
- No se sobrescribe contenido manual.
- El video solo se elimina tras validación completa.
- Ruff, mypy y pytest pasan con cobertura mínima de 70 %.
