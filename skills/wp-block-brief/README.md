# wp-block-brief

Skill de Claude que conduce una entrevista guiada en español y produce un `BLOCK-BRIEF.md` en
inglés listo para entregar a [`wp-block-development`](https://github.com/WordPress/agent-skills/tree/HEAD/skills/wp-block-development),
la skill oficial de WordPress para desarrollo de bloques de Gutenberg.

Traduce "quiero un bloque que haga X" a decisiones concretas de `block.json`, `edit.js`,
`save.js`/`render.php`, atributos, `supports` e Interactivity API.

## Por qué

`wp-block-development` construye bien, pero asume decisiones ya tomadas: estático o dinámico,
qué es atributo y qué es `supports`, dónde vive la persistencia. Sin un brief estructurado el
agente infiere en silencio y acaba implementando a mano controles que WordPress ya da gratis,
o metiendo lógica de backend dentro del bloque. Esta skill separa las dos fases: primero se
define, luego se construye.

## Qué hace

1. **Captura express (siempre).** Tres preguntas: qué hace el bloque, cómo se comporta
   (estático / interactivo / servidor), y dónde vive (plugin o tema).
2. **Profundidad opcional.** Cuatro bloques —atributos y controles, diseño visual, datos y
   persistencia, técnico— y solo ejecuta los que el usuario elige.
3. **Defaults por arquetipo.** Once arquetipos (contador, slider antes/después, acordeón,
   formulario, listado de CPT, testimonios, calculadora, mapa, feed externo, contenedor…) con
   su modelo de render, `save()`, `render.php`, `viewScriptModule`, InnerBlocks, persistencia y
   datos externos ya resueltos. Todo lo derivado se marca `(inferred)`.
4. **Tres cortes de alcance.** Ver abajo.
5. **Genera `BLOCK-BRIEF.md`** en inglés, 14 secciones, con criterios de aceptación y orden de build.

## Los tres cortes de alcance

Son la parte que evita el retrabajo.

### 1. `supports` no es un atributo

Si el cliente pide un selector de color o de espaciado, no es un campo del bloque: es una línea
en `block.json`. WordPress genera el control y el CSS.

| Petición | `block.json` |
|---|---|
| Color de texto y fondo | `supports.color` |
| Tipografía | `supports.typography` |
| Padding, margin, blockGap | `supports.spacing` |
| Alineación wide/full | `supports.align` |
| Borde y radio | `supports.border` |
| Sombra | `supports.shadow` |
| Clase propia / ancla | `supports.customClassName`, `supports.anchor` |

Atributo propio solo para lo que el editor no cubre: textos, URLs, fechas, IDs de medio,
números y opciones específicas del bloque.

### 2. La persistencia no es del bloque

Guardar envíos, reservas o votos es PHP del plugin: CPT o tabla propia, `register_rest_route`
con `permission_callback`, verificación de nonce, sanitizado y rate limiting. El bloque solo
pinta el formulario y llama al endpoint. Las claves de API viven en servidor con caché por
`set_transient` — nunca en el bundle JS, donde cualquiera las lee.

### 3. Un bloque en el tema rompe el contenido

Un bloque registrado por el tema desaparece al cambiar de tema, y todas las entradas que lo
usan quedan como **"Invalid block"**. Solo es aceptable para bloques puramente decorativos;
cualquier cosa con datos, formularios o CPT va en un plugin.

## Salida

`BLOCK-BRIEF.md` con 14 secciones: identity (namespace/slug, categoría, text domain,
`apiVersion`, WP mínimo), modelo de comportamiento con justificación, tabla de atributos
(`type`/`source`/`default`/dónde se edita), `supports`, controles del editor (canvas, sidebar,
toolbar), InnerBlocks, markup y estilos, datos y persistencia, datos externos, contexto de
proyecto, out of block scope, criterios de aceptación, preguntas abiertas y orden de build.

## Instalación

**Claude Code / Cowork (cuenta):** guarda la skill desde la tarjeta de propuesta. Aparece como
`/wp-block-brief`.

**Claude Code (local):**

```bash
git clone https://github.com/PabloMoratinos/WordPress-AI-tools.git
cp -r WordPress-AI-tools/skills/wp-block-brief ~/.claude/skills/
```

**Repo de proyecto:** copia la carpeta `wp-block-brief/` en `.claude/skills/` del repo.

## Uso

```
/wp-block-brief
```

O simplemente: *"necesito un bloque de contador regresivo para el plugin del cliente"*.

Atajos: si el primer mensaje ya describe el bloque, la skill salta esa pregunta. Si dices
"lo mínimo" o "rápido", ejecuta solo las tres preguntas y genera el brief con los defaults del
arquetipo.

Encadenado con el desarrollo:

```
/wp-block-brief                       → BLOCK-BRIEF.md
/wp-block-development BLOCK-BRIEF.md  → block.json → registro PHP → edit.js → save/render → view.js
```

## Skill hermana

[`wp-block-theme-brief`](https://github.com/PabloMoratinos/WordPress-AI-tools/tree/main/skills/wp-block-theme-brief) hace lo mismo para
temas de bloques completos, con entrega a `wp-block-themes`. Misma estructura: express →
profundidad opcional → defaults → cortes de alcance → brief.

## Estructura

```
wp-block-brief/
├── SKILL.md
└── README.md
```

## Requisitos

- Claude Code o Cowork con soporte de skills.
- Para la fase de build: [`wp-block-development`](https://github.com/WordPress/agent-skills) instalada.
- La skill asume `apiVersion: 3` y `@wordpress/scripts` como tooling de build.

## Verificar antes de usar en producción

La skill deja marcado un punto que depende de la versión de WordPress objetivo: si
`supports.border` ya es estable o sigue siendo `__experimentalBorder`. Compruébalo contra el
WP mínimo del proyecto en lugar de asumirlo.

## Licencia

Copyright (C) 2026 Pablo Moratinos

Este programa es software libre: puedes redistribuirlo y/o modificarlo bajo los términos de la
GNU General Public License publicada por la Free Software Foundation, en su versión 2 o
cualquier versión posterior.

Se distribuye con la esperanza de que sea útil, pero SIN NINGUNA GARANTÍA, ni siquiera la
garantía implícita de COMERCIABILIDAD o IDONEIDAD PARA UN PROPÓSITO PARTICULAR. Consulta la
GNU General Public License para más detalles: [`LICENSE`](../../LICENSE).

Misma licencia que WordPress y que los bloques que esta skill ayuda a especificar.
