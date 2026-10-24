---
name: sdd
description: Úsala siempre que trabajes con Spec-Driven Development en este proyecto (docs/constitution.md o cualquier archivo dentro de specs/) - redactar, revisar o cambiar specs, planes y tareas, o implementar y validar tareas de una spec.
---

# Spec-Driven Development (SDD)

## Flujo
Constitución → Spec → Clarificación → Plan → Tareas → Implementación → Validación → Cambio.
- Nunca pases a la siguiente fase sin la aprobación explícita del usuario.
- La spec manda: si algo no está en la spec, no se implementa. Si falta una decisión, para y pregunta.
- Un cambio de requisitos se hace primero en la spec, luego en el plan y las tareas, y por último en el código.
- Cada spec vive en su carpeta: `specs/NNN-nombre/` con `spec.md`, `plan.md` y `tasks.md`.
- `docs/ESPECIFICACION.md` es la visión global del producto (decisiones, modelo de datos, categorías, alcance y **requisitos comunes**, que aplican a todas las specs). Los requisitos de cada funcionalidad viven solo en su spec: no los dupliques.
- Antes de planificar, revisa qué parte de la spec ya existe en el código: el plan parte de ahí, no lo rehace.
- Al terminar cada fase, actualiza `MEMORY.md`.

## Estados
`spec.md`, `plan.md` y `tasks.md` empiezan con una línea `Estado:`.
- Al crearse o cambiar: `Estado: borrador`.
- **Cuando el usuario apruebe explícitamente** un archivo en la conversación, cambia su línea a `Estado: aprobada`. Nunca lo apruebes por tu cuenta.
- Una fase solo empieza si la anterior está `aprobada`: el plan exige la spec aprobada; las tareas, el plan aprobado; la implementación, las tareas aprobadas.
- Cuando la validación confirma que se cumple, la spec pasa a `implementada`.
- Si cambia la spec (`/sdd-change`), vuelve a `borrador`, y `plan.md` y `tasks.md` (si existen) también, hasta que se revisen.

## Plantilla de spec (spec.md)
```
# Spec NNN — <Nombre>
Estado: borrador | aprobada | implementada
## Contexto y objetivo
## Usuarios
## Historias de usuario
- HU-1. Como <rol>, quiero <acción> para <beneficio>.
## Definiciones (solo si hay términos que puedan interpretarse de varias formas)
## Requisitos funcionales
## Requisitos no funcionales
## Textos de la interfaz (pt-BR y es, solo los que el cliente deba revisar)
## Navegación (desde dónde se llega a esta funcionalidad y a dónde se puede ir)
## Casos límite
## Fuera de alcance
## Criterios de finalización
## Dudas abiertas
- [NECESITA ACLARACIÓN] <duda>
```
La spec describe el QUÉ y el POR QUÉ. Nada de stack, arquitectura ni nombres de archivos. Cada spec define su propia navegación.

## Requisitos en EARS (en español)
- RF-x: CUANDO <evento>, EL SISTEMA <respuesta>.
- RF-x: SI <condición no deseada>, ENTONCES EL SISTEMA <respuesta>.
- RF-x: MIENTRAS <estado>, EL SISTEMA <respuesta>.
- RF-x: EL SISTEMA <comportamiento permanente>.

Cada RF debe ser verificable: nada de "rápido", "sencillo" o "bonito" sin un criterio medible. Los importes de los ejemplos, siempre en R$ y con su valor en centavos.

## Plan (plan.md)
`Estado: borrador` · Archivos y responsabilidades (respetando las capas de `AGENTS.md`: `ui` → `domain` → `data`) · Funciones puras en `lib/domain/` (con "hoy" como parámetro; también las validaciones) · Algoritmo en pseudocódigo · Interfaz (pantallas, Cubit y estado, ruta, navegación y registro en DI) · Textos nuevos en los ARB (pt-BR y es) · Cambios en Supabase: migración SQL en `supabase/migrations/` y políticas RLS · Tests que se escribirán (unidad, repositorio con fakes, widget) · Decisiones justificadas con su alternativa descartada · Estrategia de verificación. Indica qué RF cubre cada parte.

## Tareas (tasks.md)
```
Estado: borrador

- [ ] **Tn. <Descripción>.** RF-x, RF-y
  - Tests: <qué tests se escriben primero>.
  - Hecho cuando: <comprobación verificable>.
```
Máximo 20-30 min por tarea, en orden de dependencia. Si salen más de 10, propón dividir la spec.

## Implementación (tests primero)
Una sola tarea cada vez:
1. Escribe primero los tests de la tarea y ejecuta `flutter test`: deben fallar (rojo).
2. Escribe el código mínimo para que pasen (verde). Prohibido avanzar con tests en rojo.
3. Si cambian clases `@freezed`, `dart run build_runner build`. Si cambian los `.arb`, `flutter gen-l10n`.
4. Si cambia el esquema de Supabase: nueva migración en `supabase/migrations/` (nunca editar una ya aplicada), con RLS, y avisa al autor de que debe aplicarla.
5. `dart format .` → `flutter analyze` sin errores ni avisos → `flutter test` en verde.
6. Marca la tarea y PARA.

Supabase en los tests: los repositorios reciben sus servicios por constructor; en los tests se inyectan fakes escritos a mano. Nunca se usa el Supabase real en un test.

## Validación
Recorre la spec RF por RF:
- Cada RF con su test: qué test lo cubre y si pasa.
- **Interfaz**: recorrido manual con los pasos exactos para que el autor lo ejecute y confirme, en un emulador, simulador o dispositivo Android o iOS (`flutter run --dart-define-from-file=env.json`). Incluye portugués y español cuando la spec lo afecte.
- Veredicto final: ¿la spec está cumplida?
