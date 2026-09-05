---
name: wp-block-theme-brief
description: Entrevista guiada en español para definir un tema de bloques de WordPress y generar un BRIEF.md en inglés listo para entregar a la skill wp-block-themes. Úsala al arrancar, definir o acotar un tema WP nuevo.
---

# WP Block Theme Brief

Conduce una entrevista corta con el usuario y produce `BRIEF.md`: un documento en inglés que traduce lo que el usuario quiere a los artefactos reales de un tema de bloques (`theme.json`, `templates/`, `parts/`, `patterns/`, `styles/`), para que la skill `wp-block-themes` pueda construirlo sin volver a preguntar.

## Reglas de operación

- **Entrevista en español, entregable en inglés.** Identificadores técnicos (slug, text domain, nombres de fichero, slugs de color) siempre en inglés y en `kebab-case`.
- **Usa `AskUserQuestion`**, máximo 4 preguntas por llamada, con opciones concretas y ejemplos reales. Nunca pidas texto libre para algo que se puede ofrecer como opción.
- **No preguntes lo que ya te han dicho.** Si el mensaje inicial ya contiene el tipo de sitio, secciones o estilo, salta esas preguntas.
- **Todo lo no preguntado se infiere** con la tabla de defaults, y en el brief se marca `(inferred)` para que sea revisable de un vistazo.
- **No inventes datos de negocio** (nombres de cliente, URLs, copys reales). Si falta, escribe `TBD` en el brief.
- Si el usuario dice "lo mínimo" o "rápido", ejecuta solo el Paso 1 y salta al Paso 5.

## Paso 1 — Captura express (siempre)

Una sola llamada a `AskUserQuestion` con estas tres preguntas. Es el mínimo viable para construir un tema.

**1. ¿Qué tipo de sitio es?**
`Blog personal` · `Portafolio` · `Negocio local / servicios` · `Tienda (WooCommerce)` · `Restaurante` · `Agencia / consultora` · `Revista o medio` · `Landing de producto / SaaS`

**2. ¿Qué secciones quieres en la home?** (multiSelect)
`Hero con imagen y CTA` · `Servicios / características` · `Últimas entradas` · `Testimonios` · `Logos de clientes` · `Galería o portfolio destacado` · `Precios` · `FAQ` · `Newsletter` · `Mapa y horarios` · `CTA final de contacto`

**3. ¿Qué estilo visual?**
`Minimalista (mucho blanco, sans moderna)` · `Editorial (serif, columnas anchas, foto grande)` · `Corporativo (azules, retícula ordenada)` · `Elegante oscuro (fondo oscuro + acento dorado, serif)` · `Vibrante (colores saturados, tipografía grande)` · `Técnico (mono, alto contraste, bordes finos)`

## Paso 2 — Ofrecer profundidad

Una pregunta multiSelect: *"¿Profundizamos en algún bloque o tiro con defaults?"*
Opciones: `Estructura y páginas` · `Identidad visual` · `Funcionalidad e integraciones` · `Layout` · `Técnico (slug, WP mínimo, plugins)` · `Nada más, genera el brief`.

Solo ejecuta los bloques del Paso 3 que el usuario elija.

## Paso 3 — Bloques profundos (solo los elegidos)

### A. Estructura y páginas
- **Páginas clave** (multiSelect): `Inicio` · `Sobre nosotros` · `Servicios` · `Blog` · `Contacto` · `Portfolio / Casos` · `Precios` · `Legales (privacidad, cookies, aviso)`
- **Navegación**: `Menú plano (4-6 enlaces)` · `Con submenús` · `Menú + CTA destacado` · `Mega menú` (advierte: el mega menú no es nativo, requiere plugin)
- **Plantillas de archivo**: `Solo blog estándar` · `Blog + archivo por categoría` · `Blog + autor + búsqueda` · `Sin blog`

### B. Identidad visual
- **Paleta**: pide tono (`cálido` · `frío` · `neutro alto contraste` · `oscuro con acento`) o hex concretos si los tiene. Traduce siempre a 5 slugs: `base` (fondo), `contrast` (texto), `primary`, `secondary`, `accent`.
- **Tipografía**: `Serif clásica (ej. Source Serif, Libre Baskerville)` · `Sans moderna (ej. Inter, DM Sans)` · `Grotesk con carácter (ej. Space Grotesk)` · `Mono técnica (ej. JetBrains Mono)` · `Mixta: serif títulos + sans cuerpo`. Registra siempre **dos** familias como máximo (`heading` y `body`) salvo petición explícita.
- **Logo**: `Ya existe (SVG/PNG)` · `Reservar espacio, placeholder` · `Solo texto (site title)`
- **Densidad**: `Aire generoso` · `Compacta` · `Editorial ancha`

