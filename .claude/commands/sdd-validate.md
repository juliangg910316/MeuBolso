---
description: SDD · Valida la spec RF por RF (tests + recorrido en Android o iOS)
argument-hint: <NNN-nombre>
disable-model-invocation: true
---

Recorre specs/$0/spec.md requisito por requisito. Usa la skill sdd.
Para cada RF indica qué código lo cumple, qué test lo cubre y cómo se verifica:
- Lógica y repositorios: el test que lo cubre y si pasa.
- Interfaz: dame los pasos exactos de un recorrido manual en un emulador, simulador o
  dispositivo Android o iOS (`flutter run --dart-define-from-file=env.json`) y el resultado
  esperado, en portugués y en español si la spec lo afecta, para que yo lo ejecute y lo confirme.

Ejecuta también `flutter analyze` y `flutter test`.
Si algún RF no está cubierto o falla, dilo claramente. NO arregles nada todavía.
Después comprueba los criterios de finalización y dame un veredicto:
¿la spec está cumplida? Si lo está y lo confirmo, cambia su estado a "implementada" y actualiza MEMORY.md.
