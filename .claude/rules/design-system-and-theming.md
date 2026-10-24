---
description: Tema visual, idiomas (ARB) y formato de importes y fechas.
globs: ["lib/ui/**", "lib/l10n/**", "lib/main.dart"]
paths:
  - "lib/ui/**"
  - "lib/l10n/**"
  - "lib/main.dart"
---

# Tema, idiomas y formatos

## Objetivo

- Tema Material 3 en `lib/ui/core/themes/app_theme.dart`. La paleta, el modo oscuro y la tipografía se deciden en la spec de diseño (`006-diseno-visual`); hasta entonces, un `ColorScheme` provisional.
- Interfaz en portugués de Brasil y español, con R$ como única moneda.

## Reglas

- **Colores y tipografía** siempre desde el tema: `Theme.of(context).colorScheme` y `.textTheme`. Nada de `Colors.*`, `Color(0x…)` ni `fontSize` fuera de `lib/ui/core/themes/`.
- **Ingresos y gastos** se distinguen con colores del tema (p. ej. una `ThemeExtension` `ColoresApp` con `ingreso` y `gasto`) **y** con signo o icono: el color nunca es la única pista.
- Medidas repetidas (márgenes, radios, zona táctil mínima de 48) en `lib/ui/core/themes/dimens.dart`.
- **Widgets compartidos** en `lib/ui/core/ui/` (estado vacío, error, avisos). Los de una sola pantalla, privados (`_Nombre`) en su archivo.

## Idiomas (l10n)

- `flutter_localizations` + `generate: true` + `l10n.yaml`. Plantilla: `lib/l10n/app_pt.arb`; traducción: `app_es.arb`. Las dos siempre con las mismas claves.
- `supportedLocales: [pt BR, es]`; si el idioma del móvil no es ninguno de los dos, se usa portugués (`localeResolutionCallback`).
- **Ningún texto visible escrito en un widget**: todo con `AppLocalizations.of(context)`. Claves en `lowerCamelCase` agrupadas por pantalla (`homeSaldo`, `movimientoGuardar`).
- Plurales y textos con datos con la sintaxis ICU de los ARB, no concatenando.
- Las categorías predefinidas se traducen con su clave (`categoria_alimentacion`); las propias del usuario se muestran tal cual.

## Formatos

- **Importes**: siempre desde centavos con `NumberFormat.currency(locale: <idioma activo>, symbol: 'R\$', decimalDigits: 2)` a través de `lib/utils/formatters.dart`. Nunca `toStringAsFixed` ni concatenar `'R\$ '`.
- **Entrada de importes**: teclado numérico; acepta coma o punto decimal; la conversión a centavos es una función pura de `domain/` con tests.
- **Fechas y meses**: `DateFormat` con el idioma activo (`outubro de 2026` / `octubre de 2026`).

## Anti-patrones

- `Text('Salvar')` o cualquier texto literal visible.
- `double` para mostrar dinero (`(centavos / 100).toString()`).
- Usar solo rojo/verde para distinguir gasto e ingreso.

## Checklist

- [ ] Sin colores ni tamaños de texto fijos en widgets.
- [ ] Claves nuevas en `app_pt.arb` y `app_es.arb`, y `flutter gen-l10n` ejecutado.
- [ ] Importes y fechas formateados con `intl` según el idioma activo.
