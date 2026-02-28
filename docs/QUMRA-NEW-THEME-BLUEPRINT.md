# Qumra Theme Blueprint — دليل بناء ثيم جديد

> ابعت الملف ده في شات جديد مع Claude وقوله: "عايز ابني ثيم Qumra Cloud جديد اسمه [اسم الثيم] بالهوية دي: [وصف الهوية]. استخدم البلوبرنت ده كمرجع."

---

## 1. معلومات المنصة (Qumra Cloud)

### Tech Stack
| Technology | Purpose |
|---|---|
| Nunjucks (.njk) | Template engine |
| Tailwind CSS CDN | Utility CSS |
| Alpine.js | Reactive JS |
| Swiper.js | Carousels |
| GLightbox | Image lightbox |

### هيكل المشروع
```
theme-name/
├── .qumra/qumra.config.json    ← معرف الثيم
├── assets/
│   ├── style.css               ← CSS variables + base styles
│   └── main.js                 ← Alpine components + managers
├── layouts/layout.njk          ← layout واحد بس
├── locales/
│   ├── ar.json                 ← ترجمات عربي
│   └── en.json                 ← ترجمات إنجليزي
├── pages/                      ← تعريفات الصفحات
│   ├── index.json
│   ├── shop.json
│   ├── product.json
│   ├── cart.json
│   ├── collections.json
│   ├── products-collection.json
│   ├── about.json
│   ├── blog.json
│   ├── blogs.json
│   ├── article.json
│   └── page.json
├── settings/settings-schema.json  ← إعدادات الثيم العامة
├── templates/
│   ├── header.json             ← Header group
│   └── footer.json             ← Footer group
├── ui/                         ← Shared partials
│   ├── product-card.njk
│   ├── cart.njk
│   ├── search.njk
│   ├── sidebar.njk
│   ├── filter-sidebar.njk
│   ├── filter-mobile.njk
│   ├── pagination.njk
│   ├── button.njk
│   ├── empty.njk
│   └── loading.njk
└── widgets/                    ← كل widget = مجلد
    └── widget-name/
        ├── schema.json         ← إعدادات الويدجت
        └── widget.njk          ← القالب
```

---

## 2. أخطاء لازم تتجنبها (اتعلمناها بالصعب)

