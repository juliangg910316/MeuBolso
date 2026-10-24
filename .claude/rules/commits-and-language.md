---
description: Idioma del proyecto y formato de commits y PRs.
alwaysApply: true
---

# Commits e idioma

## Objetivo

- Historial legible y homogéneo en español (es-ES).
- Mismo idioma en respuestas, comentarios de código y documentación. Los textos de la interfaz van en los ARB, en portugués de Brasil y español.

## Patrones

- **Idioma**: español para commits, PRs, comentarios y documentación. Identificadores de código en el idioma ya usado (dominio en español: `movimiento`, `importeCentavos`; términos técnicos en inglés: `repository`, `cubit`).
- **Commits semánticos** (Conventional Commits): `<tipo>(<ámbito opcional>): <resumen>`.
  - Tipos: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`, `style`, `build`.
  - Título en imperativo, minúsculas, sin punto final, ≤ 72 caracteres.
  - Cuerpo: qué cambia y por qué; referencia a issues (`Refs #12`, `Closes #12`) si existen.
- **PRs**: título con el mismo formato que el commit principal; descripción con "Qué", "Por qué" y "Cómo probarlo"; labels según el tipo (`feature`, `bug`, `docs`).
- Un commit por cambio lógico; el código generado (`*.g.dart`, `*.freezed.dart`, `app_localizations*.dart`) va en el mismo commit que su origen.

## Ejemplos cortos

- `feat(movimientos): registrar un gasto con su categoría`
- `fix(resumen): no contar movimientos de otro mes`
- `docs: documentar la arquitectura en CLAUDE.md`
- `chore(deps): añadir supabase_flutter`

## Checklist

- [ ] Título semántico en español e imperativo.
- [ ] Cuerpo explica el porqué si no es obvio.
- [ ] `dart format .`, `flutter analyze` y `flutter test` ejecutados antes del commit.
