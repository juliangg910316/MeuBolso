# Especificación funcional — MeuBolso

*Versión inicial: 2026-10-24 · Estado: borrador*

> Visión global del producto. Los requisitos de cada funcionalidad (RF en EARS, navegación, casos límite y criterios de finalización) viven en las specs numeradas de `specs/` (ver la sección 7). Aquí solo están los **requisitos comunes**, que aplican a todas (sección 6). Los principios innegociables están en `docs/constitution.md`, y la arquitectura en `AGENTS.md`.

## 1. Descripción

**MeuBolso** es una aplicación Flutter para Android e iOS de **organización financiera personal**. El usuario entra con su cuenta de Google, registra sus gastos e ingresos con una categoría, y la app los convierte en una **tabla y un gráfico sencillo del mes**, con el total de ingresos, de gastos y el saldo.

Es un **encargo de un cliente de Brasil** (descripción original en portugués, traducida en la sección 8).

### Problema
El usuario no sabe en qué se le va el dinero cada mes. Necesita una forma rápida de apuntar cada gasto e ingreso y ver de un vistazo cuánto entra, cuánto sale y en qué categorías.

### Objetivos (v1)
- Entrar con Google en un toque y recuperar los datos en cualquier móvil.
- Registrar un gasto o ingreso en pocos segundos desde el inicio (botón "Nuevo gasto").
- Ver en el inicio los ingresos, los gastos y el saldo del mes, con su tabla y su gráfico.
- Código limpio, con arquitectura clara y tests de la lógica.

## 2. Decisiones

| Tema | Decisión |
| --- | --- |
| Nombre | **MeuBolso** (definitivo; coincide con el paquete `meu_bolso`) |
| Cliente | Encargo de un cliente de Brasil |
| Titular de las cuentas | **El autor, siempre**: Supabase, Google Cloud (OAuth) y tiendas están a su nombre |
| Plataformas | Android e iOS |
| Idiomas de la interfaz | Portugués de Brasil y español (según el idioma del móvil; si no es ninguno de los dos, portugués) |
| Moneda | Solo real brasileño (R$), formateado según el idioma: `R$ 1.234,56`. Sin conversión |
| Cuenta | Inicio de sesión con Google (obligatorio para usar la app) |
| Datos | En la nube (Supabase: Auth + Postgres). Se sincronizan entre dispositivos del mismo usuario |
| Categorías | Predefinidas (sección 5) + categorías propias del usuario |
| Fecha de un movimiento | **Elegible, por defecto hoy**. El movimiento cuenta en el mes de su fecha |
| Sin conexión | **Solo avisar**: mensaje propio y el formulario no se pierde; sin caché ni modo offline |
| Gráfico | **Donut de gastos por categoría** del mes (los totales ya se ven en números) |
| Gastos repetidos (cuotas, facturas) | Se registran a mano cada mes. Recurrentes y cuotas, en la v2 |
| Periodo | Por mes: el inicio muestra el mes actual y se navega con ‹ › a los anteriores |
| Navegación | Cada spec define la suya |

### Riesgos aceptados
- **Dependencia de la conexión:** sin internet no se puede entrar, consultar ni guardar; solo se avisa (decisión del 2026-10-24).
- **Dependencia de Supabase:** si el proyecto gratuito se pausa por inactividad o cambia el plan, la app deja de funcionar hasta reactivarlo.
- **Responsabilidad del titular:** al ser el autor el titular de las cuentas, asume los costes de Supabase y de las tiendas, y la responsabilidad sobre los datos personales y financieros de los usuarios (LGPD en Brasil). Hará falta una política de privacidad antes de publicar.

## 3. Modelo de datos (borrador; se cierra en el plan de cada spec)

### Movimiento
| Campo | Tipo | Notas |
| --- | --- | --- |
| `id` | uuid | Lo genera la base de datos |
| `usuarioId` | uuid | Dueño (RLS). Nunca lo envía la UI |
| `tipo` | enum | `gasto` \| `ingreso` |
| `importeCentavos` | int | **> 0**, en centavos de R$ (R$ 12,50 → `1250`). El signo lo da el `tipo` |
| `categoriaId` | uuid / clave | Debe ser del mismo `tipo` que el movimiento |
| `fecha` | Date | Día del movimiento (fecha local, sin hora). Es la clave del mes |
| `descripcion` | String? | Opcional |
| `creadoEn` | DateTime | Lo pone la base de datos |