### الكلمات المحجوزة في Schema
- **`layout`** محجوز من المنصة (pages/*.json بيستخدمه) — استخدم `display_layout` أو `hero_layout`
- **`rich-text`** مش مدعوم في blocks — استخدم `textarea`

### الـ API
- **`?widgetKey=name`** بيرجع HTML مش JSON — استخدم `res.text()` مش `res.json()`
- **`qumra.filters.search()`** مش بيشتغل للبحث عن منتجات — استخدم `/ajax/search/products?q=...`
- **`qumra.filters.sort()` / `.toggle()` / `.range()` / `.clear()`** شغالين تمام مع `data-qumra-section`
- **`qumra.filters.section(name)`** لازم تتنادى في `init()` لتسجيل السكشن
- الأسعار بالوحدة الأصغر (هللة/فلس) — دايماً استخدم `| money` filter

### RTL
- CSS Grid في RTL: أول عنصر = **يمين** (start)، تاني عنصر = **شمال** (end)
- لو اليوزر اختار "صورة يمين" في البلدر، في RTL ده أول grid item
- استخدم `start-*`/`end-*` بدل `left-*`/`right-*`
- استخدم `ps-*`/`pe-*` بدل `pl-*`/`pr-*`
- الأسهم اللي بتشاور: ضيف `rtl:rotate-180`

---

## 3. الملفات الأساسية (بالترتيب)

### 3.1 — `settings/settings-schema.json`
```json
[
  {
    "label": { "ar": "الألوان", "en": "Colors" },
    "settings": {
      "custom_colors": {
        "type": "boolean",
        "label": { "ar": "ألوان مخصصة (أغلقه للرجوع للتلقائي)", "en": "Custom Colors (turn off to reset)" },
        "default": false
      },
      "primaryColor": {
        "type": "color",
        "label": { "ar": "اللون الأساسي", "en": "Primary Color" },
        "default": "#00BCD4"
      },
      "primaryColorLight": {
        "type": "color",
        "label": { "ar": "اللون الأساسي الفاتح", "en": "Primary Color Light" },
        "default": "#B2EBF2"
      },
      "primaryColorDark": {
        "type": "color",
        "label": { "ar": "اللون الأساسي الغامق", "en": "Primary Color Dark" },
        "default": "#00838F"
      },
      "secondaryColor": {
        "type": "color",
        "label": { "ar": "اللون الثانوي", "en": "Secondary Color" },
        "default": "#C9A96E"
      },
      "secondaryColorLight": {
        "type": "color",
        "label": { "ar": "اللون الثانوي الفاتح", "en": "Secondary Color Light" },
        "default": "#E8DCC8"
      },
      "backgroundColor": {
        "type": "color",
        "label": { "ar": "لون الخلفية", "en": "Background Color" },
        "default": "#F8FAFB"
      },
      "surfaceColor": {
        "type": "color",
        "label": { "ar": "لون السطح", "en": "Surface Color" },
        "default": "#FFFFFF"
      },
      "textColor": {
        "type": "color",
        "label": { "ar": "لون النص الرئيسي", "en": "Text Color" },
        "default": "#1A2C32"
      },
      "textSecondaryColor": {
        "type": "color",
        "label": { "ar": "لون النص الثانوي", "en": "Secondary Text Color" },
        "default": "#546E7A"
      },
      "borderColor": {
        "type": "color",
        "label": { "ar": "لون الحدود", "en": "Border Color" },
        "default": "#D4DFE2"
      }
    }
  },
  {
    "label": { "ar": "الخطوط", "en": "Fonts" },
    "settings": {
      "fontArabic": {
        "type": "select",
        "label": { "ar": "الخط العربي", "en": "Arabic Font" },
        "default": "IBM Plex Sans Arabic",
        "options": [
          { "value": "IBM Plex Sans Arabic", "label": "IBM Plex Arabic" },
          { "value": "Tajawal", "label": "Tajawal" },
          { "value": "Cairo", "label": "Cairo" },
          { "value": "Almarai", "label": "Almarai" }
        ]
      },
      "fontEnglish": {
        "type": "select",
        "label": { "ar": "الخط الإنجليزي", "en": "English Font" },
        "default": "DM Sans",
        "options": [
          { "value": "DM Sans", "label": "DM Sans" },
          { "value": "Poppins", "label": "Poppins" },
          { "value": "Inter", "label": "Inter" },
          { "value": "Montserrat", "label": "Montserrat" }
        ]
      }
    }
  }
]
```

### 3.2 — `layouts/layout.njk` (الهيكل الكامل)
```njk
<!DOCTYPE html>
<html lang="{{ localization.language | default('ar') }}" dir="{{ 'ltr' if localization.language == 'en' else 'rtl' }}">
  <head>
    <meta charset="UTF-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>

    {# Google Fonts — Dynamic from settings #}
    {% set fontAr = settings.fontArabic | default('IBM Plex Sans Arabic') %}
    {% set fontEn = settings.fontEnglish | default('DM Sans') %}
    <link rel="preconnect" href="https://fonts.googleapis.com"/>
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin/>
    <link href="https://fonts.googleapis.com/css2?family={{ fontAr | replace(' ', '+') }}:wght@300;400;500;600;700&family={{ fontEn | replace(' ', '+') }}:wght@300;400;500;600;700&display=swap" rel="stylesheet"/>

    {% seo %}
    {% qumra_head %}

    {# Theme CSS #}
    <link rel="stylesheet" href="{{ 'style.css' | assets }}"/>

    {# Dynamic overrides from builder settings #}
    <style>
      :root {
        {% if settings.custom_colors %}
        --color-accent: {{ settings.primaryColor | default('#00BCD4') }};
        --color-primary: {{ settings.primaryColor | default('#00BCD4') }};
        --color-accent-hover: {{ settings.primaryColorDark | default('#00838F') }};
        --color-primary-dark: {{ settings.primaryColorDark | default('#00838F') }};
        --color-accent-light: {{ settings.primaryColorLight | default('#B2EBF2') }};
        --color-border-focus: {{ settings.primaryColor | default('#00BCD4') }};
        --color-warning: {{ settings.secondaryColor | default('#C9A96E') }};
        --color-bg-primary: {{ settings.backgroundColor | default('#F8FAFB') }};
        --color-surface: {{ settings.surfaceColor | default('#FFFFFF') }};
        --color-section-a: {{ settings.surfaceColor | default('#FFFFFF') }};
        --color-header: {{ settings.surfaceColor | default('#FFFFFF') }};
        --color-text-primary: {{ settings.textColor | default('#1A2C32') }};
        --color-header-text: {{ settings.textColor | default('#1A2C32') }};
        --color-text-secondary: {{ settings.textSecondaryColor | default('#546E7A') }};
        --color-border: {{ settings.borderColor | default('#D4DFE2') }};
        {% endif %}
        --font-heading-ar: '{{ fontAr }}', system-ui, -apple-system, sans-serif;
        --font-heading-en: '{{ fontEn }}', system-ui, -apple-system, sans-serif;
        --font-body-ar: '{{ fontAr }}', system-ui, -apple-system, sans-serif;
        --font-body-en: '{{ fontEn }}', system-ui, -apple-system, sans-serif;
      }
    </style>

    {# Tailwind CSS CDN + Config #}
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
      tailwind.config = {
        theme: {
          extend: {
            colors: {
              accent: {
                DEFAULT: 'var(--color-accent)',
                hover: 'var(--color-accent-hover)',
                light: 'var(--color-accent-light)',
              },
              surface: 'var(--color-surface)',
              success: 'var(--color-success)',
              error: 'var(--color-error)',
              warning: 'var(--color-warning)',
            },
            backgroundColor: {
              primary: 'var(--color-bg-primary)',
              secondary: 'var(--color-bg-secondary)',
              tertiary: 'var(--color-bg-tertiary)',
              'section-a': 'var(--color-section-a)',
              'section-b': 'var(--color-section-b)',
            },
            textColor: {
              primary: 'var(--color-text-primary)',
              secondary: 'var(--color-text-secondary)',
              tertiary: 'var(--color-text-tertiary)',
            },
            borderColor: {
              DEFAULT: 'var(--color-border)',
              hover: 'var(--color-border-hover)',
              focus: 'var(--color-border-focus)',
            },
            fontFamily: {
              'heading-ar': ['{{ fontAr }}', 'system-ui', 'sans-serif'],
              'heading-en': ['{{ fontEn }}', 'system-ui', 'sans-serif'],
              'body-ar': ['{{ fontAr }}', 'system-ui', 'sans-serif'],
              'body-en': ['{{ fontEn }}', 'system-ui', 'sans-serif'],
            },
          },
        },
      }
    </script>

    {# External libraries #}
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/swiper@11/swiper-bundle.min.css">
    <script src="https://cdn.jsdelivr.net/npm/swiper@11/swiper-bundle.min.js"></script>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/glightbox@3/dist/css/glightbox.min.css">
  </head>
  <body>
    {% template "header" %}
    <main>{% content %}</main>
    {% template "footer" %}

    {# JS Libraries #}
    <script src="https://cdn.jsdelivr.net/npm/glightbox@3/dist/js/glightbox.min.js"></script>
    <script defer src="https://cdn.jsdelivr.net/npm/alpinejs@3.x.x/dist/cdn.min.js"></script>

    {# Store config for JS #}
    <script>
      window.__QUMRA_CONFIG__ = {
        currency: '{{ localization.currency.currencyCode | default("SAR") }}',
        currencySymbol: '{{ localization.currency.currencySymbol | default("ر.س") }}',
        language: '{{ localization.language | default("ar") }}',
        exchangeRate: {{ localization.market.exchangeRate | default(1) }},
        moneyFormatSample: '{{ 1000 | money }}',
        wishlistIds: [{% for p in wishlist.products %}'{{ p._id }}'{% if not loop.last %},{% endif %}{% endfor %}],
        messages: {
          addedToCart: '{{t("pages.product.added_to_cart")}}',
          addError: '{{t("common.error")}}'
        }
      };
    </script>

    {% qumra_scripts %}
    <script src="{{ 'main.js' | assets }}"></script>

    {% if settings.customJS %}
      <script>{{ settings.customJS | safe }}</script>
    {% endif %}
  </body>
</html>
```

---

## 4. باترنات الـ Schema

### أنواع الـ Settings المتاحة
| Type | وصف | مثال |
|---|---|---|
| `string` | نص سطر واحد | عنوان، رابط |
| `text` | نص متعدد الأسطر | وصف |
| `textarea` | نص طويل (استخدمه بدل rich-text في blocks) | إجابة سؤال |
| `number` | رقم (min, max, step) | عدد الأعمدة |
| `boolean` | Toggle on/off | إظهار/إخفاء |
| `color` | Color picker | لون العنوان |
| `select` | Dropdown | اختيار layout |
| `range` | Slider | شفافية |
| `media` | رفع صورة | صورة الهيرو |
| `menu` | اختيار قائمة | قائمة الهيدر |
| `products` | اختيار منتجات | منتجات مميزة |
| `collections` | اختيار تصنيفات | تصنيفات مختارة |

### مثال widget schema مع `custom_colors`
```json
{
  "label": { "ar": "منتجات مميزة", "en": "Featured Products" },
  "settings": {
    "title": {
      "type": "string",
      "label": { "ar": "العنوان", "en": "Title" },
      "default": "منتجات مميزة"
    },
    "products": {
      "type": "products",
      "label": { "ar": "المنتجات", "en": "Products" }
    },
    "display_layout": {
      "type": "select",
      "label": { "ar": "طريقة العرض", "en": "Layout" },
      "default": "grid",
      "options": [
        { "value": "grid", "label": { "ar": "شبكة", "en": "Grid" } },
        { "value": "carousel", "label": { "ar": "سلايدر", "en": "Carousel" } }
      ]
    },
    "custom_colors": {
      "type": "boolean",
      "label": { "ar": "ألوان مخصصة", "en": "Custom Colors" },
      "default": false
    },
    "title_color": {
      "type": "color",
      "label": { "ar": "لون العنوان", "en": "Title Color" },
      "default": "#1A2C32"
    }
  }
}
```

### مثال widget مع blocks
```json
{
  "label": { "ar": "الأسئلة الشائعة", "en": "FAQ" },
  "settings": {
    "title": { "type": "string", "label": { "ar": "العنوان", "en": "Title" } },
    "display_layout": {
      "type": "select",
      "label": { "ar": "العرض", "en": "Layout" },
      "default": "centered",
      "options": [
        { "value": "centered", "label": { "ar": "وسط", "en": "Centered" } },
        { "value": "sidebar", "label": { "ar": "جانبي", "en": "Sidebar" } }
      ]
    }
  },
  "blocks": {
    "faq": {
      "label": { "ar": "سؤال", "en": "Question" },
      "settings": {
        "question": { "type": "string", "label": { "ar": "السؤال", "en": "Question" } },
        "answer": { "type": "textarea", "label": { "ar": "الإجابة", "en": "Answer" } }
      }
    }
  }
}
```

---

## 5. باترنات الـ Widget Template

### أساسيات
```njk
{# Widget Name — Theme Name #}
{% set title = widget.data.title | default('Default Title') %}
{% set useCustom = widget.data.custom_colors | default(false) %}

{% if useCustom %}
  {% set titleColor = widget.data.title_color | default('#1A2C32') %}
{% else %}
  {% set titleColor = 'var(--color-text-primary)' %}
{% endif %}

<section class="py-10 md:py-16">
  <div class="container mx-auto px-4">
    <h2 class="text-2xl font-bold mb-6" style="color: {{ titleColor }};">
      {{ title }}
    </h2>
    {# ... #}
  </div>
</section>
```

### منتجات مع product-card partial
```njk
{% set products = widget.data.products | default([]) %}
<div class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4">
  {% for product in products %}
    {% ui "product-card.njk", product=product, show_rating=true, show_badge=true %}
  {% endfor %}
</div>
```

### Blocks loop
```njk
{% for block in widget.blocks %}
  <div class="faq-item">
    <h3>{{ block.settings.question }}</h3>
    <p>{{ block.settings.answer }}</p>
  </div>
{% endfor %}
```

### بيانات لـ Alpine.js
```njk
<div x-data="myComponent({{ widget.data | dump | safe }})">
  {# ... #}
</div>
```

### ترجمات
```njk
{{t('pages.shop.title')}}
{{t('common.products')}}
{{t('product.add_to_cart')}}
```

---

## 6. الصفحات المطلوبة (pages/*.json)

### index.json (الرئيسية)
الويدجتس المقترحة بالترتيب:
1. `hero-section` — بانر رئيسي
2. `features-bar` — مزايا (شحن، دفع، إرجاع)
3. `product-grid` — منتجات مميزة
4. `collections-section` — تصنيفات
5. `image-with-text` — عن المتجر
6. `cta-banner` — بانر ترويجي
7. `testimonials-reviews` — آراء العملاء
8. `faq-accordion` — أسئلة شائعة
9. `newsletter` — اشتراك بريدي

### shop.json (المتجر — مهم جداً)
```json
{
  "layout": "layout",
  "widgets": [
    { "name": "shop-page", "widget": "shop-page" },
    { "name": "shop-products", "widget": "shop-products" }
  ]
}
```
**ملاحظة:** لازم widget اتنين:
- `shop-page` = الشل (sidebar, search, filters, data-qumra-section container)
- `shop-products` = شبكة المنتجات (يتعرض جوا الـ section)

### product.json
```json
{ "layout": "layout", "widgets": [{ "name": "product-detail", "widget": "product-detail" }] }
```

### cart.json
```json
{ "layout": "layout", "widgets": [{ "name": "cart-page", "widget": "cart-page" }] }
```

### باقي الصفحات
- `collections.json` — صفحة كل التصنيفات
- `products-collection.json` — صفحة تصنيف واحد
- `about.json` — عن المتجر
- `blog.json` / `blogs.json` / `article.json` — المدونة
- `page.json` — صفحة عامة

### templates/header.json
```json
{
  "title": "Header Group",
  "allowAdd": true, "allowRemove": true, "allowReorder": true,
  "widgets": [
    { "name": "announcement-bar", "widget": "announcement-bar" },
    { "name": "header", "widget": "header" }
  ]
}
```

### templates/footer.json
```json
{
  "title": "Footer Group",
  "allowAdd": true, "allowRemove": true, "allowReorder": true,
  "widgets": [{ "name": "footer", "widget": "footer" }]
}
```

---

## 7. نظام الشوب (Shop System) — الأكثر تعقيداً

### المبدأ
- `shop-page/widget.njk` فيه `<div data-qumra-section="shop-products"></div>`
- المنصة بتحقن `shop-products/widget.njk` جوا الـ div ده
- `qumra.filters.sort/toggle/range/clear` بيعملوا re-render للسكشن تلقائي

### البحث — استثناء مهم
`qumra.filters.search()` مش بيشتغل — لازم تستخدم AJAX API:
```javascript
// لما يكون فيه searchQuery نشط، كل العمليات تروح بـ _fetch()
if (cfg.section && !this.searchQuery) {
    qumra.filters.sort(value);  // section mode
} else {
    this._fetch(url);           // AJAX mode (بيضرب /ajax/search/products)
}
```

### CSS trick لمنع ظهور مكرر
```css
.shop-products-content { display: none; }
[data-qumra-section="shop-products"] .shop-products-content { display: block; }
```

### MutationObserver بدل await
```javascript
// qumra.filters.* مش بترجع Promise — استخدم MutationObserver
new MutationObserver(function() {
    self._syncSidebarCount();
    self.loading = false;
}).observe(wrapper, { childList: true });
```

---

## 8. الـ CSS Variables System

### تعريف في `style.css` `:root` (defaults)
```css
:root {
    --color-accent: #00BCD4;
    --color-primary: #00BCD4;
    --color-accent-hover: #00838F;
    --color-primary-dark: #00838F;
    --color-accent-light: #B2EBF2;
    --color-bg-primary: #F8FAFB;
    --color-bg-secondary: #EBF1F3;
    --color-surface: #FFFFFF;
    --color-text-primary: #1A2C32;
    --color-text-secondary: #546E7A;
    --color-text-tertiary: #979797;
    --color-border: #D4DFE2;
    --color-border-hover: #C0CED2;
    --color-border-focus: #00BCD4;
    --color-success: #7FB69E;
    --color-error: #C4726C;
    --color-warning: #C9A96E;
    --font-heading-ar: 'IBM Plex Sans Arabic', system-ui, sans-serif;
    --font-body-ar: 'IBM Plex Sans Arabic', system-ui, sans-serif;
    --font-heading-en: 'DM Sans', system-ui, sans-serif;
    --font-body-en: 'DM Sans', system-ui, sans-serif;
}

body {
    font-family: var(--font-body-ar);
    background: var(--color-bg-primary);
    color: var(--color-text-primary);
}
html[lang="en"] body, html[dir="ltr"] body {
    font-family: var(--font-body-en);
}
h1,h2,h3,h4,h5,h6 { font-family: var(--font-heading-ar); }
html[dir="ltr"] h1,html[dir="ltr"] h2,html[dir="ltr"] h3,
html[dir="ltr"] h4,html[dir="ltr"] h5,html[dir="ltr"] h6 { font-family: var(--font-heading-en); }
```

### Override ديناميكي في `layout.njk`
الـ `<style>` block بعد `style.css` بيعمل override لما `custom_colors` مفعّل.

---

## 9. الويدجتس الأساسية (32 widget في سكون)

### Header Group
- `announcement-bar` — شريط إعلانات متحرك
- `header` — هيدر (لوجو، قائمة، بحث، سلة، لغة)

### Homepage Sections
- `hero-section` — بانر رئيسي (layouts: full-background, split, minimal)
- `features-bar` — مزايا أفقية/شبكة
- `product-grid` — منتجات مختارة
- `collections-section` — تصنيفات
- `best-products` — أفضل المنتجات
- `image-with-text` — صورة مع نص
- `cta-banner` — بانر ترويجي (countdown, overlay)
- `brand-logos` — شعارات العلامات
- `testimonials-reviews` — آراء العملاء
- `mindful-quote-banner` — شريط اقتباسات
- `faq-accordion` — أسئلة شائعة
- `newsletter` — اشتراك بريدي

### Shop
- `shop-page` — shell (sidebar + filters + search)
- `shop-products` — شبكة المنتجات (data-qumra-section)

### Product
- `product-detail` — صفحة المنتج الكاملة
- `quick-view-product` — Quick view modal content

### Collections
- `collections-page` — كل التصنيفات
- `collection-page` — تصنيف واحد مع فلاتر
- `category-detail` — تفاصيل تصنيف

### Blog
- `blog` — صفحة المدونة
- `blog-articles` — قائمة المقالات
- `best-articles` — مقالات مميزة
- `best-article` — مقال واحد مميز

### Other
- `cart-page` — صفحة السلة
- `about-page` — صفحة عن المتجر
- `footer` — الفوتر

---

## 10. main.js — الهيكل الأساسي

```javascript
document.addEventListener('alpine:init', () => {
    // ---- Config ----
    const QumraConfig = { /* من window.__QUMRA_CONFIG__ */ };

    // ---- Utilities ----
    const Utils = { debounce, formatMoney };
    const ApiClient = { get, post, delete };
    const EventBus = { emit };

    // ---- Managers (Cart, Wishlist, Search, Product) ----
    const CartManager = { add, increment, decrement, update, remove, clear };
    const WishlistManager = { add, remove, toggle, clear };
    const SearchManager = { search, suggest };
    const ProductManager = { get, getVariant };

    // ---- Alpine Stores ----
    Alpine.store('cart', { totalQuantity, totalPrice, items, update(data) });
    Alpine.store('modal', { current, open(name), close(), toggle(name) });

    // ---- Alpine Components ----
    Alpine.data('productCard', (productId) => ({ /* wishlist, add to cart */ }));
    Alpine.data('cartInteraction', (config) => ({ /* cart page logic */ }));
    Alpine.data('collectionFilter', (cfg) => ({ /* shop filters, search, sort */ }));
    Alpine.data('quickView', () => ({ /* quick view modal */ }));
    Alpine.data('productDetail', (cfg) => ({ /* product page: variants, gallery */ }));
});
```

---

## 11. Global Variables المتاحة في Templates

```njk
{{ store }}              {# بيانات المتجر (name, logo, url) #}
{{ cart }}               {# السلة (items, totalPrice, totalQuantity) #}
{{ wishlist }}           {# المفضلة (products, count) #}
{{ localization }}       {# اللغة، العملة، سعر الصرف #}
{{ settings }}           {# إعدادات الثيم (settings-schema.json) #}
{{ product }}            {# بيانات المنتج (صفحة المنتج بس) #}
{{ collection }}         {# بيانات التصنيف (صفحة التصنيف بس) #}
{{ context.shop }}       {# بيانات الشوب (products, pagination, filters) #}
{{ widget.data }}        {# إعدادات الويدجت الحالي #}
{{ widget.blocks }}      {# Blocks الويدجت (repeatable items) #}
{{ page }}               {# معلومات الصفحة #}
```

### Filters
```njk
| money          {# 244500 → "2,445.00 ر.س" #}
| assets         {# 'style.css' → CDN URL #}
| safe           {# HTML بدون escaping #}
| default(val)   {# Fallback لو فاضي #}
| dump           {# Convert to JSON string #}
| length         {# طول Array/String #}
| replace(a,b)   {# استبدال نص #}
| truncate(50)   {# قص نص #}
```

### Custom Tags
```njk
{% seo %}                {# SEO meta tags #}
{% qumra_head %}         {# Platform head resources #}
{% qumra_scripts %}      {# Platform runtime scripts #}
{% template "header" %}  {# Include header/footer template #}
{% content %}            {# Render page widgets #}
{% ui "file.njk", param=value %}  {# Include UI partial #}
{% form 'lang' %}...{% endform %} {# Language switcher form #}
```

---

## 12. ترتيب العمل المقترح

1. **الهيكل**: `qumra.config.json` + `settings-schema.json` + `layout.njk` + `style.css`
2. **الأساسيات**: `header` + `footer` + `ui/product-card.njk` + `ui/cart.njk` + `ui/search.njk`
3. **الصفحة الرئيسية**: `hero-section` → `features-bar` → `product-grid` → `collections-section`
4. **المتجر**: `shop-page` + `shop-products` (انسخ المنطق من سكون وغير الشكل)
5. **المنتج**: `product-detail` + `quick-view-product`
6. **السلة**: `cart-page`
7. **باقي الويدجتس**: `cta-banner`, `testimonials`, `newsletter`, `faq`, `about`, etc.
8. **المدونة**: `blog`, `blog-articles`, `best-articles`
9. **الترجمات**: `locales/ar.json` + `locales/en.json`
10. **التلميع**: animations, loading states, empty states
