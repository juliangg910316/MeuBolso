---
description: SDD · Genera el plan técnico de una spec aprobada
argument-hint: <NNN-nombre>
disable-model-invocation: true
---

Lee docs/constitution.md, AGENTS.md, MEMORY.md y specs/$0/spec.md, y el código relacionado. Usa la skill sdd.
NO escribas código.

Si la spec no está en "Estado: aprobada" o tiene dudas abiertas, para y avísame.

Genera specs/$0/plan.md con "Estado: borrador" y: qué parte de la spec ya existe en el código,
archivos que se crean o modifican y la responsabilidad de cada uno (capas ui → domain → data),
funciones puras de lógica y validaciones en lib/domain/ (con "hoy" como parámetro), algoritmo
en pseudocódigo, cómo se pinta en la interfaz (pantalla, Cubit y estado, ruta, navegación y DI),
textos nuevos en los ARB (pt-BR y es), cambios en Supabase (migración SQL y políticas RLS) si los hay,
tests que se escribirán primero (unidad, repositorio con fakes, widget), decisiones técnicas
justificadas (con su alternativa descartada) y estrategia de verificación.

Todo debe respetar la constitución y cubrir todos los RF y los requisitos comunes. Marca qué RF
cubre cada parte. Si el plan necesita una dependencia nueva, márcalo como decisión del autor.
Muéstrame el plan y espera. Si te digo que lo apruebo, cambia su estado a "aprobada".
