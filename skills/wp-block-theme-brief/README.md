# wp-block-theme-brief

Skill de Claude que conduce una entrevista guiada en español y produce un `BRIEF.md` en inglés
listo para entregar a [`wp-block-themes`](https://github.com/WordPress/agent-skills/tree/HEAD/skills/wp-block-themes),
la skill oficial de WordPress para desarrollo de temas de bloques.

Traduce lo que un cliente pide en lenguaje de negocio ("quiero una web elegante con testimonios")
a los artefactos reales de un tema de bloques: `theme.json`, `templates/`, `parts/`, `patterns/`
y `styles/`.

## Por qué

`wp-block-themes` construye bien, pero asume que ya sabes qué construir. Sin un brief estructurado
el agente pregunta a mitad de build, infiere en silencio o produce un tema que no encaja con lo
acordado. Esta skill separa las dos fases: primero se define, luego se construye.

## Qué hace

1. **Captura express (siempre).** Tres preguntas: tipo de sitio, secciones de la home, estilo visual.
   Es el mínimo viable para construir un tema.
2. **Profundidad opcional.** Ofrece cinco bloques —estructura, identidad visual, funcionalidad,
   layout, técnico— y solo ejecuta los que el usuario elige.
3. **Defaults por tipo de sitio.** Todo lo no preguntado se rellena con una tabla de defaults
   (templates, secciones de home, `contentSize`/`wideSize`) y se marca `(inferred)` en el brief
   para que sea revisable de un vistazo.
4. **Corte de alcance tema vs plugin.** Cada petición se clasifica antes de entrar al brief.
5. **Genera `BRIEF.md`** en inglés, con mapeo explícito a ficheros del tema y checklist de validación.

## El corte de alcance

Es la parte que evita el error más caro de un brief mal hecho:

| Petición | Dónde vive |
|---|---|
| Colores, tipografía, espaciado, layout | `theme.json` — tema |
| Cabecera, pie, plantillas | `parts/`, `templates/` — tema |
| Secciones de home | `patterns/*.php` — tema |
| Modo claro/oscuro | `styles/*.json` — tema |
| Formulario de contacto, reservas | **Plugin.** El tema solo aporta el patrón contenedor y los estilos |
| CPTs (portfolio, testimonios, propiedades) | **Plugin.** Es contenido, no presentación: en el tema, el cliente pierde los datos al cambiarlo |
| Slider, calculadora, filtros dinámicos | **Bloque en plugin** o bloque de terceros |
| Sidebar clásico / widget areas | No existe en temas de bloques: columnas o template part |

Lo que cae en la columna derecha va a la sección `Out of theme scope` del brief, con el plugin
propuesto y un responsable — no a la lista de trabajo del tema.

## Salida

`BRIEF.md` con 12 secciones: identity (slug, text domain, WP mínimo, versión de `theme.json`),
design direction (paleta en 5 slugs con hex y ratios de contraste, tipografía, layout), templates,
template parts, patterns, style variations, navegación, responsive, compatibilidad con plugins,
out of theme scope, preguntas abiertas y orden de construcción.

## Instalación

**Claude Code / Cowork (cuenta):** guarda la skill desde la tarjeta de propuesta. Aparece como
`/wp-block-theme-brief`.

**Claude Code (local):**

```bash
git clone https://github.com/<tu-org>/wp-block-theme-brief.git \
  ~/.claude/skills/wp-block-theme-brief
```

**Repo de proyecto:** copia la carpeta en `.claude/skills/` del repo.

## Uso

```
/wp-block-theme-brief
```

O simplemente: *"quiero definir un tema de bloques para un restaurante"*.

Atajos: si el primer mensaje ya contiene tipo de sitio, secciones o estilo, la skill salta esas
preguntas. Si dices "lo mínimo" o "rápido", ejecuta solo las tres preguntas y genera el brief
con defaults.

Encadenado con el desarrollo:

```
/wp-block-theme-brief          → BRIEF.md
/wp-block-themes BRIEF.md      → style.css → theme.json → parts/ → templates/ → patterns/ → styles/
```

## Estructura

```
wp-block-theme-brief/
└── SKILL.md
```

Un único fichero, sin scripts ni referencias externas.

## Requisitos

- Claude Code o Cowork con soporte de skills.
- Para la fase de build: [`wp-block-themes`](https://github.com/WordPress/agent-skills) instalada.
- La skill asume `theme.json` v3 (WordPress 6.6+) salvo que se declare un mínimo inferior.

## Licencia

GPLv2
