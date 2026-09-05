---
name: wp-block-brief
description: Entrevista guiada en español para definir un bloque del editor de WordPress (Gutenberg) y generar un BLOCK-BRIEF.md en inglés listo para entregar a la skill wp-block-development. Úsala al especificar, acotar o encargar un bloque nuevo.
---

# WP Block Brief

Conduce una entrevista corta y produce `BLOCK-BRIEF.md`: un documento en inglés que traduce "quiero un bloque que haga X" a decisiones concretas de `block.json`, `edit.js`, `save.js`/`render.php`, atributos, `supports` e Interactivity API, para que la skill `wp-block-development` pueda construirlo sin volver a preguntar.

## Reglas de operación

- **Entrevista en español, entregable en inglés.** Identificadores técnicos (namespace, slug, nombres de atributo, text domain) siempre en inglés y en `kebab-case` (atributos en `camelCase`, que es la convención de `block.json`).
- **Usa `AskUserQuestion`**, máximo 4 preguntas por llamada, con opciones concretas. Solo pide texto libre para lo que no es enumerable: descripción de la funcionalidad, nombre del bloque, endpoints.
- **No preguntes lo que ya te han dicho.** Si el mensaje inicial describe el bloque, salta la pregunta 1.
- **Todo lo no preguntado se infiere** con la tabla de arquetipos del Paso 5 y se marca `(inferred)` en el brief.
- **No inventes** endpoints, claves de API, nombres de CPT ni rutas de repo. Si falta, escribe `TBD`.
- Si el usuario dice "lo mínimo" o "rápido", ejecuta solo el Paso 1 y salta al Paso 5.

## Paso 1 — Captura express (siempre)

Una sola llamada a `AskUserQuestion` con estas tres preguntas.

**1. ¿Qué hace el bloque?** — ofrece arquetipos y deja `Otro` para descripción libre:
`Contador regresivo` · `Slider antes/después` · `Acordeón / FAQ` · `Formulario de contacto` · `Listado de contenido (posts/CPT)` · `Testimonios` · `Tarjeta de precio / CTA` · `Calculadora` · `Mapa` · `Feed de API externa` · `Contenedor con bloques dentro`

**2. ¿Cómo se comporta?**
- `Estático` — solo muestra contenido configurable; el markup se guarda en el post
- `Interactivo en frontend` — necesita JS en la página pública (cuenta atrás, arrastrar, abrir/cerrar)
- `Renderizado en servidor` — el contenido depende del momento de la petición (últimos posts, datos que cambian)
- `Servidor + interactivo` — ambas cosas

**3. ¿Dónde vive?**
- `Plugin nuevo` · `Plugin existente` · `Tema` — si eligen tema, aplica la advertencia del Paso 4.

## Paso 2 — Ofrecer profundidad

Una pregunta multiSelect: *"¿Profundizamos o tiro con los defaults del arquetipo?"*
Opciones: `Atributos y controles del editor` · `Diseño visual y estilos` · `Datos y persistencia` · `Técnico (namespace, text domain, build)` · `Nada más, genera el brief`.

## Paso 3 — Bloques profundos (solo los elegidos)

### A. Atributos y controles del editor
- **Campos editables** (multiSelect): `Texto (título, descripción, etiqueta de botón)` · `Imagen o medio` · `Enlace / URL` · `Número (cantidad, duración, columnas)` · `Fecha/hora` · `Selector de opciones (variante, layout)` · `Toggle on/off`
- **Edición en canvas** (multiSelect): `Título con RichText` · `Descripción con RichText` · `Botón con RichText` · `Nada: todo en la barra lateral`
- **Barra lateral (`InspectorControls`)**: qué ajustes van ahí. Recuerda: color, tipografía y espaciado **no** se piden aquí, salen gratis con `supports` (Paso 4).
- **Bloques internos**: `No` · `Sí, contenido libre` · `Sí, lista cerrada de bloques permitidos` · `Sí, con plantilla fija (templateLock)`

### B. Diseño visual y estilos
- **Layout**: descripción en una frase de la disposición (columnas, orden, jerarquía).
- **Móvil**: `Sin requisitos` · `Apilar en una columna` · `Ocultar elementos concretos` · `Comportamiento distinto (táctil)`
- **Tratamiento**: `Plano, sin adornos` · `Bordes redondeados` · `Sombra` · `Degradado`
- **Variaciones de estilo** (`styles` en `block.json`): `No` · `2 variaciones (default + alternativa)` · `3+`

### C. Datos y persistencia
- **¿Guarda datos enviados por el visitante?** `No` · `Sí: envíos de formulario` · `Sí: reservas` · `Sí: valoraciones/votos`
- **¿De dónde lee?** `Solo de sus atributos` · `REST API del propio WordPress (posts, CPT, taxonomías)` · `API externa` · `Endpoint personalizado`
- **Si hay API externa**: pide dominio, si requiere clave y qué TTL de caché es aceptable. La clave nunca viaja al JS: ver Paso 4.
- **¿Necesita un CPT como modelo de datos?** `No` · `Sí, ya existe` · `Sí, hay que crearlo`

