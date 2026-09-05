# WordPress AI tools

Skills de Claude para trabajar con WordPress. Cubren la fase que las skills oficiales de
WordPress no cubren: **definir qué hay que construir antes de construirlo.**

[`WordPress/agent-skills`](https://github.com/WordPress/agent-skills) construye bien, pero asume
decisiones ya tomadas. Sin un brief estructurado el agente pregunta a mitad de build, infiere en
silencio, o produce algo que no encaja con lo acordado con el cliente. Estas skills separan las
dos fases: primero se define en una entrevista guiada, luego se construye a partir de un
documento cerrado.

## Skills

| Skill | Qué define | Entrega a |
|---|---|---|
| [`wp-block-theme-brief`](skills/wp-block-theme-brief) | Un tema de bloques completo: `theme.json`, `templates/`, `parts/`, `patterns/`, `styles/` | [`wp-block-themes`](https://github.com/WordPress/agent-skills/tree/HEAD/skills/wp-block-themes) |
| [`wp-block-brief`](skills/wp-block-brief) | Un bloque individual del editor: `block.json`, atributos, `supports`, render, Interactivity API | [`wp-block-development`](https://github.com/WordPress/agent-skills/tree/HEAD/skills/wp-block-development) |

## Flujo

```
/wp-block-theme-brief          → BRIEF.md
/wp-block-themes BRIEF.md      → style.css → theme.json → parts/ → templates/ → patterns/ → styles/

/wp-block-brief                       → BLOCK-BRIEF.md
/wp-block-development BLOCK-BRIEF.md  → block.json → registro PHP → edit.js → save/render → view.js
```

## Diseño común

Las dos skills comparten la misma estructura, deliberadamente:

1. **Captura express.** Tres preguntas que son el mínimo viable. Nada más.
2. **Profundidad opcional.** Bloques temáticos que solo se ejecutan si el usuario los pide.
3. **Defaults tabulados.** Lo no preguntado se rellena desde una tabla —por tipo de sitio, o por
   arquetipo de bloque— y se marca `(inferred)` en el brief, para que revisar las suposiciones
   sea cuestión de buscar una palabra.
4. **Cortes de alcance.** Cada petición se clasifica antes de entrar al brief. Es la parte que
   evita el retrabajo caro:
   - lo que ya resuelve `theme.json` o `supports` no se implementa a mano;
   - lo que es contenido (CPTs) o backend (persistencia, endpoints, claves de API) va a un
     plugin, no al tema ni al bloque;
   - lo que un plugin maduro ya hace (formularios, multiidioma) no se reconstruye.
5. **Brief en inglés.** La entrevista es en español; el documento sale en inglés, que es el
   idioma de las skills de destino, de `theme.json` y de los identificadores del proyecto.

## Instalación

**Claude Code / Cowork (cuenta):** propón la skill desde una sesión y guárdala. Queda disponible
como `/wp-block-theme-brief` y `/wp-block-brief`.

**Claude Code (local):**

```bash
git clone https://github.com/PabloMoratinos/WordPress-AI-tools.git
cp -r WordPress-AI-tools/skills/wp-block-theme-brief ~/.claude/skills/
cp -r WordPress-AI-tools/skills/wp-block-brief       ~/.claude/skills/
```

**Repo de proyecto:** copia las carpetas que necesites en `.claude/skills/` del repo.

Cada skill tiene su propio README con el detalle de qué pregunta, qué infiere y qué produce.

## Estructura

```
WordPress-AI-tools/
├── LICENSE
├── README.md
└── skills/
    ├── wp-block-brief/
    │   ├── SKILL.md
    │   └── README.md
    └── wp-block-theme-brief/
        ├── SKILL.md
        └── README.md
```

Un `SKILL.md` por skill, sin scripts ni ficheros de referencia: todo el contenido cabe en un
único fichero y así se mantiene auditable de una lectura.

## Requisitos

- Claude Code o Cowork con soporte de skills.
- Para la fase de build: [`WordPress/agent-skills`](https://github.com/WordPress/agent-skills)
  instalada.
- `wp-block-theme-brief` asume `theme.json` v3 (WordPress 6.6+);
  `wp-block-brief` asume `apiVersion: 3` y `@wordpress/scripts`.

## Estado

En uso interno. Las tablas de defaults —tipos de sitio y arquetipos de bloque— son la parte que
más va a moverse conforme aparezcan casos que no encajen. Los issues con casos reales que las
rompan son bienvenidos.

## Licencia

Copyright (C) 2026 Pablo Moratinos

Este programa es software libre: puedes redistribuirlo y/o modificarlo bajo los términos de la
GNU General Public License publicada por la Free Software Foundation, en su versión 2 o
cualquier versión posterior.

Se distribuye con la esperanza de que sea útil, pero SIN NINGUNA GARANTÍA, ni siquiera la
garantía implícita de COMERCIABILIDAD o IDONEIDAD PARA UN PROPÓSITO PARTICULAR. Consulta la
GNU General Public License para más detalles: [`LICENSE`](LICENSE).

Misma licencia que WordPress.
