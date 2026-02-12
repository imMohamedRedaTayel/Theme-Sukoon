# Qumra Cloud Theme AI Context

> Copy this file to `.claude/memory/MEMORY.md` in any Qumra theme project to help AI understand the theming system.

---

## Context

You are working in a **Qumra Cloud** e-commerce theme project using the **Qalab** templating system.

**IMPORTANT:** All documentation is in the `docs/` folder - **READ IT** before making any changes.

## Tech Stack

| Technology | Purpose |
|------------|---------|
| Nunjucks (.njk) | Template engine |
| Tailwind CSS | Utility CSS framework |
| Alpine.js | Lightweight reactive JS |
| Swiper.js | Carousels/sliders |
| GLightbox | Image galleries |
| Tajawal/Poppins | Arabic/English fonts |

## Project Structure

```
├── .qumra/              # Theme config (qumra.config.json)
├── assets/              # CSS, JS, images
├── docs/                # FULL DOCUMENTATION
│   ├── qalab/           # Template language docs
│   ├── theme-structure/ # Structure docs
│   ├── store-ajax/      # API docs
│   └── examples/        # Code examples
├── layouts/             # Main HTML (layout.njk)
├── locales/             # Translations (ar/en/fr.json)
├── pages/               # Page definitions
├── settings/            # Theme settings
├── templates/           # Header/Footer
├── ui/                  # Shared components
└── widgets/             # Widget components
    └── widget-name/
        ├── schema.json  # Settings definition
        └── widget.njk   # Template
```

## Qalab Quick Reference

### Global Variables
```nunjucks
{{ store }}              {# Store data (name, logo, url) #}
{{ cart }}               {# Cart (items, totalPrice, totalQuantity) #}
{{ wishlist }}           {# Wishlist (products, count) #}
{{ localization }}       {# Language, currency, exchange rates #}
{{ settings }}           {# Theme settings #}
{{ product }}            {# Product page data #}
{{ collection }}         {# Category page data #}
{{ widget.data }}        {# Current widget settings #}
{{ widget.blocks }}      {# Widget repeatable blocks #}
{{ page }}               {# Current page info #}
```

### Custom Tags
```nunjucks
{% seo %}                {# Auto SEO meta tags #}
{% qumra_head %}         {# Qumra resources in <head> #}
{% qumra_scripts %}      {# Qumra runtime scripts #}
{% template "header" %}  {# Include header/footer template #}
{% content %}            {# Render page widgets #}
{% ui "file.njk" %}      {# Include UI component #}
{{t('key.path')}}        {# Get translation - NOTE: double braces, NOT {% %} #}
```

### Filters
```nunjucks
| money          {# Format price: 244500 → "2,445.00 ر.س" #}
| assets         {# Convert to CDN URL: 'style.css' → full URL #}
| safe           {# Render HTML without escaping #}
| default(val)   {# Fallback if empty #}
| length         {# Array/string length #}
| first / last   {# First/last item #}
| join(',')      {# Join array #}
| truncate(50)   {# Truncate string #}
```

## Widget Schema Types

| Type | Description |
|------|-------------|
| `string` | Single line text |
| `text` | Multi-line text (use `\| safe` for HTML) |
| `number` | Numeric (supports min, max, step) |
| `boolean` | Toggle on/off |
| `color` | Color picker |
| `select` | Dropdown options |
| `range` | Slider |
| `media` | Image/file upload |
| `menu` | Menu selection |
| `products` | Product picker |
| `collections` | Category picker |
| `css` | CSS code editor |
| `javascript` | JS code editor |

## Store AJAX API

### JavaScript Managers
```javascript
// Cart operations
CartManager.add(productId, qty, variantId)
CartManager.increment(itemId, currentQty)
CartManager.decrement(itemId, currentQty)
CartManager.update(itemId, newQty)
CartManager.remove(itemId)
CartManager.clear()

// Wishlist operations
WishlistManager.add(productId)
WishlistManager.remove(productId)
WishlistManager.toggle(productId)
WishlistManager.clear()

// Search
SearchManager.search(query, { page, limit })
SearchManager.suggest(query, callback, debounceMs)

// Product
ProductManager.get(productId)
ProductManager.getVariant(productId, selectedOptions)
```

