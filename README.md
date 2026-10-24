# MeuBolso

App Flutter (Android e iOS) de organización financiera: login con Google, gastos e ingresos por categoría, y tabla y gráfico del mes. Interfaz en portugués de Brasil y español; moneda R$.

- Visión y requisitos comunes: `docs/ESPECIFICACION.md`
- Principios: `docs/constitution.md`
- Specs (SDD): `specs/`
- Instrucciones para agentes: `AGENTS.md`

## Ejecutar

1. Copiar `env.example.json` a `env.json` y rellenar la URL y la publishable key de Supabase y los client ID de Google.
2. `flutter pub get`
3. `flutter run --dart-define-from-file=env.json`
