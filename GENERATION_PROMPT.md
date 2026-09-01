# Academic Vault Processor V1 - Instrucciones de generación

## Misión

Implementar el flujo definido en `docs/PROJECT_SPEC.md` siguiendo exactamente los hitos de `docs/IMPLEMENTATION_PLAN_V1.md`.

Antes de generar código, leer:

1. `CONTEXT.md`
2. `docs/PROJECT_SPEC.md`
3. `docs/IMPLEMENTATION_PLAN_V1.md`
4. `ARCHITECTURE.md`
5. `docs/AGENTS.md`
6. `IMPLEMENTATION_BLUEPRINT.md`

## Restricciones

- No reintroducir Ollama.
- No implementar formatos fuera de MP4 y diapositivas PDF opcionales.
- No añadir diarización en la V1.
- Gemini Flash debe completar todo el flujo.
- Gemini Pro es opcional y configurable.
- El procesamiento debe reanudarse sin repetir etapas completadas.
- Gemini devuelve datos estructurados; Jinja2 genera Markdown.
- El video solo se elimina cuando el trabajo es `COMPLETED` y todos los artefactos fueron validados.

## Orden de generación

1. Configuración del proyecto.
2. Modelos y estados persistentes.
3. Preparación de audio.
4. Faster-Whisper con fallback GPU/CPU.
5. Extracción de diapositivas.
6. Segmentación.
7. Cliente Gemini y análisis por bloques.
8. Consolidación jerárquica.
9. Generación de Obsidian.
10. Finalización y eliminación segura.
11. Prueba de aceptación.

No crear módulos vacíos, TODOs como implementación ni abstracciones para funcionalidades pospuestas.

## Calidad obligatoria

- Python 3.12.
- Ruff y formato con línea máxima de 100 caracteres.
- Mypy estricto.
- Pydantic en límites del sistema.
- Docstrings en APIs públicas.
- Inyección de adaptadores externos.
- Cobertura mínima de 70 %.
- Pruebas sin llamadas reales a Gemini salvo aceptación manual.

## Definición de terminado

Una funcionalidad solo está terminada cuando está implementada, tipada, probada, documentada y puede reanudarse con seguridad cuando corresponda.
