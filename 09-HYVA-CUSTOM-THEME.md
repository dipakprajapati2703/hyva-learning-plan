# Building a Custom Hyvä Theme

**Reference:** https://docs.hyva.io/hyva-themes/building-your-theme/index.html

---

## Prerequisites

- Magento 2.4.6+
- Hyvä Themes installed (`Hyva/default`)
- Node.js 20+
- Composer 2.x

```bash
# Verify installation
php bin/magento theme:list | grep Hyva
node --version  # v20.x.x or higher
```

---

## Theme Setup

### Step 1: Create Theme Directory

```bash
mkdir -p app/design/frontend/Logicrays/hyva-child
cd app/design/frontend/Logicrays/hyva-child
```

### Step 2: Create registration.php

```php
<?php
use Magento\Framework\Component\ComponentRegistrar;

ComponentRegistrar::register(
    ComponentRegistrar::THEME,
    'frontend/Logicrays/hyva-child',
    __DIR__
);
```

### Step 3: Create theme.xml

```xml
<?xml version="1.0"?>
<theme xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:noNamespaceSchemaLocation="urn:magento:framework:Config/etc/theme.xsd">
    <title>Hyva Child Theme</title>
    <parent>Hyva/default</parent>
    <media>
        <preview_image>media/preview.png</preview_image>
    </media>
</theme>
```

### Step 4: Create etc/view.xml (Optional - Image Sizes)

```xml
<?xml version="1.0"?>
<view xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:noNamespaceSchemaLocation="urn:magento:framework:Config/etc/view.xsd">
    <media>
        <images module="Magento_Catalog">
            <image id="category_page_grid" type="small_image">
                <width>240</width>
                <height>300</height>
            </image>
            <image id="product_page_image_large" type="image">
                <width>700</width>
                <height>875</height>
            </image>
        </images>
    </media>
</view>
```

### Step 5: Copy Web Directory from Parent

```bash
cp -r vendor/hyva-themes/magento2-default-theme/web/ \
      app/design/frontend/Logicrays/hyva-child/web/
```

### Step 6: Configure Parent Theme (CRITICAL)

**File:** `web/tailwind/hyva.config.json`

Add the `include` directive to reference parent theme templates:

```json
{
    "tailwind": {
        "include": [
            { "src": "vendor/hyva-themes/magento2-default-theme" }
        ],
        "exclude": []
    },
    "tokens": {
        "values": {
            "color": {
                "primary": {
                    "lighter": "oklch(52% 0.2 265)",
                    "DEFAULT": "oklch(46% 0.2 265)",
                    "darker": "oklch(28% 0.2 265)"
                },
                "secondary": {
                    "lighter": "oklch(72% 0.2 150)",
                    "DEFAULT": "oklch(53% 0.15 150)",
                    "darker": "oklch(39% 0.1 153)"
                }
            }
        }
    }
}
```

> **Why?** Without `include`, Tailwind only scans your child theme templates. Parent theme classes won't be compiled, causing broken styles.

### Step 7: Build and Register

```bash
cd app/design/frontend/Logicrays/hyva-child/web/tailwind
npm install
npm run build
bin/magento cache:flush
```

### Step 8: Activate Theme

**Admin:** Content > Design > Configuration > Select "Hyva Child Theme"

---

## Directory Structure

```
app/design/frontend/Logicrays/hyva-child/
├── registration.php
├── theme.xml
├── media/preview.png
├── Magento_Theme/
│   ├── layout/
│   └── templates/
├── Magento_Catalog/
│   ├── layout/
│   └── templates/
└── web/
    ├── css/styles.css          # Generated
    ├── images/
    └── tailwind/
        ├── package.json
        ├── hyva.config.json    # Parent include + tokens
        ├── tailwind-source.css
        └── generated/          # Auto-generated
```

---

## Tailwind Configuration

### Design Tokens

Customize colors in `web/tailwind/hyva.config.json`:

```json
{
    "tokens": {
        "values": {
            "color": {
                "primary": {
                    "lighter": "#60a5fa",
                    "DEFAULT": "#3b82f6",
                    "darker": "#1e40af"
                }
            },
            "font": {
                "family": {
                    "sans": "Inter, system-ui, sans-serif"
                }
            }
        }
    }
}
```

### Build Commands

```bash
cd web/tailwind

npm run watch   # Development (auto-rebuild)
npm run build   # Production (minified)
```

---

## Template Override

### How to Override Templates

Copy templates from parent theme, then customize:

```bash
# 1. Find template in parent theme
ls vendor/hyva-themes/magento2-default-theme/Magento_Catalog/templates/product/

# 2. Create directory structure in child theme
mkdir -p Magento_Catalog/templates/product/list

# 3. Copy template
cp vendor/hyva-themes/magento2-default-theme/Magento_Catalog/templates/product/list/item.phtml \
   Magento_Catalog/templates/product/list/item.phtml

# 4. Edit your copy
```

### Common Templates to Override