### D. Técnico
- **Nombre del bloque** (texto libre) → derivas `namespace/slug`
- **Namespace**: el del plugin o tema contenedor (`mi-plugin/contador`)
- **Text domain**: normalmente idéntico al slug del plugin o tema
- **¿Hay `package.json` con otros bloques?** `Sí, encadenar al build existente` · `No, scaffolding nuevo` · `No lo sé`
- **WordPress mínimo**: `6.5+` · `6.9+` · `7.0+`
- **Categoría del inserter**: `text` · `media` · `design` · `widgets` · `theme` · `embed` · categoría propia

## Paso 4 — Cortes de alcance

Aplica los tres **siempre** y refléjalos en el brief.

### 4.1 Lo que no se declara como atributo: ya lo da `supports`

Es el error más común de un brief de bloque. Si el usuario pide un color picker o un control de espaciado, no es un atributo: es una línea en `supports`.

| Petición | `block.json` |
|---|---|
| Color de texto y fondo | `supports.color` |
| Color de enlaces | `supports.color.link` |
| Degradado de fondo | `supports.color.gradients` |
| Tamaño, familia, peso, interlineado | `supports.typography` |
| Padding, margin, hueco entre hijos | `supports.spacing` |
| Alineación wide/full | `supports.align` |
| Borde y radio | `supports.border` (en WP antiguos fue `__experimentalBorder`; verifica contra el WP mínimo) |
| Sombra | `supports.shadow` (WP 6.3+) |
| Clase CSS propia / ancla | `supports.customClassName`, `supports.anchor` |
| Colocación de bloques hijos | `supports.layout` |

Atributo propio solo para datos que el bloque necesita y el editor no cubre: textos, URLs, fechas, IDs de medio, números y opciones específicas del bloque.

### 4.2 Bloque vs backend

| Requisito | Dónde vive |
|---|---|
| Markup, atributos, controles del editor | El bloque |
| Comportamiento en frontend | `viewScriptModule` + Interactivity API |
| Render dependiente de la petición | `render.php` del bloque |
| **Guardar envíos, reservas, votos** | **PHP del plugin**: CPT o tabla propia + `register_rest_route`, verificación de nonce, `current_user_can`, sanitizado y rate limiting. El bloque solo pinta el formulario y llama al endpoint |
| **Registrar un CPT** | **Plugin**, nunca el tema: es contenido, sobrevive al cambio de tema |
| **Envío de emails, antispam, exportación de leads** | Plugin de formularios existente (CF7, Gravity, Fluent). Construir esto a mano rara vez compensa |
| **Claves de API** | PHP, en constante o opción. Nunca en el bundle JS del bloque: cualquiera lo lee en el navegador. Llamada desde servidor + caché con `set_transient` |

### 4.3 Bloque en tema vs en plugin

Si el bloque aporta funcionalidad y no solo presentación, va en un plugin. Un bloque registrado por el tema deja de existir al cambiar de tema, y todo el contenido que lo usa queda como **"Invalid block"** en las entradas ya publicadas. Un bloque puramente decorativo y atado al diseño del tema es aceptable en el tema; cualquier cosa con datos, formularios o CPT, no.

## Paso 5 — Defaults por arquetipo

Rellena con esto lo no preguntado y marca `(inferred)`.

| Arquetipo | Modelo | `save()` | `render.php` | `viewScriptModule` | InnerBlocks | Persistencia | Datos externos |
|---|---|---|---|---|---|---|---|
| Contador regresivo | Estático + interactivo | sí | no | sí | no | no | no |
| Slider antes/después | Estático + interactivo | sí | no | sí | no | no | no |
| Acordeón / FAQ | Estático | sí | no | opcional | sí | no | no |
| Formulario de contacto | Dinámico + interactivo | `null` | sí | sí | no | **sí → backend** | no |
| Listado de posts/CPT | Dinámico | `null` | sí | no | no | no | REST del propio WP |
| Testimonios | Estático | sí | no | no | sí | no | no |
| Tarjeta precio / CTA | Estático | sí | no | no | opcional | no | no |
| Calculadora | Estático + interactivo | sí | no | sí | no | no | no |
| Mapa | Estático, o dinámico si hay clave | según | según | opcional | no | no | sí |
| Feed de API externa | Dinámico | `null` | sí | no | no | no | **sí + caché** |
| Contenedor | Estático | sí | no | no | **sí** | no | no |

Defaults transversales:
- `apiVersion: 3` siempre. WordPress 6.9 lo exige en el esquema de `block.json` y WP 7.0 ejecuta el editor en iframe con independencia de la versión del bloque.
- Registro en PHP desde los metadatos de `block.json` — necesario para render dinámico, traducciones y carga condicional de assets.
- i18n: todas las strings con el text domain del proyecto y `wp_set_script_translations` para las del editor.
- Un bloque interactivo usa Interactivity API (`supports.interactivity: true` + directivas `data-wp-*`), no jQuery ni un `viewScript` clásico, salvo que el proyecto ya imponga otra cosa.