### Events
```javascript
window.addEventListener('cart:updated', (e) => {
  console.log(e.detail); // { items, totalPrice, totalQuantity }
});

window.addEventListener('wishlist:updated', (e) => {
  console.log(e.detail);
});
```

## Key Rules

### Prices
All prices from API are in **smallest units** (fils/cents):
- `244500` = `2,445.00 SAR`
- **Always** use `| money` filter for display

### RTL Support
```nunjucks
{# In layout.njk #}
<html dir="{% if localization.language == 'en' %}ltr{% else %}rtl{% endif %}">

{# Use logical properties in CSS #}
margin-inline-start: 1rem;  {# NOT margin-left #}
padding-inline-end: 1rem;   {# NOT padding-right #}
```

### Manual Library Imports
These must be added manually to `layout.njk` (NOT included by `{% qumra_head %}`):
- Tailwind CSS CDN
- Google Fonts (Tajawal, Poppins)
- Swiper.js CSS & JS
- GLightbox CSS & JS
- Alpine.js

### Translations
```nunjucks
{# Correct #}
{{t('product.add_to_cart')}}

{# WRONG - don't use {% %} for translations #}
{% t('product.add_to_cart') %}
```

## Documentation Reference

| Need to know about... | Read this file |
|-----------------------|----------------|
| Available variables | `docs/qalab/variables.md` |
| All filters | `docs/qalab/filters.md` |
| Custom tags | `docs/qalab/tags.md` |
| Creating widgets | `docs/theme-structure/widgets.md` |
| Page definitions | `docs/theme-structure/pages.md` |
| Theme settings | `docs/theme-structure/settings.md` |
| Layout structure | `docs/theme-structure/layouts.md` |
| Cart API | `docs/store-ajax/cart.md` |
| Wishlist API | `docs/store-ajax/wishlist.md` |
| Search API | `docs/store-ajax/search.md` |
| Product API | `docs/store-ajax/product.md` |
| Code examples | `docs/examples/*.md` |
| Alpine.js patterns | `docs/qalab/alpine-integration.md` |

## Common Patterns

### Product Card Loop
```nunjucks
{% for product in widget.data.products %}
  <div class="product-card">
    <img src="{{ product.image.fileUrl }}" alt="{{ product.title }}">
    <h3>{{ product.title }}</h3>
    <p>{{ product.price | money }}</p>
    <button onclick="CartManager.add('{{ product._id }}', 1)">
      {{t('product.add_to_cart')}}
    </button>
  </div>
{% endfor %}
```

### Stock Check
```nunjucks
{% if product.quantity > 0 %}
  <button class="btn-primary">{{t('product.add_to_cart')}}</button>
{% else %}
  <span class="text-red-500">{{t('product.out_of_stock')}}</span>
{% endif %}
```

### Sale Badge
```nunjucks
{% if product.compareAtPrice and product.compareAtPrice > product.price %}
  <span class="sale-badge">{{t('product.sale')}}</span>
{% endif %}
```

### Responsive Images
```nunjucks
<img
  src="{{ product.image.fileUrl }}"
  alt="{{ product.title }}"
  loading="lazy"
  class="w-full h-auto object-cover"
>
```

## Before Making Changes

1. **Read relevant docs** in `docs/` folder first
2. **Check existing patterns** in similar widgets/pages
3. **Maintain RTL support** - use logical CSS properties
4. **Use translations** - `{{t('key')}}` not hardcoded text
5. **Format prices** - always use `| money` filter
6. **Test in both languages** - Arabic (RTL) and English (LTR)

---

*This context file helps AI assistants understand the Qumra Cloud theming system for better code generation and assistance.*