| Template | Path |
|----------|------|
| Product List Item | `Magento_Catalog/templates/product/list/item.phtml` |
| Product Gallery | `Magento_Catalog/templates/product/view/gallery.phtml` |
| Header | `Magento_Theme/templates/html/header.phtml` |
| Footer | `Magento_Theme/templates/html/footer.phtml` |
| Mini Cart | `Magento_Checkout/templates/cart/minicart.phtml` |

### Template Example with Alpine.js

```php
<?php
declare(strict_types=1);

use Magento\Framework\Escaper;
use Hyva\Theme\ViewModel\HeroiconsOutline;

/** @var Escaper $escaper */
/** @var HeroiconsOutline $heroicons */
$heroicons = $block->getData('heroicons');
?>

<div class="product-item" x-data="{ showQuickView: false }">
    <button @click="showQuickView = true" class="btn-primary">
        <?= $heroicons->renderHtml('eye', 'w-5 h-5') ?>
        Quick View
    </button>
</div>
```

---

## Layout XML

### Add Block

**File:** `Magento_Catalog/layout/catalog_product_view.xml`

```xml
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:noNamespaceSchemaLocation="urn:magento:framework:View/Layout/etc/page_configuration.xsd">
    <body>
        <referenceContainer name="product.info.main">
            <block name="custom.badge"
                   template="Magento_Catalog::product/badge.phtml"
                   before="product.info.price">
                <arguments>
                    <argument name="badge_text" xsi:type="string">New</argument>
                </arguments>
            </block>
        </referenceContainer>
    </body>
</page>
```

### Move Block

```xml
<move element="product.info.stock.sku"
      destination="product.info.main"
      after="product.info.price"/>
```

### Remove Block

```xml
<referenceBlock name="product.info.review" remove="true"/>
```

### Extend Default Layout

**File:** `Magento_Theme/layout/default.xml`

```xml
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:noNamespaceSchemaLocation="urn:magento:framework:View/Layout/etc/page_configuration.xsd">
    <body>
        <referenceContainer name="footer">
            <block name="footer.newsletter"
                   template="Magento_Theme::html/footer/newsletter.phtml"
                   before="footer-content"/>
        </referenceContainer>
    </body>
</page>
```

---

## Custom Components

### Create Button Component

**File:** `web/tailwind/components/buttons.css`

```css
@layer components {
    .btn {
        @apply inline-flex items-center justify-center px-6 py-3
               font-medium rounded-lg transition-all duration-200;
    }
    .btn-primary {
        @apply bg-primary text-white hover:bg-primary-darker;
    }
    .btn-secondary {
        @apply bg-secondary text-white hover:bg-secondary-darker;
    }
    .btn-outline {
        @apply border-2 border-primary text-primary hover:bg-primary hover:text-white;
    }
}
```

### Import in tailwind-source.css

```css
@import "@hyva-themes/hyva-modules/css";
@import "tailwindcss" source(none);

/* Custom components */
@import "./components/buttons.css";

/* Generated files */
@import "./generated/hyva-source.css";
@import "./generated/hyva-tokens.css";
```

### Custom Utility Classes

**File:** `web/tailwind/utilities/custom.css`

```css
@layer utilities {
    .line-clamp-3 {
        display: -webkit-box;
        -webkit-line-clamp: 3;
        -webkit-box-orient: vertical;
        overflow: hidden;
    }
    .animation-fade-in {
        animation: fadeIn 0.3s ease-in;
    }
    @keyframes fadeIn {
        from { opacity: 0; }
        to { opacity: 1; }
    }
}
```

---

## Development Workflow

```bash
# Terminal 1: Watch for CSS changes
cd web/tailwind && npm run watch

# Terminal 2: After template/layout changes
bin/magento cache:clean layout

# Before deployment
npm run build
bin/magento cache:flush
```

---

## Best Practices

1. **Never modify vendor files** - Always copy to child theme
2. **Use design tokens** - `bg-primary` not `bg-blue-600`
3. **Override only what you need** - Don't copy entire directories

---

## Troubleshooting

### Broken Styles (Most Common)

**Cause:** Missing parent include in `hyva.config.json`

```json
{
    "tailwind": {
        "include": [
            { "src": "vendor/hyva-themes/magento2-default-theme" }
        ]
    }
}
```

Then: `npm run build && bin/magento cache:flush`

### CSS Changes Not Appearing

```bash
npm run build
bin/magento cache:flush
# Hard refresh browser: Ctrl+Shift+R
```

### Theme Not in Admin

```bash
bin/magento setup:upgrade
bin/magento cache:flush
bin/magento theme:list
```

### Node Errors

```bash
rm -rf node_modules package-lock.json
npm install
node --version  # Must be 20+
```

---

## Essential Commands

```bash
# Build CSS
cd web/tailwind && npm run build

# Cache
bin/magento cache:clean layout
bin/magento cache:flush

# Theme registration
bin/magento setup:upgrade
bin/magento theme:list
```

---

**Hyvä 3.0+ | TailwindCSS 4.1+ | Node.js 20+**