Comandos de scaffolding (verifica los flags con `npx @wordpress/create-block --help` antes de ejecutar):
- Plugin nuevo estático: `npx @wordpress/create-block@latest <slug> --namespace <ns>`
- Dinámico: añade `--variant dynamic`
- Interactivo: `--template @wordpress/create-block-interactive-template`
- Dentro de un plugin existente: `--no-plugin` desde la carpeta de bloques

## Paso 6 — Escribir BLOCK-BRIEF.md

En inglés, entregado con `SendUserFile` (y a la carpeta conectada si la hay). Plantilla:

```markdown
# Block Brief — <Block Title>

> Handoff document for the `wp-block-development` skill. Values marked (inferred) were not
> explicitly confirmed and should be validated before build.

## 1. Identity
- Block name: `<namespace>/<slug>`
- Title / description: <…>
- Category: <text | media | design | widgets | theme | custom>
- Icon: <dashicon or custom SVG>
- Keywords: <…>
- Text domain: `<text-domain>`
- apiVersion: 3 · Minimum WordPress: <6.5+ | 6.9+ | 7.0+>
- Lives in: <plugin (new) | plugin (existing, path) | theme>

## 2. Behavior model
- Model: <static | dynamic | static + interactive | dynamic + interactive>
- `save()`: <serialized markup | null>
- `render.php`: <yes — why | no>
- Frontend JS: <viewScriptModule + Interactivity API | none>
- `supports.interactivity`: <true | false>
- Rationale: <one line — why this model and not the others>

## 3. Attributes
| name | type | source | default | edited in |
|---|---|---|---|---|
| `title` | string | `html` on `h3` | — | canvas (RichText) |
| `targetDate` | string | attribute | — | sidebar (DateTimePicker) |

## 4. Block supports (do NOT re-implement as attributes)
<list the supports lines that cover the client's styling asks>

## 5. Editor controls
- **Canvas (inline):** <RichText fields>
- **Sidebar (`InspectorControls`):** <panels and controls>
- **Toolbar (`BlockControls`):** <alignment, media replace, … | none>

## 6. Inner blocks
- Enabled: <yes | no>
- `allowedBlocks`: <…>
- `template` / `templateLock`: <…>
- Wrapper: `useInnerBlocksProps()`

## 7. Frontend markup & styling
- Layout: <…>
- Responsive: <…>
- Visual treatment: <…>
- Style variations (`styles` in block.json): <…>

## 8. Data & persistence
- Stores visitor input: <no | yes — what>
- Storage: <none | CPT `<name>` | custom table>
- Endpoints: <`register_rest_route` namespace/route, methods, permission_callback>
- Security: nonce verification, capability check, sanitization, rate limiting

## 9. External data
- Source: <none | core REST via @wordpress/core-data | custom endpoint | external API>
- API key handling: <none | server-side constant/option, never in JS>
- Caching: <`set_transient`, TTL …>
- Editor preview strategy: <ServerSideRender | REST fetch in edit.js | static placeholder>

## 10. Project context
- Repo root / target path: <…>
- Existing `package.json` with blocks: <yes — chain to existing build | no — scaffold>
- Build tooling: `@wordpress/scripts`
- Scaffold command: <…>

## 11. Out of block scope
| Requirement | Where it belongs | Owner |
|---|---|---|
| … | … | … |

## 12. Acceptance criteria
- Block appears in the inserter under <category> and inserts cleanly.
- Save + reload produces no "Invalid block".
- Every attribute persists across reload.
- Frontend output matches the editor preview.
- Editor and frontend assets load only where needed.
- All strings translatable with `<text-domain>`.
- Keyboard operable and labelled for screen readers.

## 13. Open questions
- <TBD>

## 14. Build order
`block.json` → PHP registration → `edit.js` → `save.js` or `render.php` → `view.js` (Interactivity)
→ `style.scss` / `editor.scss` → `styles` variations → i18n
```

## Paso 7 — Handoff y verificación

Antes de entregar, comprueba:

- [ ] El nombre es `namespace/slug` válido: minúsculas, guiones, sin acentos. Es API estable: cambiarlo después rompe el contenido existente.
- [ ] El modelo (estático / dinámico / interactivo) está justificado en una línea, no elegido por defecto.
- [ ] Ningún atributo duplica algo que ya cubre `supports`.
- [ ] Cada atributo tiene `type` y, si vive en el markup, `source` y `selector`.
- [ ] Si guarda datos del visitante, existe una fila de endpoint y otra de seguridad en la §8.
- [ ] Ninguna clave de API aparece del lado del cliente.
- [ ] `apiVersion: 3` declarado.
- [ ] Nada de §11 aparece como trabajo del bloque.
- [ ] Todo lo inferido está marcado `(inferred)`.

Cierra ofreciendo el paso siguiente: *"¿Lanzo `wp-block-development` con este brief?"* — y, si acepta, invoca esa skill pasando la ruta del `BLOCK-BRIEF.md`.