### Categoría
| Campo | Tipo | Notas |
| --- | --- | --- |
| `id` | uuid / clave | Las predefinidas tienen una clave fija (`alimentacion`, `salario`…) |
| `usuarioId` | uuid? | Vacío en las predefinidas; el dueño en las propias |
| `tipo` | enum | `gasto` \| `ingreso` |
| `nombre` | String | Las predefinidas se traducen con los ARB; las propias se guardan tal cual |
| `icono` / `color` | — | Por decidir en la spec de diseño |

**Datos derivados** (se calculan, no se guardan): total de ingresos, total de gastos y saldo del mes; totales por categoría.

## 4. Reglas de cálculo

- **Saldo del mes** = suma de ingresos del mes − suma de gastos del mes.
- Un movimiento pertenece al mes de su `fecha` (fecha local del dispositivo).
- Todos los cálculos se hacen con enteros (centavos); solo se convierten a reales al mostrarlos.

Definiciones completas y casos límite: en las specs 003 y 004.

## 5. Categorías predefinidas

| Tipo | Clave | Español | Portugués (Brasil) |
| --- | --- | --- | --- |
| Gasto | `alimentacion` | Alimentación | Alimentação |
| Gasto | `transporte` | Transporte | Transporte |
| Gasto | `cuota_moto` | Cuota de la moto | Parcela da moto |
| Gasto | `factura_luz` | Factura de la luz | Conta de luz |
| Gasto | `supermercado` | Supermercado | Supermercado |
| Gasto | `ocio` | Ocio | Lazer |
| Gasto | `otros_gastos` | Otros | Outros |
| Ingreso | `salario` | Salario | Salário |
| Ingreso | `ingreso_extra` | Ingreso extra | Renda extra |
| Ingreso | `trabajo_ocasional` | Trabajo ocasional | Bico |
| Ingreso | `comision` | Comisión | Comissão |
| Ingreso | `otros_ingresos` | Otros | Outros |

> "Otros" no estaba en el encargo: se añade (aprobado el 2026-10-24) para que siempre haya una categoría válida.

## 6. Requisitos comunes (aplican a todas las specs)

- RC-1: SI falla una operación de guardado, edición o borrado, ENTONCES EL SISTEMA muestra un mensaje de error comprensible y conserva los datos que el usuario había introducido.
- RC-2: SI una pantalla no tiene datos que mostrar (p. ej. un mes sin movimientos), ENTONCES EL SISTEMA muestra un mensaje que explica qué hacer para empezar, en lugar de una pantalla vacía.
- RC-3: EL SISTEMA muestra todos los textos en portugués de Brasil o en español, según el idioma del móvil (portugués si no es ninguno de los dos), incluidos los textos propios de Flutter.
- RC-4: EL SISTEMA muestra los importes en reales con el formato del idioma activo (`R$ 1.234,56`) y las fechas con el formato de ese idioma.
- RC-5: SI no hay conexión a internet al cargar o guardar, ENTONCES EL SISTEMA lo indica con un mensaje propio (no un error técnico).
- RC-6: EL SISTEMA solo muestra y modifica los datos del usuario que ha iniciado sesión.

## 7. Alcance

### MVP (v1): specs propuestas

Orden de implementación propuesto: 001 → 002 → 003 → 004 → 005 → 006.

| Spec | Contenido |
| --- | --- |
| `001-acceso-google` | Pantalla de entrada, inicio de sesión con Google, sesión recordada y cerrar sesión |
| `002-movimientos` | Botón "Nuevo gasto", alta de gastos e ingresos (importe, categoría predefinida, fecha elegible —por defecto hoy—, descripción), edición y borrado |
| `003-resumen-mensual` | Inicio: ingresos, gastos y saldo del mes, navegación ‹ › entre meses y tabla de movimientos |
| `004-grafico` | Donut de gastos por categoría del mes |
| `005-categorias-propias` | Crear, renombrar y ocultar categorías propias |
| `006-diseno-visual` | Paleta, modo claro y oscuro, tipografía e icono |

### v2
- Gastos recurrentes mensuales y compras a plazos (cuotas: "3 de 24").
- Presupuesto por categoría con avisos.
- Filtros por rango de fechas y vista anual.
- Exportar a CSV o PDF.
- Recordatorios.

## 8. Encargo original (traducido)

> **Crear una app de organización financiera**
> - Entrar.
> - Inicio de sesión con Google.
> - Pantalla de inicio con ingresos y gastos.
> - Botón "Nuevo gasto".
> - Elegir categoría: alimentación, transporte, cuota de la moto, factura de la luz, supermercado, ocio…
> - Ingresos: salario, ingreso extra, trabajo ocasional ("bico"), comisión.
> - Todo va formando una tabla y un gráfico sencillo.
