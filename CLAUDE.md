# Recipe Box

A personal recipe website. Plain HTML/CSS/JS, no build step — just open `index.html` in a browser.

## Folder structure

```
index.html                          Home page — Foods / Drinks accordion, expands to link straight to each subcategory
assets/style.css                    Shared stylesheet for index & listing pages
foods/<subcategory>/index.html      Recipe list for that subcategory
foods/<subcategory>/<recipe>.html   A single recipe page
drinks/<subcategory>/index.html     Recipe list for that subcategory
drinks/<subcategory>/<recipe>.html  A single recipe page
```

There is no `foods/index.html` or `drinks/index.html` — the home page's accordion (click "Foods" or "Drinks" to expand) links directly to each subcategory's `index.html`, so that extra in-between page isn't needed.

Categories and subcategories are fixed:
- **Foods**: `western`, `italian`, `chinese`, `japanese`
- **Drinks**: `fruit-tea`, `tea`, `coffee`

Recipe filenames are lowercase-kebab-case, e.g. `mushroom-alfredo-spaghetti.html`.

## Adding a new recipe

1. Save the recipe page as `<category>/<subcategory>/<recipe-name>.html`, following the "Recipe page rules" below.
2. Open `<category>/<subcategory>/index.html` and add a card inside `<section class="shelf">`:
   ```html
   <a class="card" href="<recipe-name>.html" style="--strip:#7a3e2f">
     <span class="pour"></span>
     <span class="body">
       <h3>Recipe Title</h3>
       <p>One-sentence description of the dish.</p>
     </span>
     <span class="go">&rarr;</span>
   </a>
   ```
3. If the subcategory page currently shows the "Nothing on this shelf yet" empty state, replace that `<div class="emptyState">` block with the `<section class="shelf">` block above.
4. Pick `--strip` from the accent colors table below so the card matches its category.

No other file needs to change — the home page never needs to be touched when adding a recipe.

## Breadcrumbs on subcategory pages

Each `<category>/<subcategory>/index.html` shows a two-part breadcrumb, not three:

```html
<nav class="crumb">
  <a href="../../index.html">&larr; Home</a>
  <span class="sep">/</span>
  <span class="here">Foods - Western</span>
</nav>
```

`Foods - Western` is one plain-text label (category and subcategory joined with " - "), not two separate links — there's no intermediate category page to link to.

## Accent colors

| Category / Subcategory | Color |
|---|---|
| Foods (home card) | `#b5502f` |
| Western | `#8a5a3d` |
| Italian | `#7a3e2f` |
| Chinese | `#a4123f` |
| Japanese | `#5c7a5a` |
| Drinks (home card) | `#5c7a5a` |
| Fruit Tea | `#a4123f` |
| Tea | `#5c7a5a` |
| Coffee | `#6f4a2f` |

## Recipe page rules

Each recipe page is a **single self-contained HTML file** (its own `<style>` and `<script>`, no external stylesheet) so a recipe can be shared or opened on its own. Every recipe page must include:

1. **Header** — dish name as `<h1>`, a one-sentence tagline, and meta info (prep/cook time or similar) as small chips or labeled fields.
2. **Servings/batch control** — a stepper (`-` / `+` buttons, or +/- with a range slider) that lets the reader scale the recipe. Ingredient amounts must re-render via JavaScript when this changes (see the `renderIngredients()` pattern in existing recipes) — never hardcode a single serving size.
3. **Ingredients list** — each item shows a bold/colored amount + the ingredient name.
4. **Method** — numbered steps, each with a short bold title followed by the instruction text. Steps with a wait/cook time get an optional countdown timer widget (Start/Reset buttons, ticks down, beeps and highlights on completion).
5. **Notes** — a closing section with tips, substitutions, or storage info.
6. **Print styles** (`@media print`) that hide interactive controls (steppers, timer buttons) and simplify the layout for printing/saving as PDF.
7. **Responsive layout** (`@media (max-width: ...)`) so the page is usable on a phone screen.

Visual theme (fonts, colors, card vs. flowing layout) can vary per recipe to fit the dish — the two existing recipes (`foods/italian/mushroom-alfredo-spaghetti.html` and `drinks/fruit-tea/forest-berry-hot-tea.html`) are both valid examples of this template with different themes. What must stay consistent is the **structure** above (header → servings → ingredients → method → notes) and the interactive behavior (servings scaling, timers, print-friendliness).

## Listing/index pages

`index.html` and every `<category>/<subcategory>/index.html` share one stylesheet: `assets/style.css`. Don't add page-specific `<style>` blocks to these — edit `assets/style.css` if the shared look needs to change, so every listing page updates together.
