# Academic Vault Processor

Este contexto describe el lenguaje del flujo que convierte una clase grabada en conocimiento académico organizado dentro de un vault de Obsidian.

## Language

**Materia**:
Curso académico al que pertenecen las sesiones, los conceptos y el material docente.
_Avoid_: Curso, asignatura

**Sesión**:
Unidad de clase representada por un único video MP4 consolidado, aunque originalmente haya sido grabada en varias partes.
_Avoid_: Archivo, grabación, clase

**Material docente**:
Presentación utilizada por el docente durante una sesión y entregada como PDF o, posteriormente, PPTX. Sirve para aportar estructura y vocabulario, pero no demuestra por sí sola que un tema haya sido explicado.
_Avoid_: Adjunto, documento auxiliar

**Transcripción**:
Representación textual completa de una sesión, conservada con segmentos temporales internos después de eliminar detalles técnicos innecesarios del motor de reconocimiento.
_Avoid_: Resumen, nota de sesión

**Bloque de sesión**:
Porción consecutiva de una transcripción que conserva contexto suficiente para ser analizada independientemente y luego consolidada.
_Avoid_: Página, capítulo

**Nota de sesión**:
Nota principal que organiza lo realmente tratado durante una sesión: resumen, temas, explicaciones, ejemplos, preguntas potenciales y dudas.
_Avoid_: Transcripción, minuta

**Concepto independiente**:
Idea académica suficientemente explicada, importante y reutilizable para merecer una nota propia en el vault.
_Avoid_: Término, mención

**Recapitulación**:
Contenido de una sesión dedicado a recordar una sesión anterior; permanece en la sesión actual y enlaza la anterior cuando puede identificarse.
_Avoid_: Duplicado, repetición

**Análisis académico**:
Representación estructurada y validada del conocimiento extraído de una sesión antes de convertirlo en notas Markdown.
_Avoid_: Respuesta de IA, resumen
