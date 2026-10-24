---
description: Estándares de código Dart del proyecto.
alwaysApply: true
---

# Estándares de código Dart

## Objetivo

- Código uniforme, sin avisos de `flutter analyze` y formateado con `dart format`.
- Fuente de verdad: `analysis_options.yaml` (`flutter_lints` + reglas extra) y [Effective Dart](https://dart.dev/effective-dart).

## Reglas

- **Formato**: ejecutar `dart format .` tras cada cambio. No discutir estilo a mano: lo decide el formateador (estilo "tall" de Dart 3.7+).
- **Análisis**: `flutter analyze` debe terminar en "No issues found!". No silenciar lints con `// ignore:` sin justificarlo en un comentario.
- **Comillas**: simples (`prefer_single_quotes`); dobles solo si el texto contiene `'`.
- **`const` y `final`**: `const` en constructores y declaraciones siempre que se pueda (`prefer_const_constructors`, `prefer_const_declarations`); locales `final` si no se reasignan (`prefer_final_locals`).
- **Tipos**: declarar siempre el tipo de retorno (`always_declare_return_types`). Evitar `dynamic`; usar `Object?` si el tipo es desconocido.
- **Trailing commas**: obligatorias en listas de argumentos multilínea (`require_trailing_commas`).
- **Imports**: relativos dentro de `lib/` (patrón actual). Orden: `dart:`, `package:`, relativos, separados por línea en blanco.
- **Naming**: `UpperCamelCase` para tipos, `lowerCamelCase` para miembros y constantes, `snake_case` para archivos. Los nombres de dominio siguen la especificación en español (`importeCentavos`, `categoria`, `fecha`).
- **Null safety**: sin `!` salvo que el valor esté garantizado (p. ej. un campo obligatorio ya validado). Preferir `??`, `?.` y patrones.
- **Dart moderno (SDK ^3.13)**: usar `switch` expressions y patrones, `sealed`/`final class`, records, dot shorthands (`.fromSeed`) y parámetros con nombre privados (`this._campo`).
- **Async**: `async`/`await` en lugar de `.then()`. Cancelar `StreamSubscription` en `close()`/`dispose()`. Capturar `on Exception`, no `catch` genérico.
- **Clases utilitarias**: `abstract final class` con miembros `static` (p. ej. `AppTheme`, `RouteNames`).
- **Modelos y estados con freezed** (freezed 3+):
  - Clase de datos: `@freezed abstract class X with _$X { const factory X({...}) = _X; }` con `part 'x.freezed.dart';`.
  - Con getters o métodos propios: añadir `const X._();`.
  - Uniones: `@freezed sealed class X with _$X` con un `factory` por caso; se consumen con `switch` y patrones (no hay `when` ni `map`).
  - Las colecciones de una clase freezed son no modificables y se comparan por contenido.
  - Los enums siguen siendo enums. `Result<T>` (`lib/utils/`) no es un modelo y sigue siendo una `sealed class` manual.
- **Documentación**: `///` al inicio de cada archivo (`library;`) explicando el papel del archivo en su capa, y en APIs públicas no obvias. Comentarios en español.
- **Logs**: prohibido `print` (`avoid_print`). Para depuración temporal, `debugPrint`; no dejar logs de datos del usuario.

## Anti-patrones

- Código generado editado a mano (`*.g.dart`, `*.freezed.dart`): se regenera con `dart run build_runner build`.
- `copyWith`, `==` o `hashCode` escritos a mano en modelos o estados: usar `@freezed`.
- `late` sin necesidad o para evitar pensar en la nulabilidad.
- Funciones de más de ~40 líneas o widgets con `build()` gigante: extraer widgets privados (`_StatTile`).
- Lógica en métodos `static` de widgets o en extensiones sobre tipos de Flutter.

## Checklist

- [ ] `dart format .` sin cambios pendientes.
- [ ] `flutter analyze` sin avisos.
- [ ] Sin `print`, sin `dynamic` innecesario, sin `!` injustificados.
- [ ] Archivo nuevo con comentario `///` de cabecera.
