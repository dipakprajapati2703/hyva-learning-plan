# Complete Tailwind CSS & Alpine.js Setup Guide for Hyvä Themes

**Comprehensive guide for understanding how Tailwind CSS and Alpine.js are configured in Hyvä**

---

## Table of Contents

1. [Tailwind CSS Setup](#tailwind-css-setup)
2. [Alpine.js Setup](#alpinejs-setup)
3. [File Structure Deep Dive](#file-structure-deep-dive)
4. [Compilation Process](#compilation-process)
5. [Customization Workflow](#customization-workflow)
6. [Troubleshooting](#troubleshooting)

---

## Tailwind CSS Setup

### Overview

Hyvä uses **Tailwind CSS v3** with **JIT (Just-In-Time)** compiler for optimal performance. Tailwind is configured to scan all theme templates and generate only the CSS classes that are actually used.

---

### File Structure for Tailwind CSS

```
vendor/hyva-themes/magento2-default-theme/
└── web/
    └── tailwind/
        ├── tailwind.config.js         # Main Tailwind configuration
        ├── tailwind-source.css        # Source CSS (imports)
        ├── tailwind.css               # Compiled output (generated)
        ├── package.json               # NPM dependencies
        ├── package-lock.json          # Lock file
        └── components/
            └── custom-components.css  # Custom component definitions

Your custom theme (app/design/frontend/MyCompany/hyva-custom/):
└── web/
    └── tailwind/
        ├── tailwind.config.js         # Your custom configuration
        ├── tailwind-source.css        # Optional custom source
        └── components/
            └── custom-components.css  # Your custom components
```

---

### Step 1: Understanding tailwind.config.js

**Location:** `vendor/hyva-themes/magento2-default-theme/web/tailwind/tailwind.config.js`

**Purpose:** Main configuration file that defines:
- Content paths to scan for class names
- Theme colors, fonts, spacing
- Plugins
- Custom utilities

**Key Sections:**

```javascript
module.exports = {
    // 1. PRESETS - Import Hyvä's base configuration
    presets: [
        hyvaModules.getThemeConfig()
    ],

    // 2. CONTENT - Files to scan for Tailwind classes
    content: [
        // Hyvä default theme templates
        '../../../../../../../vendor/hyva-themes/magento2-default-theme/**/*.phtml',
        '../../../../../../../vendor/hyva-themes/magento2-theme-module/src/**/*.phtml',

        // Layout XML files
        '../../*/layout/*.xml',
        '../../*/page_layout/*.xml',

        // Custom theme templates
        '../../**/*.phtml',
    ],

    // 3. THEME - Extend default theme
    theme: {
        extend: {
            colors: { /* custom colors */ },
            fontFamily: { /* custom fonts */ },
            spacing: { /* custom spacing */ },
            // ... more customizations
        },
    },

    // 4. PLUGINS - Additional functionality
    plugins: [
        require('@tailwindcss/forms'),
        require('@tailwindcss/typography'),
        // Custom plugins
    ],
}
```

---

### Step 2: Understanding tailwind-source.css

**Location:** `web/tailwind/tailwind-source.css`

**Purpose:** Entry point for Tailwind compilation. This file imports Tailwind's base styles, components, and utilities.

**Default Content:**

```css
/**
 * Tailwind CSS Source File
 * This file is compiled into tailwind.css
 */

/* Tailwind base styles */
@tailwind base;

/* Tailwind component classes */
@tailwind components;

/* Custom components */
@import './components/custom-components.css';

/* Tailwind utility classes */
@tailwind utilities;

/* Custom utilities */
@layer utilities {
    .text-shadow {
        text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1);
    }
}

/* Custom base styles */
@layer base {
    h1 {
        @apply text-3xl font-bold;
    }
    h2 {
        @apply text-2xl font-semibold;
    }
}
```

**Layers Explained:**
- `@tailwind base` - CSS reset and base element styles
- `@tailwind components` - Component classes (buttons, cards, etc.)
- `@tailwind utilities` - Utility classes (flex, grid, colors, etc.)
- `@layer` - Define custom styles in specific layers

---

### Step 3: Understanding package.json

**Location:** `web/tailwind/package.json`

**Purpose:** Defines NPM dependencies and build scripts.

**Default Content:**

```json
{
  "name": "hyva-theme-tailwind",
  "version": "1.0.0",
  "description": "Tailwind CSS for Hyvä Theme",
  "scripts": {
    "build": "tailwindcss -i tailwind-source.css -o tailwind.css --minify",
    "watch": "tailwindcss -i tailwind-source.css -o tailwind.css --watch",
    "build:dev": "tailwindcss -i tailwind-source.css -o tailwind.css"
  },
  "dependencies": {
    "@hyva-themes/hyva-modules": "^1.0.0",
    "@tailwindcss/forms": "^0.5.3",
    "@tailwindcss/typography": "^0.5.9",
    "tailwindcss": "^3.3.0"
  }
}
```

**Scripts Explained:**
- `build` - Compile for production (minified)
- `watch` - Watch for changes and recompile automatically
- `build:dev` - Compile for development (unminified, with source maps)

---

### Step 4: Tailwind Compilation Process

**How Tailwind CSS is compiled in Hyvä:**

```
┌─────────────────────────────────────────────────────────────┐
│ 1. Scan Content Files (from tailwind.config.js)            │
│    - *.phtml templates                                      │
│    - *.xml layout files                                     │
│    - *.js JavaScript files                                  │
└────────────────┬────────────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────────────┐
│ 2. Extract Tailwind Classes                                 │
│    - Find all class="..." attributes                        │
│    - Extract class names (e.g., "bg-blue-500", "p-4")      │
└────────────────┬────────────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────────────┐
│ 3. Generate CSS                                             │
│    - Create CSS rules for extracted classes                 │
│    - Apply theme configuration (colors, fonts, etc.)        │
│    - Add custom components and utilities                    │
└────────────────┬────────────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────────────┐
│ 4. Optimize (JIT Compiler)                                  │
│    - Remove unused CSS (purge)                              │
│    - Minify output                                          │
│    - Generate source map (if dev mode)                      │
└────────────────┬────────────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────────────┐
│ 5. Output: tailwind.css                                     │
│    - Final compiled CSS file                                │
│    - Served to browser                                      │
└─────────────────────────────────────────────────────────────┘
```

---

### Step 5: How Tailwind CSS is Loaded in Browser

**File:** `vendor/hyva-themes/magento2-theme-module/src/view/frontend/layout/default_hyva.xml`

```xml
<referenceContainer name="head.additional">
    <block name="head.hyva-scripts" template="Hyva_Theme::page/js/hyva.phtml"/>
</referenceContainer>
```

**Template:** `vendor/hyva-themes/magento2-theme-module/src/view/frontend/templates/page/js/hyva.phtml`

This template includes the compiled `tailwind.css` file:

```php
<link rel="stylesheet" href="<?= $escaper->escapeUrl($block->getViewFileUrl('css/tailwind.css')) ?>" />
```

**Complete Flow:**

```
Layout XML (default_hyva.xml)
    ↓
Block (Hyva_Theme::page/js/hyva.phtml)
    ↓
Loads: pub/static/.../Hyva_Theme/css/tailwind.css
    ↓
Browser applies styles
```

---

### Step 6: Building Tailwind CSS (Step-by-Step Commands)

#### For Development (Hyvä Default Theme):

```bash
# 1. Navigate to Tailwind directory
cd vendor/hyva-themes/magento2-default-theme/web/tailwind/

# 2. Install NPM dependencies (first time only)
npm install

# 3. Build Tailwind CSS for development
npm run build:dev

# 4. Watch mode (auto-rebuild on changes)
npm run watch
```

#### For Production:

```bash
# Build minified CSS for production
npm run build
```

#### For Custom Theme:

```bash
# 1. Navigate to your theme's Tailwind directory
cd app/design/frontend/MyCompany/hyva-custom/web/tailwind/

# 2. Install dependencies
npm install

# 3. Build
npm run build

# 4. Deploy to Magento static files
cd /var/www/html/m246p8
php bin/magento setup:static-content:deploy -f
```

---

### Step 7: Where Compiled CSS is Stored

**After Compilation:**
- `web/tailwind/tailwind.css` - Compiled CSS in theme directory

**After Magento Static Content Deploy:**
- `pub/static/frontend/Hyva/default/en_US/css/tailwind.css`
- `pub/static/frontend/MyCompany/hyva-custom/en_US/css/tailwind.css`

**Browser loads from:**
```
https://yourstore.com/static/version123/frontend/Hyva/default/en_US/css/tailwind.css
```

---

## Alpine.js Setup

### Overview

Hyvä uses **Alpine.js v3** as a lightweight JavaScript framework for reactive UI components. Alpine.js is loaded from CDN or bundled, with plugins for extended functionality.

---

### File Structure for Alpine.js

```
vendor/hyva-themes/magento2-theme-module/src/
└── view/frontend/
    ├── layout/
    │   └── default_hyva.xml                              # Alpine.js layout configuration
    └── templates/
        └── page/
            └── js/
                ├── alpinejs.phtml                        # Main Alpine.js loader
                ├── plugins/
                │   ├── snap-slider.phtml                 # Snap slider plugin
                │   ├── htmldialog.phtml                  # Dialog plugin
                │   ├── intersect.phtml                   # Intersect plugin
                │   ├── ignore.phtml                      # Ignore plugin
                │   └── defer.phtml                       # Defer plugin
                ├── alpine-defer-rules.phtml              # Defer configuration
                ├── cookies.phtml                         # Cookie handling
                ├── private-content.phtml                 # Private content
                └── variables.phtml                       # JavaScript variables
```

---

### Step 1: Understanding default_hyva.xml (Alpine.js Loading)

**Location:** `vendor/hyva-themes/magento2-theme-module/src/view/frontend/layout/default_hyva.xml`

```xml
<?xml version="1.0"?>
<page>
    <body>
        <!-- Alpine.js loaded before </body> for non-blocking load -->
        <referenceContainer name="before.body.end">

            <!-- Main Alpine.js block -->
            <block name="script-alpine-js" template="Hyva_Theme::page/js/alpinejs.phtml">

                <!-- Plugins (loaded BEFORE Alpine.js initializes) -->
                <block name="alpine-plugin-snap-slider"
                       template="Hyva_Theme::page/js/plugins/snap-slider.phtml"/>

                <block name="alpine-plugin-htmldialog"
                       template="Hyva_Theme::page/js/plugins/htmldialog.phtml"/>

                <block name="alpine-plugin-intersect"
                       template="Hyva_Theme::page/js/plugins/intersect.phtml"/>

                <block name="alpine-plugin-ignore"
                       template="Hyva_Theme::page/js/plugins/ignore.phtml"/>

                <block name="alpine-plugin-defer"
                       template="Hyva_Theme::page/js/plugins/defer.phtml">

                    <!-- Defer rules configuration -->
                    <block name="alpine-defer-rules"
                           template="Hyva_Theme::page/js/alpine-defer-rules.phtml"/>
                </block>
            </block>

            <!-- Utility scripts -->
            <block name="script-cookies" template="Hyva_Theme::page/js/cookies.phtml"/>
            <block name="script-private-content" template="Hyva_Theme::page/js/private-content.phtml"/>

        </referenceContainer>
    </body>
</page>
```

**Why before.body.end?**
- Non-blocking: HTML renders first, then JavaScript loads
- DOM is ready when scripts execute
- Optimal performance

---

### Step 2: Understanding alpinejs.phtml

**Location:** `vendor/hyva-themes/magento2-theme-module/src/view/frontend/templates/page/js/alpinejs.phtml`

**Purpose:** Main Alpine.js loader template

**Content (simplified):**

```php
<?php
/**
 * Alpine.js Main Loader
 * Loads Alpine.js core and initializes plugins
 */
?>
<script>
    /**
     * Alpine.js Plugin Registration
     * Plugins must be registered BEFORE Alpine.start()
     */
    window.Alpine = window.Alpine || {};

    // Plugin initialization placeholder
    window.Alpine.plugins = window.Alpine.plugins || [];

    <?= /* @noEscape */ $block->getChildHtml() ?>
    // Child blocks render plugin code here

</script>

<!-- Load Alpine.js Core -->
<script defer src="https://cdn.jsdelivr.net/npm/alpinejs@3.x.x/dist/cdn.min.js"></script>

<!-- OR load from local file -->
<!-- <script defer src="<?= $escaper->escapeUrl($block->getViewFileUrl('Hyva_Theme::js/alpine.min.js')) ?>"></script> -->
```

**Load Order:**
1. Plugin definitions (child blocks)
2. Alpine.js core library
3. Alpine automatically starts

---

### Step 3: Understanding Alpine.js Plugins

#### Plugin: Intersect

**File:** `plugins/intersect.phtml`

**Purpose:** Trigger actions when elements enter viewport (lazy loading, animations)

**Usage:**
```html
<div x-data="{ show: false }"
     x-intersect="show = true">
    <div x-show="show" x-transition>
        Appears when scrolled into view
    </div>
</div>
```

#### Plugin: Defer

**File:** `plugins/defer.phtml`

**Purpose:** Delay Alpine.js component initialization until needed (performance optimization)

**Usage:**
```html
<!-- Load component when it enters viewport -->
<div x-data="expensiveComponent()" x-defer="intersect">
    <!-- Heavy component -->
</div>

<!-- Load component on user interaction -->
<div x-data="slideshow()" x-defer="click">
    <!-- Slideshow -->
</div>
```

#### Plugin: HTMLDialog

**File:** `plugins/htmldialog.phtml`

**Purpose:** Native HTML `<dialog>` element support

**Usage:**
```html
<dialog x-data="{ open: false }"
        x-dialog="open">
    <p>Dialog content</p>
    <button @click="open = false">Close</button>
</dialog>

<button @click="open = true">Open Dialog</button>
```

#### Plugin: Snap Slider

**File:** `plugins/snap-slider.phtml`

**Purpose:** Touch-friendly product sliders with snap scrolling

**Usage:**
```html
<div x-data="hyva.snapSlider()"
     x-snap-slider>
    <div x-snap-slider-item>Slide 1</div>
    <div x-snap-slider-item>Slide 2</div>
    <div x-snap-slider-item>Slide 3</div>
</div>
```

---

### Step 4: Alpine.js Loading Flow

```
┌─────────────────────────────────────────────────────────────┐
│ 1. HTML Renders                                             │
│    - Page DOM is built                                      │
│    - Alpine directives (x-data, x-show) in HTML             │
└────────────────┬────────────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────────────┐
│ 2. Load Plugins (from child blocks)                         │
│    - window.Alpine.plugin() calls                           │
│    - Register BEFORE Alpine.start()                         │
└────────────────┬────────────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────────────┐
│ 3. Load Alpine.js Core (alpine.min.js)                      │
│    - Download from CDN or local                             │
│    - Initialize Alpine                                      │
└────────────────┬────────────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────────────┐
│ 4. Alpine.start() (automatic)                               │
│    - Scan DOM for x-data components                         │
│    - Initialize reactive data                               │
│    - Bind event listeners                                   │
└────────────────┬────────────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────────────┐
│ 5. Components Active                                        │
│    - Alpine directives work                                 │
│    - User interactions trigger updates                      │
└─────────────────────────────────────────────────────────────┘
```

---

### Step 5: JavaScript Variables (Magento Data)

**File:** `page/js/variables.phtml`

**Purpose:** Pass PHP/Magento data to JavaScript

**Example:**
```php
<script>
    window.hyva = window.hyva || {};

    hyva.config = {
        baseUrl: '<?= $escaper->escapeJs($block->getBaseUrl()) ?>',
        storeCode: '<?= $escaper->escapeJs($block->getStoreCode()) ?>',
        currency: '<?= $escaper->escapeJs($block->getCurrentCurrencyCode()) ?>',
        priceFormat: <?= /* @noEscape */ $block->getPriceFormatJson() ?>,
        customer: {
            loggedIn: <?= $block->isCustomerLoggedIn() ? 'true' : 'false' ?>
        }
    };
</script>
```

**Access in Alpine.js:**
```html
<div x-data="{ baseUrl: window.hyva.config.baseUrl }">
    <a :href="baseUrl + '/checkout'">Checkout</a>
</div>
```

---

### Step 6: Adding Custom Alpine.js Plugin

**Step-by-Step:**

#### 1. Create Plugin Template

**File:** `app/design/frontend/MyCompany/hyva-custom/Magento_Theme/templates/page/js/custom-alpine-plugin.phtml`

```php
<script>
document.addEventListener('alpine:init', () => {
    // Custom Alpine.js Magic Property
    Alpine.magic('customHelper', () => {
        return (value) => {
            return value.toUpperCase();
        };
    });

    // Custom Alpine.js Directive
    Alpine.directive('highlight', (el, { expression }, { evaluate }) => {
        el.classList.add('bg-yellow-200');
    });
});
</script>
```

#### 2. Add to Layout XML

**File:** `app/design/frontend/MyCompany/hyva-custom/Magento_Theme/layout/default.xml`

```xml
<?xml version="1.0"?>
<page>
    <body>
        <referenceBlock name="script-alpine-js">
            <block name="alpine-plugin-custom"
                   template="Magento_Theme::page/js/custom-alpine-plugin.phtml"
                   before="alpine-plugin-intersect"/>
        </referenceBlock>
    </body>
</page>
```

#### 3. Use Custom Plugin

```html
<!-- Use custom magic property -->
<div x-data="{ text: 'hello' }">
    <p x-text="$customHelper(text)"></p> <!-- Outputs: HELLO -->
</div>

<!-- Use custom directive -->
<div x-highlight>This will be highlighted</div>
```

---

## File Structure Deep Dive

### Complete File Structure Map

```
Hyvä Theme Root
├── vendor/hyva-themes/
│   ├── magento2-default-theme/             # Default Hyvä theme
│   │   ├── web/
│   │   │   └── tailwind/
│   │   │       ├── tailwind.config.js      # Tailwind config
│   │   │       ├── tailwind-source.css     # Source CSS
│   │   │       ├── tailwind.css            # Compiled CSS (generated)
│   │   │       ├── package.json            # NPM dependencies
│   │   │       └── components/
│   │   │           └── custom-components.css
│   │   ├── Magento_Theme/
│   │   │   └── templates/
│   │   │       └── html/
│   │   │           ├── header.phtml
│   │   │           └── footer.phtml
│   │   └── ...
│   │
│   └── magento2-theme-module/              # Hyvä core module
│       └── src/
│           ├── view/frontend/
│           │   ├── layout/
│           │   │   └── default_hyva.xml    # Alpine.js & core setup
│           │   ├── templates/
│           │   │   └── page/
│           │   │       └── js/
│           │   │           ├── alpinejs.phtml
│           │   │           ├── plugins/
│           │   │           │   ├── snap-slider.phtml
│           │   │           │   ├── htmldialog.phtml
│           │   │           │   ├── intersect.phtml
│           │   │           │   ├── ignore.phtml
│           │   │           │   └── defer.phtml
│           │   │           ├── variables.phtml
│           │   │           ├── cookies.phtml
│           │   │           └── private-content.phtml
│           │   └── web/
│           │       ├── js/
│           │       │   └── alpine-plugins/
│           │       └── css/
│           └── ViewModel/                   # Hyvä ViewModels
│               ├── StoreConfig.php
│               ├── CurrentProduct.php
│               ├── Modal.php
│               └── ...
│
├── app/design/frontend/MyCompany/hyva-custom/  # Your custom theme
│   ├── registration.php
│   ├── theme.xml
│   ├── composer.json
│   ├── etc/
│   │   └── view.xml
│   ├── web/
│   │   ├── tailwind/
│   │   │   ├── tailwind.config.js          # Your custom Tailwind config
│   │   │   ├── tailwind-source.css         # Your custom source CSS
│   │   │   ├── package.json
│   │   │   └── components/
│   │   │       └── custom-components.css
│   │   ├── css/
│   │   │   └── source/
│   │   ├── js/
│   │   │   └── custom.js
│   │   └── images/
│   ├── Magento_Theme/
│   │   ├── layout/
│   │   │   └── default.xml                 # Override/extend layouts
│   │   └── templates/
│   │       ├── html/
│   │       │   ├── header.phtml
│   │       │   └── footer.phtml
│   │       └── page/
│   │           └── js/
│   │               └── custom-alpine-plugin.phtml
│   ├── Magento_Catalog/
│   │   ├── layout/
│   │   │   ├── catalog_product_view.xml
│   │   │   └── catalog_category_view.xml
│   │   └── templates/
│   │       └── product/
│   └── ...
│
└── pub/static/                              # Generated static files (after deployment)
    └── frontend/
        ├── Hyva/default/en_US/
        │   ├── css/
        │   │   └── tailwind.css            # Deployed Tailwind CSS
        │   └── js/
        └── MyCompany/hyva-custom/en_US/
            ├── css/
            │   └── tailwind.css
            └── js/
```

---

## Compilation Process

### Complete Build & Deploy Workflow

```
┌──────────────────────────────────────────────────────────────────┐
│ DEVELOPMENT PHASE                                                │
└──────────────────────────────────────────────────────────────────┘

1. Edit Templates
   ├── Add/modify .phtml files
   ├── Use Tailwind classes in templates
   └── Create Alpine.js components

2. Customize Tailwind Config
   ├── Edit web/tailwind/tailwind.config.js
   ├── Modify colors, fonts, spacing
   └── Add custom components/utilities

3. Compile Tailwind CSS
   ├── cd web/tailwind/
   ├── npm run watch (auto-rebuild)
   └── Output: tailwind.css (in same directory)

4. Test Locally
   ├── Clear Magento cache: php bin/magento cache:flush
   ├── Deploy static: php bin/magento setup:static-content:deploy -f
   └── Test in browser

┌──────────────────────────────────────────────────────────────────┐
│ PRODUCTION DEPLOYMENT                                            │
└──────────────────────────────────────────────────────────────────┘

1. Build Production CSS
   ├── cd web/tailwind/
   ├── npm run build (minified)
   └── Commit tailwind.css to repo (optional)

2. Magento Production Setup
   ├── php bin/magento deploy:mode:set production
   ├── php bin/magento setup:di:compile
   ├── php bin/magento setup:static-content:deploy
   └── php bin/magento cache:flush

3. Verify Deployment
   ├── Check pub/static/ directory
   ├── Test frontend
   └── Run Lighthouse audit
```

---

## Customization Workflow

### Scenario: Adding Custom Colors

**1. Edit tailwind.config.js**

```javascript
// app/design/frontend/MyCompany/hyva-custom/web/tailwind/tailwind.config.js

module.exports = {
    theme: {
        extend: {
            colors: {
                brand: {
                    50: '#f0f9ff',
                    500: '#0ea5e9',  // Main brand color
                    900: '#0c4a6e',
                },
            },
        },
    },
}
```

**2. Rebuild CSS**

```bash
cd app/design/frontend/MyCompany/hyva-custom/web/tailwind/
npm run build
```

**3. Use in Templates**

```phtml
<button class="bg-brand-500 text-white px-6 py-3 rounded-md">
    Shop Now
</button>
```

**4. Deploy**

```bash
cd /var/www/html/m246p8
php bin/magento setup:static-content:deploy -f
php bin/magento cache:flush
```

---

### Scenario: Adding Alpine.js Store (Global State)

**1. Create Store Definition**

**File:** `Magento_Theme/templates/page/js/alpine-cart-store.phtml`

```php
<script>
document.addEventListener('alpine:init', () => {
    Alpine.store('cart', {
        items: [],

        addItem(product) {
            this.items.push(product);
        },

        removeItem(productId) {
            this.items = this.items.filter(item => item.id !== productId);
        },

        get itemCount() {
            return this.items.length;
        },

        get total() {
            return this.items.reduce((sum, item) => sum + item.price, 0);
        }
    });
});
</script>
```

**2. Add to Layout**

```xml
<referenceContainer name="before.body.end">
    <block name="alpine-cart-store"
           template="Magento_Theme::page/js/alpine-cart-store.phtml"
           after="script-alpine-js"/>
</referenceContainer>
```

**3. Use in Templates**

```html
<!-- Header cart count -->
<div x-data>
    <span x-text="$store.cart.itemCount"></span> items
</div>

<!-- Add to cart button -->
<button @click="$store.cart.addItem({ id: 1, name: 'Product', price: 29.99 })">
    Add to Cart
</button>
```

---

## Troubleshooting

### Tailwind CSS Not Working

**Issue:** Classes not applying

**Solutions:**
```bash
# 1. Rebuild Tailwind CSS
cd web/tailwind/
npm run build

# 2. Clear Magento cache
cd /var/www/html/m246p8
php bin/magento cache:flush

# 3. Redeploy static content
php bin/magento setup:static-content:deploy -f

# 4. Check content paths in tailwind.config.js
# Make sure your templates are included in the content array

# 5. Hard refresh browser (Ctrl+Shift+R)
```

---

### Alpine.js Not Initializing

**Issue:** `x-data` not working

**Solutions:**
```bash
# 1. Check browser console for errors
# Open DevTools > Console

# 2. Verify Alpine.js is loaded
# In console: console.log(Alpine)

# 3. Check plugin registration order
# Plugins must load BEFORE Alpine.js core

# 4. Verify defer attribute
# <script defer src="alpine.js"></script>

# 5. Check for JavaScript errors above Alpine.js script
```

---

### Custom Plugin Not Loading

**Issue:** Custom Alpine.js plugin not registered

**Checklist:**
1. ✅ Plugin template exists
2. ✅ Added to layout XML
3. ✅ Block name is unique
4. ✅ `before="alpine-plugin-intersect"` (loads before core)
5. ✅ Wrapped in `document.addEventListener('alpine:init', ...)`
6. ✅ Cache cleared
7. ✅ Static content deployed

---

### Performance Issues

**Issue:** Slow page load

**Optimizations:**
```javascript
// 1. Use x-defer for below-fold components
<div x-data="heavyComponent()" x-defer="intersect">

// 2. Minimize Tailwind output
// In tailwind.config.js, ensure content paths are specific

// 3. Use Alpine.js $nextTick for heavy operations
Alpine.effect(() => {
    Alpine.nextTick(() => {
        // Heavy operation
    });
});

// 4. Lazy load images
<img loading="lazy" src="..." />

// 5. Use production mode
php bin/magento deploy:mode:set production
```

---

## Quick Reference

### Essential Commands

```bash
# Tailwind CSS
cd web/tailwind/
npm install              # Install dependencies
npm run build            # Production build
npm run watch            # Development watch mode

# Magento
php bin/magento cache:flush                      # Clear cache
php bin/magento setup:static-content:deploy -f   # Deploy static files
php bin/magento dev:template-hints:enable        # Enable template hints

# Debugging
php bin/magento dev:template-hints:enable        # Show templates
tail -f var/log/system.log                       # Watch logs
```

### Essential Files

| Purpose | File Location |
|---------|---------------|
| Tailwind Config | `web/tailwind/tailwind.config.js` |
| Tailwind Source | `web/tailwind/tailwind-source.css` |
| Tailwind Output | `web/tailwind/tailwind.css` |
| Alpine.js Loader | `Hyva_Theme::page/js/alpinejs.phtml` |
| Alpine.js Plugins | `Hyva_Theme::page/js/plugins/*.phtml` |
| Layout Configuration | `layout/default_hyva.xml` |
| JS Variables | `Hyva_Theme::page/js/variables.phtml` |

---

## Summary

**Tailwind CSS Setup:**
1. Configured in `tailwind.config.js`
2. Source CSS in `tailwind-source.css`
3. Compiled with `npm run build`
4. Output to `tailwind.css`
5. Deployed to `pub/static/`
6. Loaded via layout XML

**Alpine.js Setup:**
1. Defined in `default_hyva.xml`
2. Plugins loaded via child blocks
3. Core loaded from CDN or local
4. Automatically initializes
5. Custom plugins via layout XML

**Key Principles:**
- Tailwind: Utility-first, JIT compilation, minimal output
- Alpine.js: Lightweight, reactive, declarative
- Hyvä: Performance-focused, developer-friendly

---

**Next Steps:**
- Practice building custom themes (see HYVA-LEARNING-PLAN.md Phase 5)
- Experiment with Tailwind customization
- Create custom Alpine.js components
- Build real-world projects

---

**Documentation:**
- Tailwind CSS: https://tailwindcss.com/docs
- Alpine.js: https://alpinejs.dev
- Hyvä Docs: https://docs.hyva.io