### C. Funcionalidad e integraciones
- **Interactivo** (multiSelect): `Formulario de contacto` · `Reservas / citas` · `Slider o carrusel` · `Calculadora` · `Buscador destacado` · `Filtros de catálogo` · `Ninguno`
- **Integraciones** (multiSelect): `Redes sociales` · `Google Maps` · `Vídeo embebido (YouTube/Vimeo)` · `Newsletter (Mailchimp/Brevo)` · `Analítica (GA4/GTM)`
- **Contenido personalizado**: `No hace falta` · `Sí: portfolio` · `Sí: testimonios` · `Sí: propiedades/recetas/otro`

Aplica siempre el **corte de alcance** del Paso 4 a las respuestas de este bloque.

### D. Layout
- **Ancho**: `Contenido estrecho (~640px) con bloques wide` · `Ancho medio (~800px)` · `Full-width con secciones a sangre`
- **Header** (multiSelect): `Logo` · `Menú` · `Buscador` · `CTA` · `Redes` · `Sticky`
- **Footer** (multiSelect): `Menú secundario` · `Datos de contacto` · `Redes` · `Newsletter` · `Legales` · `Créditos`
- **Sidebar**: `Sin sidebar` · `Sidebar en el blog` · `Sidebar en todo el sitio`
- **Móvil**: `Sin requisitos especiales` · `Menú off-canvas` · `Ocultar secciones concretas en móvil`

### E. Técnico
- **Nombre del tema** (texto libre → derivas slug y text domain)
- **WordPress mínimo**: `6.6+ (theme.json v3)` · `6.7+` · `7.0+`
- **Compatibilidad** (multiSelect): `WooCommerce` · `Multiidioma (Polylang/WPML)` · `Formularios (CF7/Gravity)` · `SEO (Yoast/RankMath)` · `Ninguno`
- **Variaciones de estilo**: `Solo una` · `Clara + oscura` · `3+ variaciones`

## Paso 4 — Corte de alcance: tema vs plugin

Aplica esto **siempre** y refléjalo en el brief. Es el error más caro de un brief mal hecho.

| Petición del usuario | Dónde vive realmente |
|---|---|
| Colores, tipografía, espaciado, layout | `theme.json` — tema |
| Cabecera, pie, plantillas de página/entrada | `parts/`, `templates/` — tema |
| Secciones de home (hero, testimonios, precios) | `patterns/*.php` — tema |
| Modo claro/oscuro, temas alternativos | `styles/*.json` — tema |
| Formulario de contacto / reservas | **Plugin** (CF7, Gravity, Fluent Forms). El tema solo aporta el patrón contenedor y los estilos |
| CPT y taxonomías (portfolio, testimonios, propiedades) | **Plugin** — es contenido, no presentación. Si van en el tema, el cliente pierde los datos al cambiarlo |
| Slider, calculadora, filtros dinámicos | **Bloque personalizado en plugin** o bloque de terceros |
| Mapas, newsletter, analítica | **Plugin o embed** |
| Sidebar clásico / widget areas | No existe en temas de bloques: se resuelve con columnas o un template part en la plantilla |

En el brief, lo que caiga en la columna derecha va a la sección `Out of theme scope` con el plugin propuesto, no a la lista de trabajo del tema.

## Paso 5 — Defaults por tipo de sitio

Usa esto para todo lo que no se haya preguntado. Marca cada valor derivado como `(inferred)`.

| Tipo | Templates | Home sections | contentSize / wideSize |
|---|---|---|---|
| Blog personal | `index`, `single`, `page`, `archive`, `search`, `404` | Hero corto, últimas entradas, sobre mí | 660px / 1200px |
| Portafolio | `front-page`, `index`, `page`, `single`, `404` | Hero, grid de proyectos, sobre mí, contacto | 720px / 1280px |
| Negocio local | `front-page`, `page`, `index`, `single`, `404` | Hero, servicios, testimonios, mapa y horarios, CTA | 720px / 1200px |
| Restaurante | `front-page`, `page`, `index`, `404` | Hero, carta destacada, galería, testimonios, ubicación | 720px / 1280px |
| Tienda | `front-page`, `page`, `index`, `single`, `404` + plantillas Woo | Hero, categorías, destacados, ventajas, newsletter | 720px / 1320px |
| Agencia | `front-page`, `page`, `index`, `single`, `archive`, `404` | Hero, servicios, logos, casos, equipo, CTA | 720px / 1280px |
| Revista | `front-page`, `home`, `index`, `single`, `archive`, `author`, `search`, `404` | Portada destacada, secciones por categoría, más leídos | 800px / 1400px |

