# APRD - Academic Vault Processor V1

## Visión

Academic Vault Processor convierte una sesión académica grabada en conocimiento claro, conectado y reutilizable dentro de Obsidian. El producto final no es la transcripción: es una nota de sesión útil y un conjunto prudente de conceptos independientes.

## Usuario y contexto

- Uso personal sobre un vault ya creado.
- Sesiones en español, normalmente de tres horas.
- Videos MP4 de grabaciones de pantalla, ocasionalmente con ruido, eco o bajo volumen.
- Un docente principal; la voz de estudiantes no necesita diarización en la V1.
- Diapositivas disponibles como contexto cuando existan.
- Equipo objetivo: 16 GB de RAM, Intel i5 de 12.ª generación y GTX 1650 de 4 GB.

## Necesidad

Revisar manualmente una clase de tres horas y convertirla en notas consume demasiado tiempo. Una transcripción sin estructura tampoco facilita el estudio. El sistema debe preservar lo importante, distinguir explicaciones de ejemplos y evitar un vault lleno de notas triviales.

## Propuesta de valor

- Transcripción local con equilibrio entre velocidad y precisión.
- Organización académica de alta calidad mediante Gemini.
- Uso de diapositivas para corregir vocabulario y reconocer estructura.
- Procesamiento reanudable frente a interrupciones y cuotas gratuitas variables.
- Notas coherentes con las reglas existentes en `docs/AGENTS.md`.
- Eliminación segura del video después de completar el conocimiento derivado.

## Requisitos principales

1. Procesar una sesión MP4 consolidada.
2. Intentar GPU y continuar automáticamente en CPU.
3. Conservar una transcripción normalizada.
4. Analizar bloques con Gemini Flash.
5. Usar Pro únicamente cuando esté disponible.
6. Consolidar jerárquicamente una sesión extensa.
7. Generar nota de sesión, índice de materia y conceptos justificados.
8. Pausar y reanudar al agotar cuota.
9. No inventar ni mezclar conocimiento general sin marcarlo.
10. No eliminar el video ante un trabajo incompleto.

## Métricas de éxito

- Una sesión de tres horas finaliza de extremo a extremo.
- El usuario considera útil la nota sin reorganizarla por completo.
- Las explicaciones y ejemplos del docente se distinguen correctamente.
- Las diapositivas reducen errores terminológicos.
- No se crean notas por menciones aisladas.
- Un fallo o límite de API no obliga a repetir la transcripción.
- El pipeline funciona sin Gemini Pro.

## Riesgos

- Calidad variable del audio.
- Compatibilidad de CUDA/CTranslate2 con una GPU de 4 GB.
- Límites gratuitos cambiantes de Gemini.
- Respuestas estructuradas incompletas.
- Confusión entre contenido hablado y diapositivas.
- Eliminación prematura del archivo fuente.

Cada riesgo tiene una respuesta explícita en `docs/PROJECT_SPEC.md`.

## Evolución posterior

- PPTX y OCR.
- Diarización opcional.
- Deduplicación semántica entre sesiones.
- Flashcards y evaluaciones avanzadas.
- RAG, búsqueda y chat sobre el vault.
- Otros tipos de fuentes académicas.
