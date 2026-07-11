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
2. Open `<category>/<subcategory>/index.html` and add a card:
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
   - If the page has a plain `<section class="shelf">`, add the card there directly.
   - If the page uses the Cold Brew / Hot Brew accordion instead (currently `drinks/fruit-tea` and `drinks/coffee` — see "Cold Brew / Hot Brew accordion" below), add the card inside the `<section class="shelf">` within the matching `#panel-cold` or `#panel-hot` panel.
3. If the target shelf currently shows the "Nothing on this shelf yet" empty state, replace that `<div class="emptyState">` block with a `<section class="shelf">` containing the card above.
4. Pick `--strip` from the accent colors table below so the card matches its category.

No other file needs to change — the home page never needs to be touched when adding a recipe.

## Cold Brew / Hot Brew accordion

`drinks/fruit-tea/index.html` and `drinks/coffee/index.html` split their shelf into two expandable sections instead of one flat list, since both categories have a natural cold/hot split:

```html
<section class="accordion">
  <div class="acc-item" style="--accent:#3d6b99">
    <button class="acc-header" aria-expanded="false" aria-controls="panel-cold">
      <span class="ch">🧊</span>
      <span class="label">
        <h2>Cold Brew</h2>
        <span class="blurb">...</span>
      </span>
      <span class="chevron">&#9662;</span>
    </button>
    <div class="acc-panel" id="panel-cold">
      <div class="acc-panel-inner">
        <section class="shelf">
          <!-- cold brew recipe cards -->
        </section>
      </div>
    </div>
  </div>

  <div class="acc-item" style="--accent:#c9622f">
    <button class="acc-header" aria-expanded="false" aria-controls="panel-hot">
      <span class="ch">🔥</span>
      <span class="label">
        <h2>Hot Brew</h2>
        <span class="blurb">...</span>
      </span>
      <span class="chevron">&#9662;</span>
    </button>
    <div class="acc-panel" id="panel-hot">
      <div class="acc-panel-inner">
        <!-- either a <section class="shelf"> of cards, or an <div class="emptyState"> if empty -->
      </div>
    </div>
  </div>
</section>

<script>
  document.querySelectorAll(".acc-header").forEach(function(btn){
    btn.addEventListener("click", function(){
      var item = btn.closest(".acc-item");
      var open = item.classList.toggle("open");
      btn.setAttribute("aria-expanded", open ? "true" : "false");
    });
  });
</script>
```

Only split a subcategory into this accordion once it has (or will soon have) recipes on both sides — a single flat `<section class="shelf">` is the default for every other subcategory page and needs no extra script. `--accent` is `#3d6b99` (cold) / `#c9622f` (hot) by convention; reuse those exact colors if this pattern gets added elsewhere.

## Breadcrumbs

**Subcategory pages** (`<category>/<subcategory>/index.html`) show a two-part breadcrumb, not three:

```html
<nav class="crumb">
  <a href="../../index.html">&larr; Home</a>
  <span class="sep">/</span>
  <span class="here">Foods - Western</span>
</nav>
```

`Foods - Western` is one plain-text label (category and subcategory joined with " - "), not two separate links — there's no intermediate category page to link to.

**Recipe pages** (`<category>/<subcategory>/<recipe>.html`) also get a breadcrumb, but here both parts are links, since the subcategory's `index.html` genuinely exists one level up in the same folder:

```html
<nav class="crumb">
  <a href="../../index.html">&larr; Home</a>
  <span class="sep">/</span>
  <a href="index.html">Foods - Western</a>
</nav>
```

Since recipe pages are self-contained (no shared stylesheet), add matching `.crumb`/`.crumb a`/`.crumb .sep` CSS directly in that recipe's own `<style>` block, reusing its existing `--muted`/`--line`/`--ink` variables (every recipe page defines these three, regardless of visual theme) so the breadcrumb always fits the page's palette without needing new variables.

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

For **Western, Italian, Chinese, and Coffee**, every recipe card's `--strip` uses that exact table color — pick it verbatim.

**Fruit Tea is the one exception**: since its recipes are visually distinct (different fruits/flowers), each card instead uses its own thematic color (or a two-color `linear-gradient(160deg, colorA, colorB)` for blends), rather than the flat `#a4123f` for every card — see `drinks/fruit-tea/index.html` for examples. Use this per-recipe approach only where a subcategory's recipes are similarly visually distinct from each other; default to the flat table color otherwise.

## Recipe page rules

Each recipe page is a **single self-contained HTML file** (its own `<style>` and `<script>`, no external stylesheet) so a recipe can be shared or opened on its own. Every recipe page must include:

1. **Breadcrumb** — see the "Breadcrumbs" section above for the exact markup (`Home` and `Foods - Western`, both links).
2. **Header** — dish name as `<h1>`, a one-sentence tagline, and meta info (prep/cook time or similar) as small chips or labeled fields.
3. **Servings/batch control** — a stepper (`-` / `+` buttons, or +/- with a range slider) that lets the reader scale the recipe. Ingredient amounts must re-render via JavaScript when this changes (see the `renderIngredients()` pattern in existing recipes) — never hardcode a single serving size.
4. **Ingredients list** — each item shows a bold/colored amount + the ingredient name. Use **metric units** (g, ml, °C) rather than imperial (oz, lb, cups, °F) — `tsp`/`tbsp` are fine for small volumes since they're used in both systems. Convert any imperial amounts from a source recipe to metric before adding it. Give the amount element `flex-shrink: 0` in its CSS — without it, longer values like "2 ½ tbsp" can get compressed by the flex layout and visually overlap the ingredient name next to it.
5. **Method** — numbered steps, each with a short bold title followed by the instruction text. Steps with a wait/cook time get an optional countdown timer widget (Start/Reset buttons, ticks down, beeps and highlights on completion).
6. **Notes** — a closing section with tips, substitutions, or storage info. If the recipe was adapted from an external site or video, add a final line/bullet: `Recipe adapted from <a href="URL" target="_blank" rel="noopener noreferrer">Site Name</a>.` — link out with `target="_blank" rel="noopener noreferrer"` since it leaves the site. Skip this line entirely for recipes that weren't sourced from an external page (e.g. ones written from scratch or provided directly). Add a `.notes a{ color: var(--accent); text-decoration: underline; }` rule (swap `--accent` for that page's own accent-ish variable, e.g. `--ruby`) to its `<style>` block so the link is visible against the notes background.
7. **Print styles** (`@media print`) that hide interactive controls (steppers, timer buttons) and simplify the layout for printing/saving as PDF.
8. **Responsive layout** (`@media (max-width: ...)`) so the page is usable on a phone screen.

Visual theme (fonts, colors, card vs. flowing layout) can vary per recipe to fit the dish — the two existing recipes (`foods/italian/mushroom-alfredo-spaghetti.html` and `drinks/fruit-tea/forest-berry-hot-tea.html`) are both valid examples of this template with different themes. What must stay consistent is the **structure** above (header → servings → ingredients → method → notes) and the interactive behavior (servings scaling, timers, print-friendliness).

## Listing/index pages

`index.html` and every `<category>/<subcategory>/index.html` share one stylesheet: `assets/style.css`. Don't add page-specific `<style>` blocks to these — edit `assets/style.css` if the shared look needs to change, so every listing page updates together.