Defaults transversales: `parts/header.html` + `parts/footer.html` siempre; `styles/` con al menos una variación si el usuario pidió modo oscuro; `theme.json` versión 3 salvo que el WP mínimo sea < 6.6.

Derivación técnica automática:
- `slug` = nombre en minúsculas, sin acentos, con guiones (`Trattoria Bella` → `trattoria-bella`)
- `text domain` = slug (idéntico)
- prefijo de patrones = `slug/nombre-del-patron`

## Paso 6 — Escribir BRIEF.md

Escribe el fichero en inglés y entrégalo con `SendUserFile`. Si hay carpeta conectada, guárdalo también ahí. Plantilla:

```markdown
# Block Theme Brief — <Theme Name>

> Handoff document for the `wp-block-themes` skill. Values marked (inferred) were not
> explicitly confirmed by the client and should be validated before build.

## 1. Identity
- Theme name: <Name>
- Slug / text domain: <slug>
- Site type: <type>
- Audience: <who>
- Primary content: <posts | projects | products | services | recipes>
- Minimum WordPress: <6.6+> · theme.json version: <3>
- License: GPL-2.0-or-later

## 2. Design direction
- Overall style: <minimal | editorial | corporate | dark elegant | vibrant | technical>
- Palette (theme.json `settings.color.palette`):
  | slug | role | value |
  |---|---|---|
  | base | background | #FFFFFF |
  | contrast | text | #111111 |
  | primary | brand / links | #… |
  | secondary | supporting | #… |
  | accent | highlights, CTA | #… |
- Typography (`settings.typography.fontFamilies`):
  - heading: <family>, fallback <stack>
  - body: <family>, fallback <stack>
  - Font delivery: <bundled in assets/fonts + fontFace | Font Library | system stack>
- Logo: <existing asset | placeholder | site title only>
- Layout: contentSize <…px>, wideSize <…px>
- Spacing: <default WP scale | custom scale>

## 3. Templates (`templates/`)
| File | Purpose |
|---|---|
| index.html | Required fallback |
| front-page.html | Home |
| … | … |

## 4. Template parts (`parts/`)
- header.html — <logo, nav, CTA, sticky?>
- footer.html — <columns, contact, social, legal>
- <other> — <purpose>

## 5. Patterns (`patterns/`)
Home sections, in order:
1. `<slug>/hero` — <description, blocks used>
2. `<slug>/services` — …
3. …
Each pattern header must declare Title, Slug, Categories, and Inserter visibility.

## 6. Style variations (`styles/`)
- <default> — …
- <dark> — …

## 7. Navigation & content structure
- Pages: <list>
- Menu: <flat | with submenus> — items: <list>
- Sidebar: <none | blog only | site-wide, implemented as columns>

## 8. Responsive notes
<mobile-specific requirements>

## 9. Plugin compatibility
- <WooCommerce | forms plugin | multilingual | SEO | none>
- Templates required by those plugins: <list>

## 10. Out of theme scope (needs a plugin)
| Requirement | Proposed solution | Owner |
|---|---|---|
| Contact form | Contact Form 7 / Fluent Forms; theme provides the container pattern + styles | <client | us> |
| Portfolio CPT | Custom plugin — content must survive a theme change | <client | us> |

## 11. Open questions
- <TBD items>
```

## Paso 7 — Handoff y verificación

Antes de entregar, comprueba:

- [ ] Cada sección de la home tiene un patrón nombrado en el brief.
- [ ] Existe `index.html` en la lista de templates (es obligatorio en un tema de bloques).
- [ ] Slug y text domain coinciden y son válidos (minúsculas, guiones, sin acentos).
- [ ] Los 5 slugs de color están definidos con valor hex, no con adjetivos.
- [ ] Nada de la sección `Out of theme scope` aparece como trabajo del tema.
- [ ] Todo lo inferido está marcado `(inferred)`.

Cierra ofreciendo el paso siguiente: *"¿Lanzo `wp-block-themes` con este brief?"* — y, si acepta, invoca esa skill pasando la ruta del `BRIEF.md` y el orden de construcción: `style.css` → `theme.json` → `parts/` → `templates/` → `patterns/` → `styles/`.
